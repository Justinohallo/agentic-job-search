# skills/ — the job-search firm as Agent Skills

Six role skills that staff a job search the way a placement firm would. Each is an Anthropic Agent Skill: a folder with `SKILL.md` (frontmatter `name` + `description`, then the workflow), `references/` loaded only when needed, and `evals/evals.json` with three scenarios. The research each rule rests on is in `RESEARCH.md` (findings F-n, with sources and confidence).

Everything here runs on the fictional candidate, Sam Rivera (`fixtures/candidate/`). No real person's data belongs in this folder.

## The org chart

| Skill | Role | One job | Autonomy |
|---|---|---|---|
| `job-account-lead` | Account lead | Weekly review: find the binding constraint in the funnel, decide drop / push / start, assign the week | L2 |
| `job-market-sourcer` | Market researcher / sourcer | Ranked leads, hard-requirement checks, the people who hire at each target, agency list | L3 internal only |
| `job-positioning-strategist` | Positioning strategist | One-sentence pitch per track; what leads each surface | L2 |
| `job-application-writer` | Writer / producer | Resume, cover letter, form answers by selection from MASTER, with a grounding check | L2 |
| `job-business-developer` | Business developer | Weekly batch of warm outreach, referral asks, agency registrations, follow-ups | L2, drafts only |
| `job-interview-coach` | Interview coach | Format check, timed mocks on a structured rubric, debriefs, offer negotiation | L2 |

No skill acts externally. That's [ADR-001](../docs/adr/ADR-001-no-external-actions.md): agents draft, a person sends.

The pattern is orchestrator-workers with human checkpoints (Anthropic, "Building effective agents"): the account lead assigns, the other five do the work, and every edge that leaves the system passes through the candidate.

## Data layout the skills expect

The skills use the paths of a candidate data repo (the layout `CAREER_REPO` points at). The fixture holds a subset:

| Skills refer to | Holds | In the fixture |
|---|---|---|
| `career/MASTER.md` | Every career fact, atomic and tagged ✅ ⚠️ ❓ | `fixtures/candidate/MASTER.md` |
| `applications/<x>/` (ROLE, BRIEF, COMPANY, DEFENSE, resume, cover letter) | One application | `fixtures/candidate/applications/<x>/` |
| `PIPELINE.md` | One row per opportunity | not yet |
| `career/POSITIONING.md`, `career/STORIES.md` | Pitch per track; story bank | not yet |
| `contracts/` | Agencies and base resume | not yet |
| `outreach/CONTACTS.md`, `outreach/<date>-batch.md` | People and weekly drafts | not yet |
| `reviews/<yyyy>-W<nn>.md` | Weekly account review | not yet |
| `prep/` | Cross-application interview prep | not yet |
| `skills/RUNS.md` | One-line trace per skill run (date, skill, inputs, outputs, decision, outcome) | not yet |

Fixture files the skills need but don't exist yet are for the architect to add; this folder doesn't edit `fixtures/`.

## Read/write map

| Document | account-lead | sourcer | positioning | writer | business-dev | coach |
|---|---|---|---|---|---|---|
| PIPELINE | **W** | R | R | R | P | P |
| MASTER | R | R | R, P | R | R | R |
| POSITIONING | R | R | **W** | R | R | R |
| STORIES | | | R | R | | **W** |
| applications/ROLE, COMPANY | | **W** | R | R | R | R |
| applications/BRIEF | R, W | | R | **W** | R | **W** (debrief) |
| applications/DEFENSE | | | | | | **W** |
| resume, cover letter, answers | | | | **W** | | |
| contracts/ | R | **W** | | W (base resume) | W (relationships) | |
| job-leads/ | R | **W** | R | | R | |
| outreach/ | R | W (targets) | | | **W** | |
| reviews/ | **W** | | | | | |
| prep/ | | | | R | | **W** |
| skills/RUNS.md | W | W | W | W | W | W |

R reads · W writes · **W** owns · P proposes a change for a person to apply.

## Evals

Each `evals/evals.json` follows the format in Anthropic's skill-authoring guide (`skills`, `query`, `files`, `expected_behavior`). There's no built-in runner. Run each with a fresh Claude that has only that skill loaded, against the fixture, and record the result. Turning these into an automated suite is a natural later milestone, next to M1's seeded faults.

## Installing

- Claude Code: copy a skill folder into `.claude/skills/` (project) or `~/.claude/skills/` (personal).
- claude.ai: zip the folder itself (so the zip contains `<skill>/SKILL.md`), then Settings → Capabilities → Skills → Upload.
