---
name: feature-design
description: "Use this skill as the second step in feature work, right after a feature-planning plan file exists — whenever the user wants to lay out the detailed design for a feature that already has an approved high-level plan (e.g. 'let's design the CSV export feature', 'flesh out the plan for X', 'what does the notifications feature need in detail'). Turns a high-level plan into a concrete design — API endpoints, data model shape, UI layout, and the exact files that will need to be touched — grounded in the real codebase. Still stops short of writing code, pseudocode, or schema syntax; that belongs to implementation, the step after this one. Requires a feature-planning plan file to already exist — if one can't be found, direct the user to run feature-planning first rather than skipping ahead."
---

# Feature Design

The second step in a multi-step feature workflow. Step 1 (`feature-planning`)
produces a short, high-level plan with no implementation detail. This skill takes
that plan and fleshes it out into a concrete design — endpoints, data shape, UI
layout, and the specific files involved — while still stopping short of actual code.
Implementation happens in a later step, out of scope here.

## When this applies

- A `feature-planning` plan file already exists for the feature in question, and the
  user wants to move it forward into detailed design
- Requests like "design the X feature", "flesh out the plan for Y", "lay out the
  details for Z"

**Requires the Step 1 plan.** If you can't find a plan file for this feature (check
`/plans/`, `/docs/plans/`, or wherever this repo's plans live), don't guess your way
into a design. Tell the user you can't find a plan for this feature and ask them to
either point you to it or run the `feature-planning` skill first.

## Workflow

### Step 1: Read the plan

Load the plan file and extract: the Goal, Scope, Non-goals, and Affected areas it
already identified. This design must stay inside the plan's scope — if something in
the design would fall outside what the plan covers, flag it rather than silently
expanding scope.

### Step 2: Deeper codebase scan

The plan's "Affected areas" were identified at a skim level. Now actually open those
files and read them closely:

- Existing endpoint conventions (URL structure, auth patterns, response shape) in the
  affected API areas
- Existing model/schema conventions (naming, field types, relations) in the affected
  data areas
- Existing component/UI conventions (structure, state handling, styling approach) in
  the affected UI areas

This is where the design's grounding comes from — it should read like it belongs in
this codebase, not like a generic proposal.

### Step 3: Ask questions (until confident)

Fill in whatever the plan and scan didn't already answer. Typical topics:

- Exact fields needed for new/changed data
- Request/response shape for new endpoints — what's required vs optional
- UI states that matter (loading, empty, error, permissions-denied)
- Edge cases the plan's "Open questions" flagged but didn't resolve

As with `feature-planning`, keep asking — across multiple rounds if needed — until
you're confident enough to write every section without guessing. Don't stop at a
fixed number of questions, and don't stop early just because you have *something* to
say for each section.

### Step 4: Draft the design doc

Write a markdown file to a dedicated design folder — `/designs/` by default, or
`/docs/design/` if that convention already exists (mirror whatever pattern the plan
folder used, e.g. plan in `/docs/plans/` → design in `/docs/design/`).

**Filename:** same feature slug as the plan file, e.g. `designs/csv-export.md`.

Use `assets/design-template.md` as the structural template. Sections:

1. **Overview** — one or two lines recapping the goal, with a pointer to the plan file
2. **API endpoints** — for each new/changed endpoint: method, path, purpose, request
   fields (name, plain-English type, required/optional), response fields. Use tables
   or bullets, plain-English types only (e.g. "text", "number", "date", "true/false")
   — never language-specific types, JSON code blocks, or route-handler code
3. **Data model** — for each new/changed entity: fields (name, plain-English type,
   notes) and relationships described in prose (e.g. "a Comment belongs to one Task
   and one User"). No schema DDL, no ORM syntax, no class definitions
4. **UI layout** — screens/components affected or newly needed, and the states each
   should handle (loading, empty, error, etc.), described in prose/bullets. No JSX,
   no markup, no styling code
5. **Files to touch** — concrete paths from the actual repo, each with a one-line
   description of what needs to change there conceptually (e.g.
   "`src/routes/tasks.js` — add the export endpoint"). Descriptions stay conceptual;
   no code, no diffs
6. **Open questions / risks** — anything still unresolved or risky heading into
   implementation

**Hard rule — still no code.** No code blocks, no pseudocode, no schema/DDL syntax,
no function or class signatures, no JSON payload examples, no language-specific type
annotations (`string`, `int`, `VARCHAR(255)`, etc. are all out — use "text", "number",
"whole number" instead). Endpoints, fields, and files are named and described in
plain language and tables, not written as code. If you catch yourself writing
anything that would compile or run, stop and rewrite it as a description.

### Step 5: Present and revise

Show the user the design (or point them to the file) and invite changes. Iterate in
place until they confirm it's good. That's the end of this skill's job — actual
implementation is a later step, not part of this pass.

## Guardrails

- Never skip Step 1 — if there's no plan, stop and ask for one rather than inventing
  scope
- Never skip Step 2 — a design not grounded in the real files will invent
  conventions that don't match the codebase
- If the deeper scan in Step 2 reveals the plan's "Affected areas" were wrong or
  incomplete, don't silently fix it — note the discrepancy for the user in Open
  questions / risks (or flag it to them directly) since it may affect the plan itself
- Keep "Files to touch" to real paths only — don't invent files or guess at names
  that don't fit the codebase's actual structure
- No code, ever — including inside "Files to touch" and "API endpoints," where the
  temptation to write a snippet is strongest
