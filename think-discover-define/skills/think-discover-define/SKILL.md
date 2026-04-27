---
name: think-discover-define
description: Spec-driven feature development. Guides the developer through interactive discovery, generates a formal-ish spec with EARS requirements and state contracts, derives an implementation plan, and produces a validation strategy. Invoke manually with /think-discover-define or /spec when starting a new feature, modifying complex behavior, scoping a bug fix, or planning an implementation.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
metadata:
  version: 2.2.0
  category: development-workflow
  tags: think-discover-define, spec-driven-development, sdd, formal-methods
---

# Spec-Driven Feature Development

A Claude Code skill that replaces vibe-coding with structured, contract-based
feature development. Works with any language, framework, or architecture.

## Philosophy

**Spec is the contract.** A lightweight, living reference that both you and
the developer point at when things drift. Not a waterfall document.

**Discovery before implementation.** The biggest source of wasted cycles is
coding before requirements are clear. This skill front-loads the thinking.

**Don't ask what you can discover.** Before asking the developer any question,
check whether the codebase already answers it. This is a continuous rule —
not a one-time pre-phase. Read code during discovery, not just before it.

**Every question carries a recommended answer.** Based on codebase analysis,
propose what you think the answer is. The developer confirms or overrides.
This reduces cognitive load while keeping thoroughness.

**Formal-methods-flavored, not formal-methods-heavy.** We borrow pre/post
conditions, state invariants, and EARS notation without requiring mathematical
notation or verification tooling.

**Micro-spec, not mega-spec.** One spec per feature or change. Small, focused,
scoped to one shippable unit.

**Stack-agnostic spec, stack-specific plan.** The spec describes behavior,
not implementation. Tech stack details surface in the Plan phase.

---

## Pipeline Overview

```
01-SPEC  ──►  02-PLAN  ──►  03-TEST
(discover      (derive        (derive
 + specify)     tasks)         tests)

Each phase:
  read reference ──► explore codebase ──► draft artifact
  ──► present to developer ──► refine interactively ──► save on approval
```

**Three phases. Three artifacts. One checkpoint per phase.**

Never auto-advance without developer approval. After each phase, present the
draft and refine interactively until the developer confirms.

---

## Phase 01-SPEC

Combines orientation, discovery, and specification into one phase. Produces a
single SPEC document with context, discovery summary, formal requirements, and
the appropriate state-model tier (none, lightweight, or full).

### Execution

1. **Load phase instructions:**
   Read `${CLAUDE_SKILL_DIR}/references/01-SPEC.md` for detailed guidance on
   discovery, EARS notation, state models, and contracts.

2. **Scan the codebase (read-only):**
   Search for project docs (CLAUDE.md, architecture docs, testing docs, API
   specs, READMEs, config files). Identify the relevant modules, files, and
   patterns for the feature area. Synthesize into a Project Context summary.

3. **Run decision-tree discovery:**
   Walk through the feature's decision tree, exploring each branch depth-first.
   For every question:
   - Propose a recommended answer based on what you found in the codebase
   - Use `AskUserQuestion` for structured choices where applicable
   - Ask one question at a time — resolve each branch before moving on
   - If the codebase answers a question, state the answer and move on
   - Push back on vague or incomplete answers — probe deeper

   Cover all core topics: scope, user goal, entry/exit points, happy path,
   error states, data dependencies, async behavior, anti-requirements,
   constraints, and validation intent. The order follows the feature's natural
   shape, not a rigid template.

4. **Draft the complete SPEC:**
   Read `${CLAUDE_SKILL_DIR}/references/SPEC_TEMPLATE.md` for the format.
   Read `${CLAUDE_SKILL_DIR}/references/EXAMPLE_SPEC.md` for a worked example.
   Fill every section from discovery answers.

5. **Checkpoint — present the draft to the developer:**
   > "Here's the spec. Review each EARS requirement — are they accurate?
   > Any missing states or error cases? Anti-requirements to add?"

   This is the most important checkpoint. Loop until the developer confirms.

6. **Save:** Write `<slug>-SPEC.md` to the spec folder.

### Handling Partial Input

The developer may arrive with a Jira ticket, design doc, Slack thread, or
detailed prompt that already answers many questions.

1. Extract all available answers from what they provided.
2. Present a pre-populated discovery summary with your recommended answers.
3. Ask the developer to verify, then probe the gaps depth-first.
4. Don't skip topics — even comprehensive input usually has gaps in edge
   cases, error handling, or constraints.

---

## Phase 02-PLAN

Derives a concrete implementation plan from the approved spec.

### Execution

1. **Load phase instructions:**
   Read `${CLAUDE_SKILL_DIR}/references/02-PLAN.md` for detailed guidance.

2. **Re-read the approved SPEC.** Explore the codebase for implementation
   patterns, existing utilities, and conventions relevant to the plan.

3. **Draft the PLAN:** Component breakdown, task checklist, suggested order,
   and key implementation decisions not covered by the spec. Every plan item
   must trace to one or more `R#` or `INV-#` references.

4. **Checkpoint:**
   > "Here's the implementation plan. Want to reorder anything? Any tasks
   > missing or too large? Any decisions you want to make upfront?"

5. **Save:** Write `<slug>-PLAN.md` to the spec folder.

6. **Context-break checkpoint:**
   After saving, output this block verbatim — do not paraphrase it:

   ---
   > **Context checkpoint.** SPEC and PLAN are saved. If this session has been long, this is a good moment to clear context before the TEST phase.
   >
   > To continue in a fresh session, paste this prompt:
   >
   > ```
   > Continue spec-driven development.
   >
   > Approved artifacts saved:
   > - `specs/<folder>/<slug>-SPEC.md`
   > - `specs/<folder>/<slug>-PLAN.md`
   >
   > Run Phase 03-TEST from the think-discover-define skill:
   > - Read `references/03-TEST.md` for guidance
   > - Re-read the approved SPEC and PLAN
   > - Derive one or more validation items per requirement or invariant (no orphan validation)
   > - Present the draft for review, refine, then save `<slug>-TEST.md`
   > ```
   >
   > Or reply **"continue"** to proceed in this session.
   ---

   Wait for the developer's response before proceeding to Phase 03-TEST.

---

## Phase 03-TEST

Derives validation items directly from the EARS requirements and invariants.

### Execution

1. **Load phase instructions:**
   Read `${CLAUDE_SKILL_DIR}/references/03-TEST.md` for detailed guidance.

2. **Re-read the approved SPEC and PLAN.** Check for project testing docs
   or conventions discovered earlier.

3. **Draft the TEST strategy:** One or more validation items per requirement
   or invariant. Use executable tests by default, but switch to benchmarks,
   audit/manual checks, or inspection-only proofs when those are the correct
   verification method. Every validation item traces to the spec. No orphan
   validation.

4. **Checkpoint:**
   > "Here is the derived validation strategy. Do the validation types fit the
   > requirements? Any gaps, over-testing, or missing pass criteria?"

5. **Save:** Write `<slug>-TEST.md` to the spec folder.

6. **Implementation handoff (mandatory context break):**
   After saving, output this block verbatim — do not paraphrase it. Fill in
   all placeholders (`<feature-name>`, `<folder>`, `<slug>`) with the actual
   values for this session:

   ---
   > **All spec phases complete.**
   >
   > **Clear your context before implementing.** Starting implementation here risks carrying discovery noise and stale intermediate reasoning into a coding session. The spec artifacts are self-contained — a clean session is strictly better.
   >
   > Paste this prompt in a new session to start implementing:
   >
   > ```
   > Implement <feature-name> per the approved spec.
   >
   > All spec artifacts are at `specs/<folder>/`:
   > - `<slug>-SPEC.md` — EARS requirements, state model, contracts, anti-requirements
   > - `<slug>-PLAN.md` — component breakdown, ordered task checklist, key decisions
   > - `<slug>-TEST.md` — derived validation items (executable tests, benchmarks, audits, inspection-only proofs)
   >
   > Before writing any code:
   > 1. Read all three artifacts in full
   > 2. Read any testing or architecture guidelines referenced in the spec
   > 3. Confirm your understanding of the task order in PLAN.md
   >
   > Start with task T1 from PLAN.md unless I say otherwise.
   >
   > Rules:
   > - Follow the spec strictly — it is the contract
   > - If implementation reveals a contradiction or gap, stop and flag it before deviating
   > - Update the spec explicitly if requirements change; never silently deviate
   > - Every piece of code you write should be traceable to a spec requirement or invariant
   > ```
   ---

---

## File Naming & Output Location

All generated files go into a dedicated folder:

```
specs/<ticket-number>--<feature-slug>/
```

Examples:
- `specs/PROJ-1234--task-comments/`
- `specs/GH-42--user-auth-flow/`
- `specs/add-comment/` (no ticket number)

Use `kebab-case` for the feature slug. Create the `specs/` directory at the
project root if it doesn't exist.

### Output Files

- `<slug>-SPEC.md` — Context, discovery summary, EARS requirements, state model, contracts
- `<slug>-PLAN.md` — Implementation plan with task checklist
- `<slug>-TEST.md` — Validation strategy with all derived validation items

Save each file as its phase completes — don't wait for all phases.

---

## Drift Handling

Specs change. The point is to change them explicitly, not silently.

**Developer-initiated drift** — the developer asks for something that
contradicts the spec during implementation:
1. Flag the contradiction: "This changes R3. The spec says X, you're
   asking for Y."
2. Ask: "Want to update the spec, or is this a one-off exception?"
3. If updating: edit the EARS requirement in SPEC.md. Check if PLAN.md
   or TEST.md need cascading changes.

**Discovery drift** — implementation reveals something the spec didn't
anticipate:
1. Stop implementation on the affected task.
2. Propose a new EARS requirement or state transition.
3. Add to SPEC.md after developer approval.
4. Derive any new validation items.

Never silently deviate from the spec. If you catch yourself implementing
something the spec doesn't describe, stop and spec it first.

---

## Traceability Checks

Run these checks before approving each artifact:

- Every `R#` and every `INV-#` appears in `PLAN.md`.
- Every `R#` and every `INV-#` appears in `TEST.md` with at least one
  validation item.
- No PLAN task references observable behavior that is missing from the SPEC.
  If a task changes what a user, caller, or downstream system can observe,
  that behavior must already exist as a requirement, invariant, or
  anti-requirement.
- Contracts refine operations, but they do not introduce new observable
  behavior. If a contract clause affects externally visible behavior, promote
  it into a requirement or invariant first.
- If traceability is incomplete, stop and repair the artifact before moving
  to the next phase.

---

## Read-Only Rule

During all phases, the codebase is read-only. Use `Read`, `Glob`, `Grep`,
and read-only `Bash` commands to explore. The only files you create or modify
are the three spec artifacts in the `specs/` folder. Never modify source code,
tests, configs, or any other project files during spec generation.
