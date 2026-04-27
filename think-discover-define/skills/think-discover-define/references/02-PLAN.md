# Phase 02-PLAN: Instructions

This file is loaded by the main skill at the start of the PLAN phase. It
contains all guidance for deriving an implementation plan from an approved spec.

Read `${CLAUDE_SKILL_DIR}/references/EXAMPLE_PLAN.md` for a worked example.

---

## Purpose

Transform the approved SPEC into a concrete, actionable implementation plan.
This is where tech-stack details appear — the spec describes behavior, the
plan maps it to specific files, components, and patterns in the codebase.

---

## Step 1: Analyze Spec and Codebase

Re-read the approved SPEC. Then explore the codebase to understand:

- Which existing files need modification vs. new files needed
- Existing patterns for similar features (naming, structure, state management)
- Utilities, helpers, or base classes that should be reused
- Testing patterns and test file placement conventions
- Which `R#` and `INV-#` each planned change will satisfy

---

## Step 2: Component Breakdown

List which files/modules need changes:

| File | Change Type | Requirements |
|------|------------|--------------|
| `path/to/file` | New / Modify / Delete | R1, R3 |

For new files, suggest a path that follows existing project conventions.
For modifications, note what specifically changes.
Do not cite vague buckets like "State Model" or "Contracts" in the
Requirements column when a specific `R#` or `INV-#` is available.

---

## Step 3: Task Checklist

Break work into small, independently verifiable tasks. Each task:

- Has a clear "done when" condition tied to a spec requirement
- Is small enough to implement in one prompt
- References the specific EARS requirements or invariants it fulfills
- Cross-references validation items from Phase 03-TEST (added after that phase)

Format:
```markdown
- [ ] **T1: [short title]** — [what to do]. Fulfills: R1, R3.
  Validation: V-1, V-4.
  Done when: [specific verifiable condition]
```

Group tasks logically:
- **Foundation** — models, types, API clients (do first)
- **Core flow** — main feature logic (sequential, depends on foundation)
- **UI / Integration** — wiring it together (can sometimes parallel core)
- **Polish** — pagination, edge cases, final touches (after core)

---

## Step 4: Suggested Order

Propose an implementation order based on dependencies. Explicitly note which
tasks are independent and can be done in any order:

```
T1 → T2 → T3 (sequential, each depends on previous)
       ↘ T4 → T5 (parallel track, can start after T1)
```

---

## Step 5: Key Decisions

Flag implementation decisions NOT covered by the spec that need developer
input during coding. These are typically tech-stack-specific choices:

- State management approach (existing store vs. new)
- Optimistic update strategy
- Pagination approach (cursor vs. offset)
- Naming conventions for new files/types
- Any choice where the codebase has multiple precedents

Present each with your recommended answer based on codebase patterns.

---

## PLAN Lint Checklist

Before presenting the PLAN for approval, verify:

- Every `R#` and every `INV-#` appears in at least one task or file row.
- No task introduces observable behavior missing from the SPEC.
- If a plan item depends on a contract clause, that clause traces back to a
  requirement or invariant.
- Key decisions are implementation choices, not hidden scope changes.
- Tasks are small enough to complete and verify independently.
