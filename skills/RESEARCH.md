# RESEARCH.md — what each skill is built on

Research pass 2026-11-04. Most hiring and outreach numbers come from vendor surveys and blogs that repeat each other, so each finding carries a confidence note. A skill rule cites the finding it rests on (F-n). Where research was thin, the rule is marked as convention.

## Contents

- Agent design: how the skills are built
- Business developer: referrals, ties, outreach
- Market sourcer: where roles actually come from
- Writer / producer: the AI-application flood
- Interview coach: structured interviews and practice
- Account lead / negotiation: first offers
- Positioning: what the research does and doesn't say

## Agent design

| # | Finding | Source | Confidence | Used in |
|---|---|---|---|---|
| F-1 | A skill is a folder with `SKILL.md`. Frontmatter `name` ≤64 chars, lowercase/digits/hyphens, no "anthropic"/"claude"; `description` ≤1,024 chars, third person, says what and when. Body under 500 lines; references one level deep; reference files over 100 lines get a table of contents | [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) | High (primary) | Every skill's format |
| F-2 | Only name and description are always loaded; the body loads on trigger; references load when needed (progressive disclosure). "Claude is already very smart": add only context it lacks | same | High | Kept bodies short; method detail in `references/` |
| F-3 | Build evaluations before extensive docs: three scenarios per skill, baseline without the skill, then iterate with one Claude writing and a fresh one testing | same | High | `evals/evals.json` per skill |
| F-4 | Checklists for multistep workflows; validator → fix → repeat feedback loops for quality-critical output | same | High | Every workflow is a copyable checklist; writer runs the grounding check |
| F-5 | Start with the simplest solution. Orchestrator-workers when subtasks aren't predictable; evaluator-optimizer when criteria are clear; agents "pause for human feedback at checkpoints" | [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) | High (primary) | Account lead as orchestrator; coach as evaluator; L2 gates |

## Business developer

| # | Finding | Source | Confidence | Used in |
|---|---|---|---|---|
| F-6 | Referrals convert to hire far above job boards: about 30% vs 7% (Jobvite / Aptitude 2024, most-cited); CareerPlug 2024 (60K+ businesses) reports referred candidates about 10x more likely to be placed. Referrals are a small share of applicants and a large share of hires | [Pinpoint summary](https://marketing-assets.pinpointhq.com/insights/referrals-are-7x-more-likely-to-be-hired-than-job-board-candidates), [Interview Guys](https://blog.theinterviewguys.com/referred-candidates-get-hired-4x-more-often-so-stop-asking-for-a/), [noon.ai roundup](https://www.noon.ai/blog/articles/360-employee-referral-statistics-2026) | Medium: direction consistent everywhere, exact ratios vary 4x to 11x | The whole skill; referral asks outrank cold asks |
| F-7 | Moderately weak ties produce the most job mobility (inverted U), from randomized experiments on ~20M LinkedIn users, 2015–2019. Weak ties help most in digital industries | Rajkumar et al., "A causal test of the strength of weak ties", *Science* 2022 ([PubMed](https://pubmed.ncbi.nlm.nih.gov/36107999/), [MIT News](https://news.mit.edu/2022/weak-ties-linkedin-employment-0915)) | High (peer-reviewed, causal) | Tie-strength scoring: target ex-colleagues you haven't spoken to in a while, not only close friends |
| F-8 | LinkedIn messages get far more replies than cold email (Pin 2026 benchmark, 4M+ recruiter messages: ~17% vs ~5%). Shorter wins; 50–120 words is the commonly cited band. A personalised note roughly doubles connection-request replies. Generic "Dear Hiring Manager" gets near zero | [Pin benchmark](https://www.pin.com/blog/recruiting-outreach-benchmark-report/), [Aurium](https://research.aurium.ai/ai-driven-messaging/messaging-variables-booking-rates) | Medium-low: recruiter-to-candidate data, vendor sources | Message rules: ≤120 words, one ask, a specific shared detail |
| F-9 | Hiring managers are less active on LinkedIn than recruiters ("While recruiters live on LinkedIn, managers generally do not", Aline Lerner, interviewing.io) | quoted in [Interview Guys](https://blog.theinterviewguys.com/?p=4593) | Medium | Channel choice: email or a mutual intro for managers, LinkedIn for recruiters |
| F-10 | One follow-up after about a week, then stop | Convention (no study found) | Low | Follow-up rule |

## Market sourcer

| # | Finding | Source | Confidence | Used in |
|---|---|---|---|---|
| F-11 | The "70–80% of jobs are never posted" figure has no traceable primary source. What is supported: referrals and networks fill a disproportionate share (F-6) | Search 2026-11-04 found only blogs | Claim rejected | The sourcer doesn't cite it; it maps people at target companies instead of hunting "hidden" postings |
| F-12 | Regional agencies often fill contracts from their databases before posting, so registration beats scanning | First-hand market read in the motivating search (2026-11-04) | Medium (one pass) | Agency track in sourcer and business developer |

## Writer / producer

| # | Finding | Source | Confidence | Used in |
|---|---|---|---|---|
| F-13 | AI-written applications have raised volume and slowed hiring: about two-thirds of HR leaders say reviewing them slowed hiring; 65% of hiring managers say AI-enhanced resumes make skills harder to verify; employers shift weight to referrals, skills tests and in-person rounds | Robert Half survey via [People Matters](https://www.peoplematters.in/news/ai-and-emerging-tech/67percent-of-hr-leaders-say-ai-generated-resumes-are-slowing-the-hiring-process-48742), [Staffing Industry Analysts](https://www.staffingindustry.com/news/global-daily-news/ai-generated-cvs-flood-employers-and-slow-hiring-in-the-uk), [Resume Now](https://www.resume-now.com/job-resources/careers/ai-applicant-report) | Medium (surveys, self-report) | Writer rules: evidence over adjectives, no sentence reused across applications, every claim traceable to MASTER |

## Interview coach

| # | Finding | Source | Confidence | Used in |
|---|---|---|---|---|
| F-14 | Structured interviews are the top-ranked selection method after correcting earlier range-restriction errors (mean validity ≈ .42); unstructured ≈ .19. So interviewers who matter run structured, rubric-scored loops | Sackett et al. 2022, *J. Applied Psychology* (doi 10.1037/apl0000994), via [SIOP](https://www.siop.org/tip-article/is-cognitive-ability-the-best-predictor-of-job-performance), [TestGorilla](https://testgorilla.com/blog/science-hiring-tools-validity-revisited-two) | High for the ranking; check exact figures in the paper | Coach mirrors structured rubrics: same questions, scored per dimension, not a vibe |
| F-15 | In the motivating search, two lost later rounds failed the same way: right conclusions, mechanisms not explained under time pressure | First-hand retros (private) | High for that candidate | Coach grades "explains the mechanism out loud" as its own dimension |
| F-16 | Practice should be calibrated against the real format before grading against it (rigour on an unverified premise produces a precise wrong answer) | First-hand retro (private) | High | Coach step 1: verify the interview format |

No good evidence turned up on mock-interview training effects. The coach treats its rubric as unproven until its grades predict real outcomes.

## Account lead / negotiation

| # | Finding | Source | Confidence | Used in |
|---|---|---|---|---|
| F-17 | Making the first offer is an advantage (meta-analysis, 90 studies, g = 0.42), but over-ambitious first offers cause more impasses and lower the other side's satisfaction | 2025 preregistered first-offer meta-analysis, via [Leuphana](https://fis.leuphana.de/en/publications/the-power-and-peril-of-first-offers-in-negotiations-a-conceptual-/); Galinsky & Mussweiler 2001 | Medium-high | Coach at offer stage: anchor with a researched number, not a fantasy one |
| F-18 | Candidates who don't negotiate leave the gap to compound over a career | Babcock & Laschever, via [NPR](https://www.npr.org/2007/08/06/12529237/for-women-pay-negotiations-can-bear-social-cost) | Medium | Coach default: always counter once, politely |

## Positioning

Research on positioning for job seekers is mostly opinion. The strategist relies on two things with evidence behind them: F-13 (verifiable specifics beat adjectives in an AI-flooded pile) and F-7/F-6 (the pitch has to be repeatable by someone else in one sentence, because a referrer will paraphrase it). The repeatability test is a convention.
