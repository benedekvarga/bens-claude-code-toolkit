# Feature Spec: [Feature Name]

> **Ticket:** `[PROJ-1234]` (if applicable)
> **Branch:** `[feature-branch-name]`
> **Date:** [YYYY-MM-DD]
> **Status:** Draft | In Review | Approved | Implemented

---

## Discovery Summary

- **Project / Stack:** [project name, language, framework]
- **Area:** [module/screen/service]
- **Type:** New Feature | Modification | Bug Fix
- **User goal:** [one sentence: "The user can ___" or "The system can ___"]
- **API surface:** [endpoints/mutations/services involved, or "none"]
- **Entry:** [how the user/caller reaches this feature]
- **Happy path:** [numbered steps of the ideal flow]
- **Exit:** [where the user/caller goes when done, or resulting system state]
- **Error states:** [known error conditions]
- **Anti-requirements:** [what this does NOT do]
- **Test focus:** [what must be tested]

---

## Requirements

### Happy Path

<!-- Event-driven and state-driven requirements for the normal flow.
     Use EARS notation: "When [trigger], the [system] shall [response]."
     Be specific about the response — not "show an error" but "display
     an inline error message with the text from the API response." -->

- **R1:** When [trigger], the [system] shall [response].
- **R2:** When [trigger], the [system] shall [response].

### Error Handling

<!-- If/Then requirements for failure and edge cases.
     Cover: network errors, invalid input, missing data, permission
     failures, and any domain-specific error conditions. -->

- **R3:** If [bad condition], then the [system] shall [response].
- **R4:** If [bad condition], then the [system] shall [response].

### State-Dependent

<!-- While requirements for mode- or state-dependent behavior.
     These describe what the system does differently based on what
     state it's currently in. -->

- **R5:** While [precondition], the [system] shall [response].

### Constraints

<!-- Ubiquitous requirements — always true regardless of state.
     Performance limits, character limits, ordering guarantees, etc. -->

- **R6:** The [system] shall [constraint].

---

## State Model

<!-- Only needed if the feature has more than two meaningful states.
     For simple CRUD with no async or multi-step flow, skip this section
     and note "No complex state" instead. -->

### States

| State | Description | Observable Behavior |
|-------|-------------|---------------------|
| `idle` | [description] | [what user/caller observes] |
| `loading` | [description] | [what user/caller observes] |
| `loaded` | [description] | [what user/caller observes] |
| `error` | [description] | [what user/caller observes] |

### Transitions

| From | To | Trigger |
|------|----|---------|
| `idle` | `loading` | [trigger] |
| `loading` | `loaded` | [trigger] |
| `loading` | `error` | [trigger] |

### Invariants

<!-- Properties that must ALWAYS hold, regardless of state.
     Think: data consistency, ordering guarantees, things that
     should never be violated even during transitions. -->

- **INV-1:** [invariant]
- **INV-2:** [invariant]

### Impossible Transitions

<!-- State changes that must NEVER happen.
     These are defensive — they catch bugs where the system
     enters a state it shouldn't be able to reach. -->

- [from] → [to] — reason
- [from] → [to] — reason

---

## Contracts

<!-- Define pre/post conditions for each significant operation:
     API calls, database writes, state-changing commands, and any
     side-effect-producing interactions. Skip trivial getters. -->

### Operation: `[OperationName]`

**Preconditions:**
- [what must be true before calling]

**Postconditions (success):**
- [what must be true after successful completion]

**Postconditions (failure):**
- [what must be true after failure — typically: state is preserved]

<!-- Repeat this block for each significant operation. -->

---

## Anti-Requirements

<!-- Explicit boundaries. These are just as important as requirements —
     they prevent scope creep and give the AI clear stop signals.
     Be specific: "does NOT support X" is better than "out of scope." -->

- This feature does NOT [thing 1].
- This feature does NOT [thing 2].
- This feature does NOT [thing 3].

---

## Dependencies

<!-- What must exist or be true before this feature can be built?
     Include: APIs that must be available, data migrations, feature
     flags, other features that must ship first, third-party services. -->

- [ ] [dependency 1]
- [ ] [dependency 2]

---

## Open Questions

<!-- Anything unresolved after discovery. Must be answered before
     implementation begins. Mark resolved questions with [x]. -->

- [ ] [question 1]
- [ ] [question 2]

---

## File Manifest

<!-- List source files this spec references. Used for staleness detection.
     Update this when the spec changes or during drift resolution.
     Format: file path | last known hash/date | which requirements reference it -->

| File | Last Known State | Referenced By |
|------|-----------------|---------------|
| `[path]` | `[git hash or date]` | R1, R2 |

---

## Change Log

<!-- Append entries here when the spec changes during implementation.
     Format: [date] R# updated/added: description of change and reason.
     Example:
     - **[2026-04-15]** R3 updated: changed from "display toast" to "display
       inline error" per developer feedback during T4 implementation.
     - **[2026-04-16]** R12 added: API returns 429 on rate limit, added
       retry-with-backoff requirement.
-->
