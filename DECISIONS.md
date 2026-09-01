# Decision log — public subset

Fifteen entries from a working decision log kept while building. Each records what was decided,
what it cost, and what would undo it. **They are dated at the time, not reconstructed afterwards.**

Most were chosen because I was wrong in them.

> **Why this exists as a portfolio piece.** A brag document is retrospective and self-reported.
> A decision log is contemporaneous and falsifiable — which is the property that makes a signal
> expensive to fake, and the reason it is worth more than a list of technologies. Of fourteen
> early-career portfolios reviewed for comparison, one published reasoning rather than outcomes.
>
> The full log runs to 157 entries. This is a curated subset with nothing personal in it.

---

### 1 · A test suite passed 34 of 34 on an artifact answering the wrong question

Twice. The suite verified the artifact against the spec it was built to, and the spec was wrong —
it asked the user to *select* their training time, when training time is the number they cannot
look up and the whole point of the tool. **A mechanical gate has no opinion on whether its spec
is right.** Every assertion was true and measured nothing.

**Cost:** two full rebuilds. **Undo:** restore the earlier version, which is kept.

### 2 · I wrote a test to catch a bug, and the test passed with the detector deleted

The rule was "every check needs a fixture it demonstrably fails." I wrote one for a detector I had
just narrowed. It used an input a *different* detector also caught, and asserted on a string that
was a substring of the target's own output. **Deleting the detector entirely left the test green.**

Caught by an external reviewer, not by me. Repaired to an input only the target can catch, then
proved by deleting the detector and confirming the test now fails.

**Cost:** would have shipped a suite that could not detect its own subject's removal.

### 3 · A safety hook was declared closed and never wired

The handler existed in the script. Three fixtures passed. The README recorded the hole as closed.
The settings file that registers hooks contained zero occurrences of the event.

**A hook is two artifacts — the script and the registration — and the fixtures exercised only
the first.** Coverage without wiring.

### 4 · Wiring it exposed that the test suite polluted live state

The suite pointed at the real project directory, so it read and wrote the live counter and deleted
it on exit. With a live hook now able to fire mid-run, the two raced the same file: **17/19, then
18/19, then 19/19 across consecutive runs with no code change.**

A suite that can be perturbed by the thing it tests is not a suite. Isolated to a temp directory,
matching the pattern a sibling suite already used — an inconsistency inside the project, not a
missing idea.

### 5 · Measured a detector's false-positive rate instead of arguing about it

A personal-data guard fired six times in two sessions, every time on ordinary prose, and caught
nothing. **Six false positives, zero true positives.** Google's published static-analysis practice
sets **under 10% effective false positives** to enable a check at commit time; this one sat at 100%.

The cause was one character: a case-insensitive flag defeating the pattern's own `[A-Z][a-z]+`.
Every suffix token in it — Way, Lane, Court, Drive — is also an ordinary English noun.

**Undo:** one revert, which restores the 100% rate.

### 6 · Then my own fix broke a different detector

Adding a tracking table used the canonical row format, so the drift checker counted every entry
twice and reported 23 duplicated IDs. **The script had documented that exact trap in its own
output.** The convention existed, was written down, and I did not read it before writing into the
file it guards.

Fifth instance of one pattern in a single session: *a gate that fires falsely stops being read,
and the fastest way to poison one is to trip it without checking.*

### 7 · The screen said three, the arithmetic counted two

Reported by a user: "if I entered the full term class last, stuff got miscalculated." Reproduced by
driving the page. Deleting a session left its classes holding a dead reference — the dropdown fell
back to displaying the first option while the stored value stayed stale, and the lookup silently
dropped the class.

**Three classes displayed, two counted. Six credits and half time where nine and three-quarter time
was on screen** — silent credit loss, understating a veteran's pay.

### 8 · Published work existed on one laptop and nothing reported it

A branch with five commits and roughly 15,000 lines had no upstream and no remote copy. **Every
session that touched it correctly reported success, because committing *is* success locally** —
`git status` reports a clean tree for an unpushed branch.

The check that catches it is `git branch -vv` and an empty upstream column. Nothing was running it.

### 9 · Refused a statistic rather than repeat it

A claim circulates widely that a 2025 study found 83% of hiring managers trust GitHub profiles over
resumes, bundled with "10+ starred repos gets 4× more interviews." Checked the named company's own
site: **nothing of the kind exists.** It propagates through content farms citing each other, and it
contradicts the one real study, which rates star counts *unreliable*.

**Undo:** none. Refusing to cite something is not reversible and should not be.

### 10 · VA's own published table is structurally misleading, and it misled everyone

A rate table merges three regulatory program types into one row group and hangs a ¼-time rate
underneath. In the regulation those are separate blocks and **only the third has a ¼ row** — a
footnote carries the entire restriction.

An external reviewer read it structurally and called my artifact wrong. I confirmed the reviewer at
the issuer and repeated the error. **The artifact's original instinct was closer to right than
either review of it.**

### 11 · Scouted before building, because the obvious artifact would have been wrong

Asked to build an office tool for a VA counselor who "can't get her veterans paid." The obvious move
was a calculator. Research first established that she is not reaching the computation — she is stuck
at a certification gate she does not control, which VA proposed a deadline for in 2024 and never
finalized.

**Three artifact types were rejected on evidence:** a calculator (wrong problem), a tracker (no
status feed exists to read), and a rebuilt handbook (VA publishes one; only its URL is dead).

### 12 · Ran an adversarial review that failed my own work 0 of 7

Sent the artifact to an independent model instructed to refute it. Verdict: **FAIL, "unsafe payment
estimator."** Its sharpest finding contradicted a call I had made in the review packet — I had
declared a rounding rule "a judgment call"; VA's guidance says drop the fraction. Rounding to
nearest **overstated** training time and pay.

**Generator ≠ judge, and the judge was right.**

### 13 · Bypassed a safety gate once, with the blast radius written down

An escalation card needed VA's published phone numbers. The detector matched correctly — they are
phone-shaped — but published government contacts are not personal data under the rule's own wording,
and no allowlist mechanism existed.

Bypassed with authorization, and recorded **which six detectors went dark to let three institutional
contacts through.** That asymmetry is the argument for building the allowlist, not against the bypass.

### 14 · Declared a step unbacked rather than guessing

One computation had no locatable published rule. It shipped labelled *"none found"* and told the
reader to treat the result as a question rather than an answer.

**Later proven wrong** — VA does publish the rule, in a handbook I had not reached. A false *"no
answer exists"* is worse than a wrong number: it tells someone to stop looking.

### 15 · Two of three real defects came from a glance, not from a gate

Across four review rounds, a formal test suite passed every time. The defects that mattered were
found by a reader looking at the screen for a few seconds — including one where the displayed amount
overstated what a part-time veteran was owed.

**The finding is about the gates, not the artifact.** A suite tests what you thought to assert.

---

## What this log is for

Every entry carries a reversal — the specific command or action that undoes it. That column exists
because a decision you cannot undo is not a decision, it is a commitment, and the two should not be
recorded the same way.
