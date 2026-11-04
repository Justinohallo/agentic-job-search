# VALIDATION.md — definition of done for M1

## Status

**Baseline defined, nothing run yet (2026-11-04).** M1 is done when every test case below passes at once.

---

## Baseline

| Measure | Target |
|---|---|
| Deterministic-layer faults caught (F-01 … F-08) | **8 / 8**, each with the exact rule and severity in its `expected.json` |
| Judge-layer faults the deterministic layer misses (F-09, F-10) | **2 / 2** produce zero deterministic errors (the documented gap) |
| Clean fixtures (Northwind, Harbour Ledger) | **0 errors, 0 warnings** each |
| Judge-layer faults caught with `--judge` (live, manual) | **≥ 1 / 2**, reported in `CHANGELOG.md` with the model's reasons |
| Real career repo (`CAREER_REPO`, local only) | Runs on every real resume with **zero exit-2 parse errors**; findings listed for the user to review |

## Tolerance

None for the deterministic layer: counts are exact. The judge is non-deterministic, so its baseline is a floor, and false positives on the clean fixtures are recorded rather than failed.

---

## Test cases

| TC | Name | How | Pass when |
|---|---|---|---|
| TC-01 | Master parsing | Unit test on `fixtures/candidate/MASTER.md` | 7 entries (A-1, A-2, A-3, A-3b, A-4, A-5, A-6); `neverUse` has exactly the 2 A-1 phrases; A-4's `350` line is `estimate` |
| TC-02 | Resume parsing | Unit test on Northwind | 3 sections; 6 bullets; every bullet has its inline id; comments stripped from text |
| TC-03 | Trace table | Unit test on Harbour Ledger + its BRIEF | Bowline 1 → A-3, A-2; Ledgerly 2 → A-4; Prairie Freight 1 → A-1 |
| TC-04 | Number normalization | Unit table test | `$350K` = `$350,000`; `35%` ≠ `35`; `10×` = `10x`; `2017` skipped; `A-5` and `Q1` yield no token; `2.1` → 2.1 |
| TC-05 | Each rule in isolation | One unit test per rule G-1, G-1T, G-2, G-2W, G-3, G-4, G-5, G-6 using small inline strings | Fires on the bad case, silent on the good case |
| TC-06 | G-4 qualifiers | Unit test | "about 350" silent; "350" warns; `--estimates error` makes it an error; `off` silences it |
| TC-07 | Clean fixtures | `ground check-all fixtures/candidate` | Exit 0; 0 errors, 0 warnings in both |
| TC-08 | Fault suite | `npm run faults` | Every `expected.json` satisfied (8 deterministic caught; F-09, F-10 zero deterministic errors) |
| TC-09 | Exit codes | CLI tests | 0 clean, 1 on F-03, 2 on missing file, 2 on empty master, 2 on `--judge` without key |
| TC-10 | Determinism | Run TC-07 twice | Byte-identical stdout |
| TC-11 | Read-only | Hash every fixture before and after TC-07 and TC-08 | Hashes unchanged |
| TC-12 | Judge contract | Unit tests with a fake client | Prompt wraps data in tags; unsupported → G-8; bad JSON retried once then G-8E; never-use lines absent from prompt |
| TC-13 | CI | GitHub Actions workflow on push and pull request | Runs `npm ci`, `npm test`, `npm run faults`, `npm run ground -- check-all fixtures/candidate`; green on the final commit |
| TC-14 | Live judge (manual) | `ANTHROPIC_API_KEY=… npm run faults -- --judge` | ≥ 1 of F-09/F-10 caught; result and cost written to `CHANGELOG.md` |
| TC-15 | Real data (manual, local) | `CAREER_REPO=../career npm run ground -- check-all $CAREER_REPO` | No exit 2. Findings pasted into the user's private notes, **never** into this repo |

## Fail protocol

If a test case fails:

1. Do not change the test, the fixture or `expected.json` to match the output.
2. Do not change this baseline.
3. Write a report to `BLOCKERS.md`: the test, expected versus actual, the input, and a hypothesis.

A fixture that is itself wrong (for example, a fault that accidentally contains two faults) is a spec defect: write a blocker for the Architect instead of editing it.

## Sign-off

**Who:** the user (Justin), after QA signs off.
**How:** watch TC-08 and TC-07 run, plant one new fault by hand in a copy of a fixture and see it caught, then run TC-15 on his own machine.
