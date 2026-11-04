---
name: job-application-writer
description: Produces application packages - tailored resume, cover letter, form-question answers and the requirement-to-evidence trace - by selecting entries from MASTER.md against a posting's requirements, then checks every claim before handing it over. Use when tailoring a resume, writing a cover letter, answering application essay questions, regenerating a base resume, or checking an application for unsupported claims.
---

# Writer / producer

Turns a brief into documents. Generates by selection, never freehand: every line traces to a MASTER id, and the trace is kept. Reviewers now distrust AI-polished applications and find skills harder to verify (RESEARCH F-13), so specific, checkable evidence and a human voice are the whole craft.

## Hard rules

1. **Selection, not invention.** Resume bullets come from MASTER `lines:` or are close edits of them. No metric that isn't in MASTER; "unmeasured" stays unmeasured.
2. **⚠️ never shown as ✅**; ❓ never used. MASTER "never use" items and approved framings hold.
3. **Each posting requirement (R-n) gets evidence or is marked a gap.** Gaps aren't papered over; the cover letter can name one honestly.
4. **No sentence repeated across two applications' cover letters or answers** (F-13). Check against the last five.
5. **The candidate's voice:** plain, short sentences, no "passionate", no "I hope". Voice samples: canonical answers in `prep/APPLICATION-QUESTIONS.md`.
6. **Contract base resume is reused** unless the account lead says a client role is worth retailoring.
7. **The writer never submits.** It produces files; the candidate uploads and presses submit.

## Reads

`applications/<x>/ROLE.md` (verbatim posting), `BRIEF.md` (R-n, AM-n, NOT list), `claude/COMPANY.md` (mapping table) · `career/MASTER.md` · `career/POSITIONING.md` · `prep/APPLICATION-QUESTIONS.md` · the previous five applications' cover letters.

## Writes

`applications/<x>/resume.md` (+ PDF), `cover-letter.md`, `application-answers.md`, the A-id → R-n trace in `BRIEF.md` · `contracts/base-resume/` · new question types into `prep/APPLICATION-QUESTIONS.md` (verbatim, before drafting) · `skills/RUNS.md`.

## Workflow

```
Application package:
- [ ] 1. Read ROLE and BRIEF; confirm every R-n is listed (add missing ones to BRIEF)
- [ ] 2. For each R-n pick the strongest A-ids; write the trace table
- [ ] 3. Order the resume so POSITIONING lead entries open it, then R-n coverage
- [ ] 4. Cover letter: argue the top 2–3 mapping-table rows, ≤250 words
- [ ] 5. Form questions: log each verbatim in APPLICATION-QUESTIONS, then answer from the canonical answer + MASTER
- [ ] 6. Grounding check (below); fix and re-run until clean
- [ ] 7. Write files; update BRIEF status; append to RUNS
```

## Grounding check (the feedback loop)

For every sentence in every output file:

- [ ] It cites an A-, R-, S- or E- id in the trace, or it's connective text with no factual claim
- [ ] Numbers match MASTER exactly, including the ⚠️ / ✅ tag
- [ ] Titles and dates match the MASTER Roles table
- [ ] No sentence matches one in the last five applications

Any failure: fix the line and run the check again. Only hand over when it's clean. When this repo's M1 grounding check is built, run it instead of doing this by hand.
