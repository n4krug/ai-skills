---
name: feature-implementation
description: "Use this skill as the fourth and final step in feature work, right after feature-scaffolding has left TODO comments in place — whenever the user wants to actually implement a scaffolded feature (e.g. 'implement the X feature', 'fill in the TODOs for Y', 'build out the CSV export TODOs'). Writes the real code for each TODO, following the codebase's existing conventions, removing each TODO once it's implemented. Produces a deviation report (as a file and a chat summary) covering every place the implementation had to differ from the design/TODO and why. Makes a judgment call and reports it as a deviation for small ambiguities; pauses and asks the user only when something is genuinely blocking. Requires feature-scaffolding's TODOs to already exist — if none can be found for this feature, direct the user to run feature-scaffolding first rather than improvising an implementation from scratch."
---

# Feature Implementation

The fourth and final step in the feature workflow. Step 1 (`feature-planning`)
produced a plan, Step 2 (`feature-design`) turned it into a design, Step 3
(`feature-scaffolding`) turned the design into files and TODOs. This skill writes
the real, working code that fills in those TODOs — and reports honestly on every
place it had to deviate from the plan.

## When this applies

- Scaffolded TODOs already exist for this feature (from `feature-scaffolding`), and
  the user wants them actually implemented
- Requests like "implement the X feature", "fill in the TODOs for Y", "build out the
  CSV export feature"

**Requires the Step 3 TODOs.** Search the codebase for TODO comments referencing
this feature's design doc (e.g. `grep -r "TODO" --include=*` for comments pointing at
`designs/<feature>.md` or `docs/design/<feature>.md`). If none exist, don't invent an
implementation from a design or plan alone — tell the user you can't find scaffolded
TODOs for this feature and ask them to run `feature-scaffolding` first (or point you
to where the TODOs live).

## Workflow

### Step 1: Locate every TODO for this feature

Find every TODO comment tied to this feature across every file it touched — both the
new files `feature-scaffolding` created and the existing files it annotated. Match
strictly on the design-doc reference each TODO carries (see the guardrail below) —
don't touch a TODO that belongs to a different feature or predates this workflow
entirely, even if it sits right next to one that does belong to you.

### Step 2: Read the design (and plan, if useful)

Each TODO points back to a section of the design doc — read the relevant sections
for full context before writing code, not just the one-line TODO comment. Skim the
Step 1 plan too if the design doc alone leaves the intent unclear.

### Step 3: Implement each TODO

For every TODO, write the real, working code it describes:

- Match the surrounding file's and codebase's existing conventions (style, patterns,
  error handling, naming) rather than introducing new ones
- Once a TODO is implemented, remove the TODO comment — the code that replaces it is
  the record of what happened, not a comment saying it's done
- Implement TODOs in an order that makes sense given their dependencies (e.g. a data
  model field before an endpoint that needs it)

**When a TODO is ambiguous or underspecified:**

- For small stuff — a reasonable implementation detail the design didn't spell out
  (e.g. exact error message wording, minor validation choice) — make the sensible
  call yourself, implement it, and log it as a deviation (see Step 4). Don't stop
  and ask for things a competent engineer would just decide.
- For truly blocking stuff — the design's intent is genuinely unclear in a way that
  would materially change behavior, or something the design assumed turns out not to
  hold (a similar situation to what `feature-design` flags about the plan) — pause,
  leave that TODO in place unimplemented, and ask the user. Keep implementing other
  independent TODOs in the meantime rather than stalling the whole feature.

### Step 4: Track deviations as you go

A deviation is any place the actual implementation differs from what the design/TODO
explicitly said — not just ambiguity calls, but also cases like: a file needed
changes the design didn't mention, an endpoint's shape had to change because of
something already in the codebase, a dependency didn't work the way assumed, etc.
For each one, note: where (file/section), what changed, and why. Silently working
around a problem instead of logging it defeats the point of this step.

### Step 5: Write the deviation report and summarize

Save a report as a file, mirroring the naming convention already used by
`/plans` + `/designs`, or `/docs/plans` + `/docs/design`: use `/reports/<feature>.md`
or `/docs/reports/<feature>.md` respectively. Sections:

1. **Summary** — what was implemented, in a sentence or two, plus which files changed
2. **Deviations** — one entry per deviation from Step 4 (where / what / why). If
   there were none, say so explicitly rather than omitting the section
3. **Blocked / needs input** — any TODOs left unimplemented pending a genuinely
   blocking question, and what's needed to unblock them. Omit this section if
   nothing is blocked

Then give the user a concise chat summary covering the same ground — don't make them
open the file to learn whether anything went sideways.

## Guardrails

- **Only touch TODOs that reference this feature's design doc.** Every TODO
  `feature-scaffolding` writes ends with a pointer like
  `See designs/<feature>.md#section` or `See docs/design/<feature>.md#section`. Match
  on that reference, not on file location or TODO wording alone. A TODO in the same
  file that points at a *different* feature's design doc, or a plain non-workflow
  TODO with no design-doc reference at all (someone's unrelated "clean this up"
  note), must be left completely untouched — don't implement it, don't remove it,
  don't mention it as a deviation. It isn't part of this pass.
- Only implement TODOs that already exist from `feature-scaffolding` — don't expand
  scope or invent new TODOs to fill
- Match existing codebase conventions; don't introduce a new style or pattern the
  rest of the file/codebase doesn't already use
- Every deviation gets reported — there's no such thing as a deviation too minor to
  mention, only ones that turn out to be a one-line entry
- Don't guess through something genuinely blocking just to appear finished — pause
  and ask, and keep working on whatever else is independent
- The deviation report isn't optional even when everything went exactly to plan —
  produce it either way, noting "no deviations" plainly
