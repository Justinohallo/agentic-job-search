# TASKS.md — M1 build queue

## Status

**Ready for the builder (2026-11-04).** 0 of 12 tasks done. Execute in order; one task, one commit. Read `AGENTS.md` first.

---

## Phase 0: Repository

### Task 00 — Project scaffold
- Create `package.json` (name `agentic-job-search`, `"type": "module"`, private: false), `tsconfig.json` (strict, `noUncheckedIndexedAccess`), and install the exact versions in `ARCHITECTURE.md` › Tech stack.
- Scripts: `test` (vitest run), `ground` (tsx src/cli.ts), `faults` (tsx scripts/run-faults.ts), `typecheck` (tsc --noEmit).
- **Done when:** `npm test` runs (zero tests is fine) and `npm run typecheck` passes.
- **Commit:** `chore(task-00): scaffold TypeScript project`

## Phase 1: Parsers

### Task 01 — Master parser
- Implement `src/master.ts` per `DATA-SPEC.md` §1.
- **Done when:** TC-01 passes.
- **Commit:** `feat(task-01): parse MASTER.md entries and never-use list`

### Task 02 — Resume parser
- Implement `src/resume.ts` per `DATA-SPEC.md` §2.
- **Done when:** TC-02 passes.
- **Commit:** `feat(task-02): parse resume header, sections, bullets and inline ids`

### Task 03 — Trace parser
- Implement `src/trace.ts` per `DATA-SPEC.md` §3, including G-1T findings.
- **Done when:** TC-03 passes.
- **Commit:** `feat(task-03): apply BRIEF resume-trace table to bullets`

### Task 04 — Number extractor
- Implement `src/numbers.ts` per `DATA-SPEC.md` §4.
- **Done when:** TC-04 passes.
- **Commit:** `feat(task-04): extract and normalize number tokens`

## Phase 2: Rules

### Task 05 — Trace and id rules (G-1, G-1T, G-2, G-2W)
- One file per rule under `src/rules/`. Each exports a pure function.
- **Done when:** TC-05 passes for these four.
- **Commit:** `feat(task-05): rules G-1 and G-2`

### Task 06 — Grounding rules (G-3, G-4)
- Corpus = cited entries' lines minus never-use lines. Skip bullets with no resolvable id.
- **Done when:** TC-05 (G-3, G-4) and TC-06 pass.
- **Commit:** `feat(task-06): rules G-3 and G-4`

### Task 07 — Never-use and contact rules (G-5, G-6)
- **Done when:** TC-05 (G-5, G-6) passes.
- **Commit:** `feat(task-07): rules G-5 and G-6`

## Phase 3: CLI, fault suite, CI

### Task 08 — CLI and report
- Implement `src/cli.ts` and `src/report.ts` per `DATA-SPEC.md` §7–8 (no `--judge` yet: the flag exists and exits 2 with `JUDGE_NOT_BUILT` until Task 10).
- **Done when:** TC-07, TC-09 (except the judge case), TC-10 and TC-11 pass.
- **Commit:** `feat(task-08): ground CLI with text and JSON reports`

### Task 09 — Fault runner and CI
- Implement `scripts/run-faults.ts` per `DATA-SPEC.md` §9. Print one line per fault: `F-03 PASS G-3 error` or `F-03 FAIL expected G-3 error, got …`. Exit 1 on any FAIL.
- Add `.github/workflows/ci.yml`: Node 26 (fallback 22), `npm ci`, `npm run typecheck`, `npm test`, `npm run faults`, `npm run ground -- check-all fixtures/candidate`. No secrets, no judge.
- **Done when:** TC-08 and TC-13 pass.
- **Commit:** `feat(task-09): fault suite and CI`

## Phase 4: Judge

### Task 10 — G-8 judge
- Implement `src/judge.ts` per `DATA-SPEC.md` §6 with an injected client; wire `--judge` into the CLI and the fault runner (the runner then also checks each `judge.mustInclude`).
- **Done when:** TC-12 passes and TC-09's judge case passes.
- **Commit:** `feat(task-10): opt-in LLM judge (G-8)`

### Task 11 — Live judge run and real-data run (manual, with the user)
- Ask the user to run TC-14 with their key and TC-15 on their machine. Record TC-14's result and cost in `CHANGELOG.md`. Record only "TC-15: ran, N files, no parse errors" for TC-15, never its findings.
- **Done when:** both recorded.
- **Commit:** `docs(task-11): record live judge and real-data runs`

---

## Build notes

- Tests live in `tests/`, one file per source module, named `<module>.test.ts`.
- Fixtures are read-only. Tests that need variants build them as inline strings.
- If Node 26 isn't available in the build environment, use 22 and note it in `CHANGELOG.md`; don't install a Node manager.
- Every rule message quotes the offending token or phrase so a person can fix it without rerunning anything.
