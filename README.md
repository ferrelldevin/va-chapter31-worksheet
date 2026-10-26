# VA Chapter 31 Payment Worksheet

> Full case study: [devinferrell.com/work/va-chapter-31-worksheet](https://devinferrell.com/work/va-chapter-31-worksheet/)

**Case study — the tool is not linked here on purpose.** An independent adversarial review
returned a failing verdict on an earlier build and it has not been re-gated, so the verdict of
record still stands. Money is the failure mode; I am not sending strangers to a calculator that
my own gate has not cleared. What follows is the reasoning, the constraints, and what the review
caught. Written to a six-field schema — Problem, Constraints, Solution, Responsibilities,
Technology, Status.

---

## Problem

A veteran on VA Chapter 31 cannot find out what they will be paid until after they are paid.

Payment depends on *training time* — full, ¾, ½ — which VA measures from certified credit hours
under 38 CFR § 21.4270(c). **That is the one number a student cannot look up.** They can obtain
their certified credit hours and their term dates; they cannot obtain the school's full-time
figure, their measured training time, or their rate. No official VA tool computes it: the rate
pages are static tables, the public school-lookup was retired, and the GI Bill Comparison Tool
lists Chapter 31 in its selector while its code path ignores enrollment entirely and serves a
rate that expired in 2023.

So the person with the most at stake is the only one without the number.

## Constraints

**Money is the failure mode, so wrong beats missing only in one direction.** Overstating a
payment invites a veteran to spend money they will owe back. Every unresolved case renders as
UNKNOWN with the reason, never as an estimate.

**No rate may be stored without its expiry.** Chapter 31 rates change every 1 October; housing
allowances every 1 January. The page carries `asOf` and `nextChange` in the data object and
withholds money after the change date while keeping training time — because the *measurement*
rule is not dated even though the money is. This constraint came from watching VA's own tool
serve a three-year-expired figure.

**No housing figure is stored at all.** It is ZIP-specific and resets annually; the page asks for
it and links the issuer, so the number is current by construction rather than by maintenance.

**Not a filing tool.** It computes and explains. It does not submit anything, hold personal data,
or talk to a server — single self-contained HTML file, no backend, nothing leaves the browser.

**Where no published rule exists, say so.** One step had no locatable authority; it shipped
labelled *"none found"* rather than guessed. That label was later proven wrong — see Status.

## Solution

Each constraint, answered:

- **Sessions are first-class dated objects.** Term dates once, sessions inside it with their own
  dates, classes pick a session from a dropdown. This is not a UI convenience — it is the data
  model § 21.4272(g) and VA's overlap rule require. Earlier versions used one whole-weeks-per-term
  field and could not express classes that start and stop on different dates, which is why they
  kept producing wrong answers.
- **Output is a date-segmented schedule, not a single number.** Training time steps down on
  calendar dates as short sessions end. In the worked example a two-day gap between an 8-week and
  a 16-week session drops the veteran from full time to half time for those two days.
- **Money is prorated per calendar month**, capped at 30/30, because a monthly rate is not what
  lands in an account.
- **Below half time renders NOT PAYABLE citing § 21.314**, a statement about the law, rather than
  "not on this table", a statement about the page.
- **In-person versus online is per class**, implementing § 21.260(c) footnote 5: one in-person
  class moves the whole period from a fraction of a national average onto the campus rate. In the
  worked example that is $1,000 versus $2,400 on identical credits.
- **Every rule shows its authority at the line** so a certifying official can check the reasoning
  instead of trusting the total.

## Responsibilities

Sole engineer, designer, and researcher. Sourced the regulations, wrote the arithmetic, built the
interface, ran the reviews, and kept the decision record. Reviewed by an external model acting as
an adversarial judge, and in review with a VR&E counselor and a campus benefits office.

## Technology

Single self-contained HTML file — no backend, no build step, no dependencies beyond a webfont.
Plain JavaScript. Verified by driving the running page in a headless browser rather than by
reading source.

Authorities implemented: 38 CFR §§ 21.260, 21.310, 21.312, 21.314, 21.4270, 21.4272 · 38 U.S.C.
3108 · VA M28C · VA FY2026 Chapter 31 rate tables.

## Status

**Live, in review, and it has failed a gate.** An independent model review returned **FAIL, 0 of 7**
on an earlier build. Three of its six defects are now closed; it has not been re-gated, so the
verdict of record still stands.

**What that review caught matters more than that it failed.** The rounding rule: VA drops the
fractional unit, and rounding to nearest turned 6.75 into 7 — *overstating* training time and
therefore pay. That is the exact direction of harm the project exists to prevent, and I had
declared the rule "a judgment call" in the review packet. It was not.

**Known and open:**
- The `"none found"` label above was wrong. VA's School Certifying Official Handbook publishes the
  multi-session rule with a worked example. A false *"no answer exists"* is worse than a wrong
  number, and it shipped for a day.
- § 21.312 reduced work tolerance lets a reduced course load *be* full time. A credit-hours-in tool
  silently under-predicts for those veterans — and a VR&E caseload is disproportionately that
  population. **This tool is wrong for the people most likely to open it**, and it says so on
  the page rather than hiding it.
- Every regulation quote came from a faithful mirror rather than the issuer, which the project's
  own gate distinguishes. The rate figures *are* issuer-verified.

---

### What I would build next

A pre-Gate-A check that re-derives the question from the originating need rather than from the
spec. Two of the three worst defects here were *spec* defects: the test suite passed 34 of 34
assertions, twice, on an artifact that answered the wrong question. A mechanical gate verifies an
artifact against the spec it was built to and has no opinion on whether that spec is right.
