# AGENTS.md — who does what in this repo

## Status

**M1 spec complete, build not started (2026-11-04).** Entry point for every agent. Read this, then the files in "Reading order".

---

## Team roster

| Seat | Who | Owns |
|---|---|---|
| Client | Justin (human) | Decisions, sign-off, anything outward-facing, the private career repo |
| Architect | Claude, in a planning session | Every spec file: `ARCHITECTURE.md`, `DATA-SPEC.md`, `VALIDATION.md`, `TASKS.md`, `AGENTS.md`, `docs/`, `fixtures/` |
| Builder | `.claude/agents/builder.md` (Claude Code subagent) | `src/`, `tests/`, `scripts/`, `package.json`, `tsconfig.json`, `.github/workflows/` |
| QA | `.claude/agents/qa.md` (Claude Code subagent) | Sign-off entries in `CHANGELOG.md`; failure reports in `BLOCKERS.md` |

The architect and the builder are separate seats. The builder never edits a spec file or a fixture; the architect never writes code.

## File ownership map

| Path | Owner | Others may |
|---|---|---|
| `*.md` at root except `CHANGELOG.md`, `BLOCKERS.md` | Architect | Read |
| `docs/**` | Architect | Read |
| `fixtures/**` | Architect | Read only. Never modify to make a test pass |
| `src/**`, `tests/**`, `scripts/**` | Builder | QA reads and runs |
| `package.json`, `package-lock.json`, `tsconfig.json`, `.github/**` | Builder | Read |
| `CHANGELOG.md` | Builder (task entries), QA (sign-off) | Append only |
| `BLOCKERS.md` | Anyone | Append only; the architect resolves |

## Reading order

1. `AGENTS.md` (this file)
2. `ARCHITECTURE.md`
3. `DATA-SPEC.md`
4. `VALIDATION.md`
5. `TASKS.md`
6. `docs/adr/` (three short files)

## Blocked protocol

When the spec is ambiguous, missing or contradicts itself, stop that task and append to `BLOCKERS.md`:

```
## BLOCKER — <seat> — <YYYY-MM-DD>
**Task:** <Task NN>
**What's unclear:** <one paragraph>
**Options seen:** <A / B>
**Waiting on:** Architect
```

Then continue with any task that doesn't depend on it.

## Forbidden actions

- Editing a fixture or an `expected.json` to make a test pass.
- Adding a runtime dependency without an ADR in `docs/adr/`.
- Any network call outside `src/judge.ts`.
- Reading, copying or committing anything from the private career repo. Real data is read only at runtime from `CAREER_REPO`, on the user's machine.
- Sending, submitting or posting anything anywhere (ADR-001).
- Letting `ground` modify any input file (ADR-003).

## Completion signal

After each task's commit, the builder appends to `CHANGELOG.md`:

```
## <YYYY-MM-DD> — Task NN complete — Builder
**Output:** <files>
**Commit:** <short hash>
**Validation:** <which TCs pass>
**Notes:** <anything QA or the next task needs>
```
