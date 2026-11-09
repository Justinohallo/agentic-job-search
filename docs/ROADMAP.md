# ROADMAP.md — milestones M0 to M5

**Status (2026-11-04):** M0 done (this spec). M1 ready to build.

The plan and the reasoning behind it, including the case against building any of this, live in the private career repo at `proposals/AGENTIC-JOB-SEARCH.md` §11. This is the public summary.

| # | Ships | Concept | Demo | Status |
|---|---|---|---|---|
| M0 | The spec for M1 (`ARCHITECTURE.md`, `DATA-SPEC.md`, `VALIDATION.md`, `TASKS.md`, `AGENTS.md`, ADRs, subagents) | Spec-first agentic development; acceptance criteria as tests; separate architect and builder seats | Walk from the problem to M1's acceptance criteria | Done |
| M1 | `ground`: the grounding check, CLI + CI | Deterministic checks versus LLM-as-judge; evals with seeded faults; a critic that blocks but never fixes | Plant an invented metric, push, watch CI fail | Next |
| M2 | Fit scorer + tailor: posting in, scored brief and traced resume out | Tool use; retrieval by id; untrusted input; M1 as the gate | Paste a posting, get a resume that passes M1 | Not started |
| M3 | Practice loop: timed mock round, rubric grading, debrief | Multi-turn agents; rubric evals calibrated to real outcomes; memory write-back | A 10-minute mock with a graded debrief | Not started |
| M4 | Orchestrator: pipeline stage changes trigger agents; output lands in a daily digest | State machines; subagents; human-in-the-loop gates | Add a lead; next morning a checked package waits for a yes | Not started |
| M5 | Traces and an eval report | Observability; measuring against the outcome, not activity | One page: what it did, cost, and moved | Not started |

**Rules for the build:** applications never wait on the system; a milestone over twice its estimate stops for a retro; a milestone isn't started until the previous one has been used for real.
