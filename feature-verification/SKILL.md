---
name: feature-verification
description: "Use this skill as the fifth step in feature work, right after feature-implementation has produced real code and a deviation report — whenever the user wants to confirm a feature actually works as designed (e.g. 'verify the X feature', 'check that Y matches the design', 'run tests for the Z feature'). Independently checks the implementation against the design doc section by section, runs the existing test suite, writes new tests for anything not already covered, and surfaces any bugs, missing pieces, or undisclosed deviations it finds. Never fixes anything itself — it always reports findings and asks before making any change. Requires a feature-implementation report to already exist — if one can't be found, direct the user to run feature-implementation first rather than verifying nothing against no baseline."
---

# Feature Verification

The fifth and final step in the feature workflow, following `feature-planning`,
`feature-design`, `feature-scaffolding`, and `feature-implementation`. This skill
doesn't trust that the implementation matches the design just because the TODOs are
gone — it independently checks, runs tests, and reports what it actually finds. It
never fixes anything on its own initiative.

## When this applies

- A `feature-implementation` report already exists for this feature, and the user
  wants to confirm the result actually works
- Requests like "verify the X feature", "check Y matches the design", "run tests for
  the Z feature"

**Requires the Step 4 implementation report.** If you can't find one for this
feature (check `/reports/`, `/docs/reports/`, or wherever this repo's reports live),
there's no baseline to verify against — tell the user you can't find an
implementation report for this feature and ask them to point you to it or run
`feature-implementation` first.

## Workflow

### Step 1: Read the full chain

Read the implementation report, and the design doc it points back to (and the plan,
if useful for context). You need three things: what was supposed to be built, what
the implementation report claims was built, and what deviations were already
disclosed. This skill's job is to check all three against the actual code — not just
re-read the report and take its word for it.

### Step 2: Verify the implementation against the design, section by section

Go through the design doc's sections one at a time and check the real code:

- **API endpoints** — does each documented endpoint actually exist? Do its request
  and response fields match what the design describes?
- **Data model** — does each entity/field described actually exist in the code, with
  the relationships the design specified?
- **UI layout** — does each screen/component/state described actually exist and
  behave as specified (loading, empty, error states included)?
- **Files to touch** — was every listed file actually touched, or a new file created
  where one was called for? Flag anything listed that wasn't.

For each item, the outcome is one of: **matches**, **missing**, or **mismatched**
(exists but behaves differently than designed). Also watch for anything the
implementation report didn't disclose as a deviation but actually is one — this is
part of this skill's value: catching what self-reporting missed.

### Step 3: Run tests — existing and new

- Find the repo's test setup (test script in the manifest, existing test files/
  framework) and run whatever test suite already exists
- For anything from this feature that isn't already covered by a test, write new
  tests using the same framework and conventions already in use in the repo, then
  run those too
- If the repo has no test framework at all, don't invent one or silently skip
  testing — flag this as a blocker and ask the user how they'd like to proceed
- Capture and report actual results (pass/fail), not just "tests were written"

### Step 4: Never fix — report and ask

If verification turns up a bug, a missing piece, or a mismatch with the design, do
not fix it. Write it up clearly enough that the user can decide, and explicitly ask
whether they'd like it fixed now — even for something that looks trivial. This
applies to every finding, including newly-discovered undisclosed deviations.

### Step 5: Write the verification report and summarize

Save a report as a file, mirroring the naming convention already used by
`/reports` or `/docs/reports`: use `/verification/<feature>.md` or
`/docs/verification/<feature>.md` respectively. Sections:

1. **Summary** — one or two lines on overall status
2. **Design conformance** — a checklist-style rundown of each design item and its
   outcome (matches / missing / mismatched), including any newly-found undisclosed
   deviations
3. **Test results** — what was run (existing + newly written), pass/fail counts, and
   what any new tests cover
4. **Findings requiring a decision** — every bug, gap, or mismatch that needs the
   user's input on whether/how to fix it. State plainly if there's nothing here

Then give the user a concise chat summary covering the same ground, and explicitly
ask about any findings that need a fix decision — don't just leave them in the file.

## Guardrails

- **Never fix anything without being asked to.** Not a one-line bug, not a typo in a
  response field — report it and wait
- Don't rubber-stamp the implementation report — actually check the code and run
  tests; "the report says it's done" is not verification
- Distinguish previously-disclosed deviations from newly-found undisclosed ones in
  the report, so the user knows what's new information
- If there's no test framework in the repo, that's a blocker to ask about, not
  something to work around silently
- The verification report isn't optional even when everything checks out — produce
  it either way, stating plainly that everything matched
