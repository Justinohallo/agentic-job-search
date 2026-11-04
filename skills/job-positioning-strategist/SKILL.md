---
name: job-positioning-strategist
description: Owns the candidate's positioning - the one-sentence pitch per track, which career facts lead, and what leads each surface (resume, LinkedIn, portfolio, outreach) - and keeps POSITIONING.md current. Use when writing or testing a pitch, deciding what to lead with, changing target tracks, reviewing LinkedIn or portfolio emphasis, or when other skills disagree on framing.
---

# Positioning strategist

Decides what the candidate is known for, per track, and makes every other skill say it the same way. In a pile flooded with AI-written applications, verifiable specifics beat adjectives (RESEARCH F-13), and a referrer will paraphrase the pitch, so it has to survive being repeated by someone else (F-6, F-7).

## Hard rules

1. **Built only from MASTER.** Lead entries are A-ids. A fact not in MASTER is sent to the career thread first; this skill never edits MASTER.
2. **One sentence per track**, ≤25 words, with one concrete proof point (a number or named system from a ✅ entry). ⚠️ estimates never lead.
3. **Engineering substance leads.** AI-assisted delivery answers "how did you move fast"; it never opens the pitch (project rule). The exception is a role that is itself about agentic engineering, recorded as a dated decision.
4. **Approved framings are fixed** (MASTER "Approved framings"). Never reword them.
5. **Scope is closed once declared.** A NOT list per track says what the candidate doesn't pitch.

## Reads

`career/MASTER.md` · `career/STORIES.md` · `PIPELINE.md` (which tracks are converting) · latest `job-leads/` (what the market asks for) · `linkedin/PROFILE-DRAFT.md`, `portfolio/PLAN.md` · the account lead's latest review.

## Writes

`career/POSITIONING.md` (owner) · emphasis notes in `linkedin/` and `portfolio/PLAN.md` · `skills/RUNS.md`. Proposes MASTER additions to the career thread.

## Workflow

```
Positioning pass:
- [ ] 1. Pull the last 10 target postings; list the requirements that repeat
- [ ] 2. Map repeats to MASTER entries; note gaps
- [ ] 3. Draft the pitch per track: role, proof point, the kind of team it fits
- [ ] 4. Run the three tests below; rewrite until they pass
- [ ] 5. Set lead tags and lead A-ids per track; set the NOT list
- [ ] 6. Check each surface (resume top third, LinkedIn headline/About, portfolio hero, outreach opener) leads with the same thing
- [ ] 7. Update POSITIONING.md with a dated change line; append to RUNS
```

## The three tests

- **Repeat test:** could an ex-colleague say it to their hiring manager after reading it once? If it needs the resume next to it, it fails.
- **Proof test:** does it contain one thing a skeptic could verify (a shipped system, a deadline met, a dollar figure, a validation to the cent)?
- **Swap test:** if you swap in another senior React engineer's name, is it still true? If yes, it's too generic.

## Output in POSITIONING.md

Per track: lead tags, lead A-ids, one-line pitch, proof point with its A-id, base resume path, NOT list, dated change log line.
