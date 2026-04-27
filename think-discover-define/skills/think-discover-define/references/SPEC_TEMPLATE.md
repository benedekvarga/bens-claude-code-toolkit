> **Ticket:** `[PROJ-1234]` (if applicable)
> **Date:** [YYYY-MM-DD]
> **Status:** Draft | Approved | Implemented

---

## Project Context

### Tech Stack (from `[source files]`)
- [language / framework / architecture]

### Conventions (from `[source files]`)
- [relevant patterns, naming, structure]

### Testing (from `[source files]`)
- [testing framework, conventions, coverage expectations]

---

## Discovery Summary

- **Project / Stack:** [project name, language, framework]
- **Area:** [module/screen/service]
- **Type:** New Feature | Modification | Bug Fix
- **State model tier:** None | Lightweight | Full
- **User goal:** [one sentence: "The user can ___" or "The system can ___"]
- **API surface:** [endpoints/mutations/services, or "none"]
- **Entry:** [how the user/caller reaches this feature]
- **Happy path:** [numbered steps of the ideal flow]
- **Exit:** [where the user/caller goes when done]
- **Error states:** [known error conditions]
- **Anti-requirements:** [what this does NOT do]
- **Validation focus:** [what must be validated]

---

## Requirements

### Happy Path

- **R1:** When [trigger], the [system] shall [response].
- **R2:** When [trigger], the [system] shall [response].

### Error Handling

- **R3:** If [bad condition], then the [system] shall [response].

### State-Dependent

- **R4:** While [precondition], the [system] shall [response].

### Constraints

- **R5:** The [system] shall [constraint].

---

## State Model

**Tier:** None | Lightweight | Full

<!-- Use the lightest tier that keeps behavior precise. -->
<!-- None: replace this section body with "No meaningful state beyond direct request/response behavior." -->
<!-- Lightweight: include Key States, Critical Transitions, and Invariants only. -->
<!-- Full: include States, Transitions, Invariants, and Impossible Transitions. -->

### Key States / States

| State | Description | Observable Behavior |
|-------|-------------|---------------------|
| `idle` | [description] | [what user/caller observes] |

### Critical Transitions / Transitions

| From | To | Trigger |
|------|----|---------|
| `idle` | `loading` | [trigger] |

### Invariants

- **INV-1:** [invariant]

<!-- Full tier only -->
### Impossible Transitions

- [from] → [to] — [reason]

---

## Contracts

### Operation: `[OperationName]`

**Preconditions:**
- [what must be true before calling]

**Postconditions (success):**
- [what must be true after successful completion]

**Postconditions (failure):**
- [what must be true after failure]

<!-- Contracts refine requirements and invariants. They may not introduce new observable behavior. -->

---

## Anti-Requirements

- This feature does NOT [thing 1].
- This feature does NOT [thing 2].

---

## Dependencies

- [ ] [dependency 1]
- [ ] [dependency 2]

---

## Open Questions

- [ ] [question 1]
  - Classification: Blocking | Assumption-backed | Non-blocking
