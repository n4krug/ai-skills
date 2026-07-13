---
name: feature-planning
description: Use this skill as the mandatory first step whenever the user wants to start work on a new feature, add new functionality, or kicks off a request like "let's build X", "I want to add Y", "help me implement Z". Do NOT jump straight into writing code or design details for a new feature — use this skill first to scan the codebase, interview the user, and produce a high-level, codebase-grounded plan saved as a markdown file. This skill only covers planning; it explicitly stops short of implementation specifics like code, function names, or schemas, since those belong to a later step. Trigger this even if the user doesn't say the word "plan" — any greenfield feature request in an existing codebase should start here.
---

# Feature Planning

The first step in a multi-step feature workflow: turn a rough feature request into a
short, codebase-grounded plan the user has signed off on — with zero implementation
detail. Later steps (design, implementation) are out of scope for this skill.

## When this applies

- New feature requests in an existing codebase: "let's add X", "I want to build Y",
  "can we implement Z"
- The user is at the very start of feature work and hasn't already got a plan

**Does not apply to:** bug fixes, small tweaks, or cases where the user already has a
detailed plan/spec and just wants code written. If a plan file already exists for this
feature, treat this as a revision of Step 4, not a fresh Step 1.

## Workflow

### Step 1: Quick codebase scan

Before asking anything, get just enough context to ask smart questions and ground the
plan in reality. Do **not** go deep — this is a skim, not a design pass.

- Look at repo structure, README, and manifest files (package.json, pyproject.toml,
  etc.) to understand the stack and high-level architecture
- Identify the areas of the codebase that are plausibly relevant to the requested
  feature (which services/modules/directories would likely be touched)
- Note existing conventions (folder layout, naming, doc locations) so the plan fits
  the repo's style — including whether `/plans` or `/docs/plans` already exists

### Step 2: Ask the user questions (until confident)

Ask about whatever the codebase scan couldn't tell you. Typical topics:

- **Goal** — what problem this solves and why now
- **Users** — who or what will use this feature (end users, other services, admins)
- **Scope boundaries** — what's explicitly *not* included in this pass
- **Constraints** — deadlines, must reuse existing patterns, must avoid certain
  dependencies, etc.
- **Known affected areas** — anything the user already knows will need to change that
  the scan might have missed

If your environment has a structured question/elicitation tool, use it for quick
multiple-choice questions; otherwise just ask directly in your reply. Keep asking —
in multiple rounds if needed — until you're
genuinely confident you understand the goal, scope, and constraints well enough to
draft a grounded plan. Don't pad this with questions for their own sake, but don't cap
it artificially either: if an answer opens up a new ambiguity, ask about that too
before drafting. Move to Step 3 once you'd be comfortable defending every section of
the plan without guessing.

### Step 3: Draft the plan

Write a markdown file to a dedicated plans folder — `/plans/` by default, or
`/docs/plans/` if that convention already exists in the repo. Create the folder if
neither exists yet.

**Filename:** kebab-case feature name, e.g. `plans/csv-export.md`. Date-prefix it
(`plans/2026-07-08-csv-export.md`) only if the repo already dates similar docs
elsewhere.

Use `assets/plan-template.md` as the structural template. Sections:

1. **Goal** — plain-language statement of the problem and why it matters
2. **Scope** — what this pass will cover
3. **Non-goals** — explicitly out of scope, so nobody assumes otherwise later
4. **Affected areas** — structural, from the codebase scan (e.g. "the auth
   middleware", "the billing service", "the settings UI") — never function names,
   file-by-file diffs, or code. Only list areas that will genuinely be touched. Don't
   pad this list with observations about what *doesn't* exist or isn't affected
   (e.g. "no existing export functionality") — that kind of note belongs in "Open
   questions / risks" if it's relevant at all, not "Affected areas."
5. **Open questions / risks** — anything unresolved, ambiguous, or risky that should
   be flagged before implementation starts

**Hard rule — no implementation specifics.** No code or pseudocode, no function/class
names, no specific algorithms or library choices, no database schema, no API
signatures. If you catch yourself writing any of these, cut it and turn it into an
open question or a bullet in "affected areas" instead. That decision-making belongs to
a later step in the workflow, not this one.

Keep it short — bullets over paragraphs. A plan nobody reads is worse than no plan.

### Step 4: Present and revise

Point the user to the file (or show it inline) and explicitly invite edits: "Let me
know what to adjust." Iterate in place on the same file until the user confirms it's
good. Only then is this step done. Do not continue into implementation or detailed
design in the same pass — that's the next skill/step in the workflow.

## Guardrails

- Never write code or pseudocode in the plan, even as illustration
- Never assert an architecture decision the codebase scan didn't actually support —
  if unsure, put it in "open questions" instead of stating it as fact
- Don't skip Step 1 — a plan that isn't grounded in the actual codebase is just
  generic guesswork
- Don't skip Step 4 — the plan isn't final until the user has seen and confirmed it
