---
name: feature-data-flow
description: Traces where a feature lives in a codebase and maps how data flows through it — every file, function, and hop involved. Use this whenever the user asks "where is X implemented", "find all the code that touches X", "trace the data flow of X", "how does X work end to end", or wants a map/diagram of a feature across the code, whether X is named precisely (a function, endpoint, class, table) or described loosely (a product-level concept like "checkout flow" or "password reset"). Trigger this proactively any time the user is trying to understand the shape or full extent of a feature across a single codebase, even if they don't use the words "data flow" or "trace" explicitly — e.g. "what touches the user's email address", "walk me through how signup works", "everywhere we handle refunds".
---

# Feature Data Flow Finder

Goal: given a feature — named precisely or described loosely — find every place in a single repo that's involved, and lay out how data moves between the pieces.

Scoped to **one repository**. If the flow genuinely crosses into another service/repo that isn't present, name that seam explicitly rather than guessing at code you can't see.

## Workflow

### Step 1: Establish the repo root and shape
Confirm the working codebase if not already obvious. Use `bash_tool`/`view` (`find`, `git rev-parse --show-toplevel`, or just look at the directory listing) to get oriented — language, framework, and rough layout (`package.json`, `requirements.txt`, `go.mod`, `pom.xml`, etc. are quick tells).

### Step 2: Classify the query
- **Precise identifier** (function name, route, class, table/column name): search for it directly.
- **Fuzzy / product-level concept** (e.g. "checkout flow", "password reset"): before searching, brainstorm 5–10 candidate keywords — likely function/route/event names, UI copy, table names, synonyms (British/American spelling, singular/plural, camelCase/snake_case/kebab-case variants).
- If genuinely ambiguous, a quick clarifying question is fine — but default to trying the precise search first and widening if it comes up empty, rather than stalling on the question.

### Step 3: Search broadly, then follow the thread
Prefer `rg` (ripgrep) for speed and gitignore-awareness; fall back to `grep -r` if unavailable.
```
rg -ni "keyword1|keyword2|keyword3"
```
- Check the typical entry-point locations first: HTTP routes/controllers, GraphQL resolvers, UI components/event handlers, CLI commands, cron/scheduled jobs, message queue consumers.
- Don't stop at the first hits. From each hit, follow imports/calls outward (who calls this? what does it call?) until you reach natural termination points: a DB write/read, an external API call, a rendered response, a queued event.
- Tests are a good source of truth for intended behavior — check them if the flow is unclear from source alone.
- For large repos, narrow folder-by-folder using the project's own structure (e.g. `src/api`, `src/services`) rather than grepping the entire tree blindly.

### Step 4: Assemble the flow
For each hop, capture:
- File path + line number (never approximate — always a real, verifiable reference)
- Its role (entry point / validation / business logic / data access / external call / response)
- Direction of data (incoming request, outgoing response, DB read, DB write, event emitted, etc.)

Note any dead ends, feature-flagged branches, or deprecated code paths you notice along the way — these are often relevant to "where is X" questions even if they're not the main path.

### Step 5: Decide the output — no fixed format, pick what fits
- **Quick list in chat**: `file:line — role` for narrow lookups or short flows (roughly <5 files, few hops).
- **List + diagram**: once there are more hops, branches, or several files, add a diagram alongside the list (see below).
- **Markdown report file**: for large/sprawling features (many files, multiple entry points), or whenever the user seems likely to want to save/share it. Include per-component sections with code refs, plus the embedded diagram.
- When genuinely unsure, default to the lighter option — it's easy to expand into a file if the user wants more.

### Step 6: Diagram guidance (when a diagram earns its place)
- **Sequence diagram** — for a mostly-linear multi-hop call chain; shows caller → callee order and request/response clearly.
- **Flowchart** — for branching/conditional flows (e.g. validation → success/fail paths, feature flags).
- Diagram only the meaningful hops (route → controller → service → DB); don't include every helper function.
- In chat, use the visualization tool for an inline diagram. In a markdown report file, use a mermaid code block so it renders wherever the file is viewed.

## Output principles
- Real file:line references only, never invented or approximate paths.
- Be explicit about repo boundaries: if the trail leads to an external service call (e.g. `POST /charge` into `payments-service`), say so and stop there rather than fabricating what's on the other side.
- Keep the write-up scannable — a list of touchpoints beats a wall of prose.
