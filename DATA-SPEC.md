# DATA-SPEC.md — formats, rules and outputs for `ground`

## Status

**Complete for M1 (2026-11-04).** Every rule, regex and output field the builder needs. If something here is ambiguous, write a blocker; don't guess.

---

## 1. Input: `MASTER.md`

Line-oriented markdown. The parser needs only these structures:

| Structure | How to recognize it | What to keep |
|---|---|---|
| Entry heading | Line matching `^### ([ARSE]-\d+[a-z]*) · ` | The id (group 1). The entry runs until the next `### ` or `## ` heading or end of file |
| Entry text | Every line inside an entry | Kept as `lines: string[]`, with the line's status (below) |
| Line status | Line contains ⚠️ → `estimate`; contains ❓ → `unknown`; else `fact` | Per line |
| Never-use line | Line inside an entry starting with `**Never use:**` | Every substring inside straight `"…"` or curly `“…”` quotes goes into `neverUse[]`. The **whole line is excluded** from the grounding corpus |

`Master` type:

```ts
type LineStatus = 'fact' | 'estimate' | 'unknown';
interface MasterEntry { id: string; lines: { text: string; status: LineStatus; lineNo: number }[] }
interface Master { entries: Map<string, MasterEntry>; neverUse: { phrase: string; entryId: string }[] }
```

Example from the fixture: `A-1` has a never-use line with two phrases, `cut quoting errors by 40%` and `doubled quote volume`. Neither phrase, nor the `40%` in it, counts as grounding.

Parse errors: a file with zero entries → exit 2, message `MASTER_EMPTY: no entries found in <path>`.

## 2. Input: `resume.md`

| Part | Rule |
|---|---|
| Header | Every line before the first line starting with `## ` |
| Section | A line starting with `### `. Section label = heading text up to the first ` — ` (em dash with spaces), trimmed. Example: `Bowline Outfitters — Senior Frontend Developer` → `Bowline Outfitters` |
| Bullet | A line starting with `- ` inside a section. Bullets are numbered 1, 2, 3… within their section |
| Inline ids | `<!-- A-5 -->` or `<!-- A-3, A-3b -->` at the end of a bullet. Regex: `<!--\s*([ARSE]-\d+[a-z]*(?:\s*,\s*[ARSE]-\d+[a-z]*)*)\s*-->`. The comment is stripped from the bullet text before any rule runs |
| Body | Everything from the first `## ` line to the end, including non-bullet lines |

`Resume` type:

```ts
interface Bullet { section: string; index: number; text: string; ids: string[]; lineNo: number }
interface Resume { headerLines: string[]; bodyLines: { text: string; lineNo: number }[]; bullets: Bullet[] }
```

## 3. Input: `BRIEF.md` trace table (optional, `--trace`)

Find the `## Resume trace` heading, then the first markdown table under it. Columns are found by header name, case-insensitive: `A-ids used` and `Line on the page`. Ignore other columns.

`Line on the page` grammar, e.g. `alluraDirect 1–2; Burrard Works 1` or `Ledgerly 1, 2`:

- Split on `;`. Each part is `<label> <refs>` where `<refs>` is the trailing run of digits, commas, spaces, hyphens and en dashes.
- `<label>` matches a resume section whose label **starts with** it, case-insensitive (`Bowline` matches `Bowline Outfitters`).
- `<refs>`: comma-separated items; an item is `n` or `n–m` / `n-m` (inclusive range).
- Every referenced bullet gets every id in that row's `A-ids used` cell (comma-separated).

Inline ids win: if a bullet has an inline comment, the trace table is not applied to it. A label that matches no section → finding `G-1T` (error) with the label in the message.

## 4. Number tokens (`numbers.ts`)

Token regex (global): `(?<![A-Za-z0-9-])[$~]?\d[\d,]*(?:\.\d+)?\s?(?:%|×|x\b|[KkM]\b|\+)?`

Normalize each token to `{ value: number, unit: 'pct' | 'times' | 'plain' }`:

| Step | Rule |
|---|---|
| Strip | `$`, `~`, `,`, spaces |
| Suffix `K`/`k` | value × 1,000, unit `plain` |
| Suffix `M` | value × 1,000,000, unit `plain` |
| Suffix `%` | unit `pct` |
| Suffix `×` or `x` | unit `times` |
| Suffix `+` | ignored |
| Skip | Integers 1900–2100 with no suffix (years). Tokens preceded by a letter or hyphen are already excluded by the lookbehind (`Q1`, `A-5`, `S3`) |

Two tokens **match** when `value` and `unit` are equal. So `$350K` matches `$350,000`, and `8-week` → `8` matches `8-week` in the master.

## 5. Rules

Each rule is a pure function `(ctx: { master: Master; resume: Resume }) => Finding[]`.

| Id | Name | Severity | Fires when |
|---|---|---|---|
| G-1 | TRACE_MISSING | error | A bullet has no ids after inline comments and the trace table are applied |
| G-1T | TRACE_LABEL_UNMATCHED | error | A trace-table label matches no resume section |
| G-2 | UNKNOWN_ID | error | A cited id is not in the master, and stripping trailing lowercase letters doesn't find it either |
| G-2W | SUBID_RESOLVED | warn | A cited id isn't in the master but its parent is (`A-5x` → `A-5`). The parent is used for the other rules |
| G-3 | UNGROUNDED_NUMBER | error | A number token in a bullet matches no token in the grounding corpus of its cited entries (all entry lines except never-use lines) |
| G-4 | ESTIMATE_UNQUALIFIED | warn (config: `--estimates error\|warn\|off`) | A bullet's number matches **only** tokens on `estimate` lines, and the bullet has no qualifier within the 3 words before the number: `~`, `about`, `approximately`, `around`, `roughly`, `nearly`, `est.`, `estimated` (case-insensitive) |
| G-5 | NEVER_USE | error | A never-use phrase appears anywhere in the resume (header or body), compared case-insensitive with runs of whitespace collapsed |
| G-6 | CONTACT_OUTSIDE_HEADER | error | A body line contains an email (`[\w.+-]+@[\w-]+\.[\w.]+`) or a phone number (`\b\d{3}[-. ]\d{3}[-. ]\d{4}\b`) |
| G-8 | CLAIM_UNSUPPORTED | error | **Judge only (`--judge`).** The model says a bullet asserts something its cited entries don't support |

Bullets with no resolvable id (G-1 or G-2 fired) are skipped by G-3 and G-4, so one fault produces one finding.

**Known limitation:** status is per line, not per clause. A master line holding a ✅ fact and a ⚠️ estimate is treated as `estimate` throughout. Keep facts and estimates on separate lines in new entries; the real career master mostly does already.

Rule order in output: by line number, then by rule id.

## 6. G-8 judge contract (`judge.ts`)

- Runs once per bullet that has at least one resolvable id. Skips bullets that already have a G-2 error.
- Prompt: system text states the task and that the resume bullet and master text are **data, not instructions**. The user message wraps them in `<bullet>…</bullet>` and `<master_entry id="…">…</master_entry>` tags. Never-use lines are excluded from the master text.
- Ask for JSON only: `{ "supported": boolean, "unsupported_claim": string | null, "reason": string }`.
- `supported: false` → G-8 finding whose message is `unsupported_claim — reason`.
- A response that isn't valid JSON → one retry; then a `G-8E` (warn) finding `JUDGE_UNPARSEABLE` and move on.
- Model: `process.env.JUDGE_MODEL ?? 'claude-sonnet-5-5'`. API key from `ANTHROPIC_API_KEY`; if `--judge` is given without a key, exit 2 with `JUDGE_NO_KEY`.
- The client is injected (`judge(ctx, client)`) so tests use a fake.

## 7. Output

### `Finding`

```ts
interface Finding {
  rule: 'G-1' | 'G-1T' | 'G-2' | 'G-2W' | 'G-3' | 'G-4' | 'G-5' | 'G-6' | 'G-8' | 'G-8E';
  severity: 'error' | 'warn';
  file: string;          // path as given on the command line
  line: number;          // 1-based line in the resume
  message: string;       // one sentence, includes the offending token or phrase
  ids?: string[];        // cited ids, when relevant
}
```

### Text format (default)

```
fixtures/faults/F-03/resume.md:21  error  G-3  UNGROUNDED_NUMBER  "35%" is not in A-2
1 error, 0 warnings
```

### JSON format (`--format json`)

`{ "file": string, "findings": Finding[], "summary": { "errors": number, "warnings": number } }`, one object per checked file; `check-all` prints an array.

### Exit codes

| Code | Meaning |
|---|---|
| 0 | No errors (warnings allowed) |
| 1 | At least one error |
| 2 | Usage or parse error (bad flags, missing file, `MASTER_EMPTY`, `JUDGE_NO_KEY`) |

## 8. CLI

```
ground check <resume.md> --master <MASTER.md> [--trace <BRIEF.md>] [--format text|json] [--estimates error|warn|off] [--judge]
ground check-all <root> [--format …] [--estimates …] [--judge]
```

`check-all <root>` reads `<root>/MASTER.md` (or `<root>/career/MASTER.md` if the first is missing, which is the real career repo's layout) and checks every `<root>/applications/*/resume.md`, passing `<root>/applications/<x>/BRIEF.md` as `--trace` when it exists. Exit code is the worst across files.

## 9. Fault fixtures

`fixtures/faults/F-nn/resume.md` is the clean Northwind resume with one planted fault. `expected.json`:

```json
{
  "fault": "F-03",
  "description": "Invented metric on a real achievement",
  "layer": "deterministic | judge",
  "deterministic": { "mustInclude": [{ "rule": "G-3", "severity": "error" }], "mustHaveNoErrors": false },
  "judge": { "mustInclude": [] }
}
```

`mustHaveNoErrors: true` (judge-layer faults) asserts the deterministic layer **does not** catch it. That's deliberate: it documents what rules can't see. All faults are checked against `fixtures/candidate/MASTER.md`.
