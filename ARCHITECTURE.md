# ARCHITECTURE.md — M1: the grounding check (`ground`)

## Status

**Spec complete, build not started (2026-11-04).** Milestone M1 of the plan in `docs/ROADMAP.md`. Read `DATA-SPEC.md` next.

---

## System overview

`ground` is a command-line checker that reads a career master file (`MASTER.md`) and a tailored resume, and fails when the resume says something the master doesn't support. It is the critic in the larger system: every later milestone (M2's tailor, M4's orchestrator) sends its output through `ground` before a human sees it.

It has two layers, and the difference between them is the lesson of M1:

1. **Deterministic rules (G-1 to G-6).** Cheap, exact, run on every push. They catch missing traces, unknown ids, numbers that aren't in the master, estimates presented as facts, never-use lines, and contact details in the wrong place.
2. **LLM judge (G-8), opt-in.** Catches claims with no number in them that contradict the master ("sole engineer" becoming "led the team"). It costs money and can be wrong, so it never runs in CI and its findings are reported with the model's quoted reasoning.

`ground` can block. It can never fix (ADR-003). It reports findings; a person or the tailor repairs the document.

---

## Component map

| Component | File | Responsibility |
|---|---|---|
| CLI | `src/cli.ts` | Parse arguments, run checks, print a report, set the exit code |
| Master parser | `src/master.ts` | Turn `MASTER.md` into entries (id, text, line tags) and the never-use list |
| Resume parser | `src/resume.ts` | Split a resume into header, sections and bullets; read inline id comments |
| Trace parser | `src/trace.ts` | Read the `## Resume trace` table in a `BRIEF.md` and map ids onto bullets |
| Number extractor | `src/numbers.ts` | Find and normalize numeric tokens in a string |
| Rules | `src/rules/g1.ts` … `src/rules/g6.ts` | One deterministic rule per file, each a pure function returning findings |
| Judge | `src/judge.ts` | G-8: ask a Claude model whether each bullet is supported by its cited entries |
| Report | `src/report.ts` | Render findings as text or JSON |
| Fault runner | `scripts/run-faults.ts` | Run every fixture in `fixtures/faults/` and compare with `expected.json` |

---

## Data flow

```
MASTER.md ──▶ master.ts ──▶ Master { entries, neverUse }
                                   │
resume.md ──▶ resume.ts ──▶ Resume { header, sections[bullets] }
                                   │
BRIEF.md (optional) ──▶ trace.ts ──┤  (adds ids to bullets that have no inline comment)
                                   ▼
                     rules G-1…G-6 (pure functions)  ──▶ Finding[]
                                   │
                   --judge? ──▶ judge.ts (G-8) ──────▶ Finding[]
                                   ▼
                     report.ts ──▶ stdout (text | json), exit code
```

No component writes to any input file. The only filesystem writes in the whole milestone are the fault runner's report to stdout.

---

## Tech stack

Versions resolved 2026-11-04 with `npm view <pkg> version`. Lock exact versions in `package.json`.

| Layer | Choice | Version | Why |
|---|---|---|---|
| Runtime | Node.js | 26.x LTS (26.13.0 current) | Current LTS. The cloud build container has Node 22.23.4; the builder uses 26 if available, else 22 with a note in `CHANGELOG.md`. Nothing in M1 needs 26-only APIs |
| Language | TypeScript | 7.0.2 | Candidate's main language. TS 7 is the native compiler; if `tsc` from 7.0.2 fails on this setup, write a blocker and fall back to the latest 6.x rather than guessing |
| Run TS directly | tsx | 4.23.15 | Runs the CLI and scripts without a build step |
| Tests | Vitest | 5.1.0 | Fast, TS-native |
| LLM | @anthropic-ai/sdk | 1.4.0 | G-8 judge only. Model from `JUDGE_MODEL`, default `claude-sonnet-5-5` |
| Markdown parsing | none (line-based, hand-written) | — | The formats are line-oriented and owned by this project; a markdown AST library adds a dependency and hides the rules. Revisit if a format needs nesting |

No other runtime dependencies. Adding one needs an ADR first (`docs/adr/`).

---

## Key constraints

- **Read-only on inputs.** `ground` never edits a resume, master or brief (ADR-003).
- **Deterministic by default.** Without `--judge`, the same inputs give byte-identical output. No network calls.
- **The real career repo is private.** Real data is only ever read from a path given by the `CAREER_REPO` environment variable, on the user's machine. It is never copied into this repo, never used in CI, and never printed in a committed file.
- **Fixtures are fictional.** Everything in `fixtures/` is invented. Committing it is deliberate and safe; that is why `.gitignore` does not exclude `fixtures/` here.
- **No L4 actions** (ADR-001). Nothing in this repo sends, submits or posts anything.

---

## Explicitly deferred

| Item | Deferred to | Reason |
|---|---|---|
| Cover letters and form answers (prose without bullets) | M1b | Needs sentence-level claim splitting; resumes first |
| Word numbers ("six-week", "three teams") | M1b | Digit tokens cover the seeded faults; word numbers need a lexicon |
| Auto-fixing findings | never | ADR-003 |
| G-7 (numbers matching a ❓ line) | dropped | No realistic fixture; G-3 and G-8 cover it |
| Running in the private career repo's own CI | later, by decision | The career repo would need its own workflow; out of scope here |
| Tailoring, scoring, orchestration | M2, M4 | See `docs/ROADMAP.md` |
