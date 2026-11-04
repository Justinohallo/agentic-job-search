# agentic-job-search

An agentic job-search system, built spec-first as a learning project. Everything runs on a **fictional candidate** (Sam Rivera, in `fixtures/`), so it's safe to demo; the same code points at a real, private career repo through `CAREER_REPO` on the owner's machine.

## Why it exists

A job search is a small client engagement: a posting is a brief, a resume is a deliverable, and an interview is a review. This repo builds the agents for that, one milestone at a time. Each milestone teaches one agentic-engineering concept and ships something you can watch work in under three minutes. See `docs/ROADMAP.md`.

The rule running through all of it: **agents draft and check; a person sends.** Nothing here submits, emails or posts anything (`docs/adr/ADR-001-no-external-actions.md`).

## Where it is now

**M0 (spec) is done; M1 is next.** M1 is `ground`, a checker that fails a resume when it claims something the candidate's career master doesn't support: an invented metric, an estimate presented as fact, a line flagged never-use, contact details in the wrong place. Ten seeded faults in `fixtures/faults/` define what it must catch, and two of them are deliberately invisible to rules. Those two need the opt-in LLM judge, and showing that gap is the point.

## How it's built

| Seat | File |
|---|---|
| Architect (writes the spec, never code) | `ARCHITECTURE.md`, `DATA-SPEC.md`, `VALIDATION.md`, `TASKS.md`, `AGENTS.md`, `docs/adr/` |
| Builder (writes code, never the spec) | `.claude/agents/builder.md` |
| QA (verifies, never fixes) | `.claude/agents/qa.md` |

To start the build in Claude Code: open this repo and ask for the `builder` agent to execute `TASKS.md`. When it logs Task 10, run the `qa` agent.

## Layout

```
AGENTS.md          entry point: who owns what, the reading order
ARCHITECTURE.md    M1 components and locked stack
DATA-SPEC.md       formats, rules G-1…G-8, outputs, exit codes
VALIDATION.md      the baseline and test cases
TASKS.md           ordered build queue
docs/adr/          the three decisions everything else rests on
docs/ROADMAP.md    M0–M5
fixtures/          fictional candidate, two clean applications, ten seeded faults
```
