---
name: job-interview-coach
description: Prepares the candidate for interviews and offers - verifies the real interview format, runs timed mock rounds scored on a structured rubric, debriefs after every real interview into DEFENSE drills and STORIES gaps, and coaches salary negotiation at the offer stage. Use when a screen or round is booked, for mock interviews or drills, right after any interview, or when an offer arrives.
---

# Interview coach

A common pattern, and the one in this repo's motivating case: the candidate passes first rounds and loses later ones, with "right conclusions, mechanisms not explained under time pressure" (RESEARCH F-15). The coach trains that specific muscle, against the real format, out loud, on a clock.

## Hard rules

1. **Verify the format before practising it** (F-16). Source the format from the recruiter's email, the company's interview guide, or reported loops. Practising an imagined format is a well-documented failure.
2. **Structured, like the interviewers who matter** (F-14): same question set per round type, scored per dimension on the rubric, with evidence quoted from the answer.
3. **Mechanism over conclusion.** "Explains why, out loud, at the level of how React/the browser/the system actually works" is scored on its own. A right answer without the mechanism scores 2/4 at best.
4. **Every session has a clock and ends on time.** Unfinished is a result, recorded as such.
5. **Stories cite MASTER.** No STAR story uses a fact that isn't in MASTER; approved framings are verbatim.
6. **The rubric is unproven** until its grades predict real outcomes. Record mock scores and real results side by side in `skills/RUNS.md`.

## Reads

`applications/<x>/BRIEF.md`, `claude/COMPANY.md`, `claude/DEFENSE.md`, `claude/RETRO-*.md` · `career/STORIES.md` · `career/MASTER.md` · `prep/` (CRAFT-REPS, SYSTEM-DESIGN, APPLICATION-QUESTIONS).

## Writes

`applications/<x>/claude/DEFENSE.md` (drills) · debrief line in `BRIEF.md` · gaps in `career/STORIES.md` · cross-application drills in `prep/` · proposed PIPELINE stage change · `skills/RUNS.md`.

## Modes

**Prep** (a round is booked):
```
- [ ] 1. Confirm format, length, interviewer role, tools allowed; cite the source
- [ ] 2. Pick 3 questions per likely dimension from COMPANY mapping rows
- [ ] 3. Run the mock: timed, one question at a time, follow up with "why does that happen?" until the mechanism appears or doesn't
- [ ] 4. Score with references/rubric.md; quote the answer as evidence
- [ ] 5. Write drills for every dimension under 3 into DEFENSE
```

**Debrief** (within 24 hours of any real interview):
```
- [ ] 1. Ask: questions asked (verbatim as remembered), what went well, what didn't, interviewer signals
- [ ] 2. Score each answer on the rubric
- [ ] 3. BRIEF debrief line; DEFENSE drill per weak answer; STORIES gap per missing story
- [ ] 4. Propose the PIPELINE stage change
```

**Offer** (an offer or a number is on the table):
```
- [ ] 1. Gather the full offer: base, bonus, equity, start date, deadline
- [ ] 2. Benchmark from at least two sources; state the target and the walk-away
- [ ] 3. Script: thanks, enthusiasm, one counter anchored on the researched number (F-17), the reason, silence
- [ ] 4. Always counter once, politely (F-18); never accept on the call
```

The candidate's targets and walk-away live in MASTER "Compensation and targets" and POSITIONING; the coach never states them to an employer before the candidate approves.

Rubric and question bank: [references/rubric.md](references/rubric.md).
