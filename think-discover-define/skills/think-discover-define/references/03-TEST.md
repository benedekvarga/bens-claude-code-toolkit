# Phase 03-TEST: Instructions

This file is loaded by the main skill at the start of the TEST phase. It
contains all guidance for deriving validation items from an approved spec.

Read `${CLAUDE_SKILL_DIR}/references/EXAMPLE_TEST.md` for a worked example.

---

## Purpose

Derive a validation strategy directly from the EARS requirements and
invariants. Every validation item traces back to the spec. No orphan
validation. No validation for trivial things unless the developer explicitly
wants them.

If the project has testing docs (found during Phase 01-SPEC discovery), the
validation strategy MUST conform to those conventions.

---

## Validation Types

Use the narrowest validation type that proves the requirement:

- **Executable test** — unit, integration, end-to-end, contract test
- **Benchmark** — latency, throughput, memory, startup, bundle size
- **Audit/manual check** — accessibility audit, UX review, operator runbook,
  cross-device check, one-time validation that is not sensibly automated yet
- **Inspection-only proof** — code/config/schema inspection where runtime
  execution is unnecessary or lower signal than static inspection

Default to **Executable test**. Switch away from it only when another type is
more honest, cheaper, or better aligned to the requirement.

Examples:
- `Executable test` — "When the user submits a valid comment, the system shall display it in the list."
- `Benchmark` — "The import pipeline shall process 10k rows within 30 seconds."
- `Audit/manual check` — "The dialog shall be fully keyboard navigable and screen-reader labeled."
- `Inspection-only proof` — "All writes shall pass through the feature-flagged adapter."

---

## Validation Derivation

For each requirement or invariant, derive validation items using this mapping:

| EARS Pattern | Validation Structure |
|-------------|----------------------|
| **When** `<trigger>`, shall `<response>` | Setup: trigger condition. Assert: response. |
| **While** `<pre>`, shall `<response>` | Setup: establish precondition. Assert: response holds. |
| **If** `<bad>`, **then** shall `<response>` | Setup: cause bad condition. Assert: response. |
| **Complex** (While + When) | One validation item per condition combination. |
| **Invariant** (INV-n) | Exercise the system or inspect the implementation. Assert: invariant holds. |
| **Impossible transition** | Setup: source state. Action: attempt trigger. Assert: transition blocked. |

**Every requirement and invariant needs validation.** Do not assign priority
tiers (P0/P1/P2) or suggest validation is optional. If a requirement is in the
spec, it gets validated. The developer descopes at the spec level, not the
validation level.

---

## Validation Naming Convention

Give every validation item a stable `V-#` ID.

If the validation type is **Executable test**, also include a concrete test
name. Default format (adapt casing to project convention):

```
test_[subject]_[condition]_[expectedOutcome]
```

- Reads as a sentence: "test [that] subject [when/with] condition [then] expected outcome"
- No `testThat`, `should`, `verify` prefixes — just the behavior
- Under ~60 characters
- Adapt casing to project convention (camelCase, snake_case, etc.)

If the project has an existing test naming convention, defer to it.

---

## Validation Item Format

```markdown
### V-1: [short title] → R1

**Type:** Executable test | Benchmark | Audit/manual check | Inspection-only proof
**Concrete artifact:** `test_functionName_condition_outcome` | `none`
**Method:** [how this will be executed or inspected]
**Pass criteria:** [what must be true to pass]
```

Group validation items by category:
- Executable tests
- Benchmarks
- Audit/manual checks
- Inspection-only proofs

---

## What NOT to Validate

Explicitly list exclusions based on discovery answers. Common exclusions:

- Pure layout/styling (unless accessibility-critical)
- Simple property forwarding with no logic
- Framework/library behavior (not your code)
- Boilerplate or generated code
- Behavior already covered by the project's shared test infrastructure

---

## Validation Placement

Match existing test patterns in the codebase for:
- File location (co-located vs. separate test directory)
- Mock setup patterns
- Async test patterns
- Fixture data conventions

For benchmarks, audits, or inspection-only proofs, note where the result
belongs: benchmark suite, checklist, engineering note, PR checklist, etc.

Check the codebase for existing patterns before drafting validation items.

---

## Cross-Reference with PLAN

After the TEST phase is approved, go back and add validation references to the
PLAN's task checklist:

```markdown
- [ ] **T4: [title]** — [what to do]. Fulfills: R1, R3. Validation: V-1, V-4.
```

This ensures every task knows which validation items verify it.

---

## TEST Lint Checklist

Before presenting the TEST artifact for approval, verify:

- Every `R#` and every `INV-#` has at least one `V-#` item.
- Every `V-#` references behavior that already exists in the SPEC.
- Validation type matches the requirement honestly; do not force everything
  into executable tests.
- If a contract clause needs validation but no matching requirement/invariant
  exists, update the SPEC first.
