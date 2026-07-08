---
name: feature-scaffolding
description: "Use this skill as the third step in feature work, right after a feature-design doc exists — whenever the user wants to turn a detailed design into a reviewable skeleton of files and TODOs (e.g. 'scaffold the X feature', 'set up the files for Y', 'create the TODOs for Z so I can review them'). Creates any new files the design calls for with only the minimal boilerplate needed for that file type, and adds TODO comments — in existing and new files alike — marking exactly what needs to change, each explained in a sentence and traceable to the design doc. Writes zero real logic: no populated function bodies, no route handlers, no field definitions, nothing beyond the bare minimum shell. This is meant to be reviewed and revised by the user before any actual implementation happens. Requires a feature-design doc to already exist — if one can't be found, direct the user to run feature-design first rather than skipping ahead."
---

# Feature Scaffolding

The third step in a multi-step feature workflow. Step 1 (`feature-planning`)
produces a high-level plan. Step 2 (`feature-design`) turns that into a concrete
design. This skill turns the design into a reviewable skeleton: any new files the
design calls for, and TODO comments — in both new and existing files — marking
exactly what needs to be built. No real logic anywhere. The user reviews and revises
the TODOs before an implementation step (not covered by this skill) fills them in.

## When this applies

- A `feature-design` doc already exists for the feature, and the user wants to turn
  it into files + TODOs
- Requests like "scaffold the X feature", "set up the files for Y", "create the TODOs
  for Z"

**Requires the Step 2 design doc.** If you can't find one for this feature (check
`/designs/`, `/docs/design/`, or wherever this repo's designs live), don't guess your
way into a scaffold. Tell the user you can't find a design for this feature and ask
them to either point you to it or run `feature-design` first.

## Workflow

### Step 1: Read the design doc

Extract the "Files to touch," "API endpoints," "Data model," and "UI layout"
sections — these are the source of every TODO you'll write. Every TODO must be
traceable back to something the design doc actually says. Don't invent scaffolding
the design didn't call for.

### Step 2: Scan the codebase

For each file under "Files to touch":

- If it already exists, read it — you'll be inserting TODOs, not rewriting it
- If it's new, look at sibling files of the same type/directory (other routes, other
  models, other components) to learn the codebase's conventions, so the new file's
  minimal shell actually matches the house style instead of looking generic

### Step 3: Ask questions only if something's ambiguous

The design doc should already be detailed enough that this is usually mechanical. Ask
only when something is genuinely unclear — e.g. the design doesn't say exactly where
a new file should live, multiple existing files could plausibly house a described
change, or the codebase scan reveals two conflicting conventions to choose between.
Otherwise, skip straight to Step 4.

### Step 4: Create files and insert TODOs

**New files** — create with only the minimal boilerplate required for that file type
to be a valid, empty member of the codebase in its existing style: imports, the
bare function/class/component shell, exports. Nothing beyond that structural
minimum — no populated logic, no route registrations, no field definitions, no
rendered markup beyond an empty/placeholder return. Then add TODOs inside marking
what belongs where.

**Existing files** — never rewrite, reformat, or restructure existing code. Only
insert TODO comments, placed near the related existing code (or appended at a
sensible point if there's no natural anchor) marking what needs to be added or
changed there.

**TODO format** — use the file's native comment syntax. One TODO per design item,
phrased as a sentence explaining what needs to happen, with a pointer back to the
design doc:

```
// TODO: Add GET /tasks/export endpoint — returns the current user's tasks as CSV,
// respecting the same filters as GET /tasks. See designs/csv-export.md#api-endpoints
```

Adjust comment syntax per language (`//`, `#`, `<!-- -->`, etc.) to match the file.

### Step 5: Summarize and hand off for review

List what was created and what was changed, and explicitly invite the user to review
and revise the TODOs — this is the point of the exercise. Make clear that
implementation (writing the actual logic) is a later step, not part of this pass, and
that nothing here should be treated as final until they've looked it over.

## Guardrails

- **Zero real logic, ever.** Not a populated function body, not a route handler with
  even a trivial implementation, not a field definition, not real JSX content beyond
  an empty placeholder. If you're about to write something that would actually run
  and do something, stop — it belongs in a TODO instead.
- Never rewrite, reformat, or restructure existing files beyond adding TODOs
- Every TODO must trace back to something specific in the design doc — don't
  editorialize or add scope the design didn't call for
- Don't skip Step 1 — if there's no design doc, stop and ask for one
- Don't skip Step 2 for new files — an unstyled file that doesn't match the
  codebase's conventions defeats the point of scaffolding
