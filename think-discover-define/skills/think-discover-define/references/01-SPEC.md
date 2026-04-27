# Phase 01-SPEC: Instructions

This file is loaded by the main skill at the start of the SPEC phase. It
contains all guidance for document discovery, interactive discovery, and
specification writing.

---

## Step 1: Document Discovery

Before asking the developer anything, scan the project for relevant documents.
This builds context and prevents asking questions the code already answers.

### What to Scan

Search broadly — do NOT assume a fixed folder structure:

- **LLM context files:** `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`,
  `AGENTS.md`, `.ai/`, `.prompts/`
- **Architecture docs:** `ARCHITECTURE.md`, `docs/architecture*`, `design/`, `ADR/`
- **API docs:** OpenAPI/Swagger specs, `API.md`, `docs/api*`, GraphQL schemas
- **Testing docs:** `TESTING.md`, `docs/testing*`, `test/README*`
- **Existing specs:** Markdown files containing EARS-style requirements or
  acceptance criteria — scan `docs/`, `specs/`, `design/`, root-level markdown
- **Project config:** `package.json`, `Cargo.toml`, `pyproject.toml`, `build.gradle`,
  `Package.swift`, etc. for tech stack signals
- **README files:** Root and module-level

### Synthesis Format

Produce a short Project Context summary with full file paths:

```markdown
## Project Context

### Tech Stack (from `./package.json`, `./CLAUDE.md`)
- TypeScript / Next.js 14 / App Router
- Zustand for state, Tailwind for styling

### Conventions (from `./CLAUDE.md` lines 12-45, `./docs/ARCHITECTURE.md`)
- Feature modules in `src/features/<name>/`
- Co-located tests in `__tests__/` subdirectories

### Testing (from `./docs/TESTING.md`)
- Unit tests for business logic, integration for API layer
- Mock API calls with msw
```

If no project documents are found, note this and proceed with code analysis.

---

## Step 2: Decision-Tree Discovery

Explore the feature's requirements through depth-first questioning. This
replaces a rigid layered questionnaire with an adaptive conversation that
follows the feature's natural shape.

### How to Explore

1. **Walk each branch of the decision tree one at a time.** Start with scope,
   then follow whichever branch matters most for the feature.

2. **For every question, propose a recommended answer.** Base it on what you
   found in the codebase, project docs, or common patterns for this type of
   feature. Example:
   > "This touches the task detail module. Based on the existing pattern in
   > `src/features/tasks/`, I'd recommend co-locating the new component there.
   > Does that sound right, or should it go elsewhere?"

3. **If the codebase answers a question, state the answer and move on.** Don't
   ask the developer to confirm obvious facts ("What framework is this?" when
   you can see it's Next.js). Do confirm non-obvious interpretations.

4. **Use AskUserQuestion for categorical choices.** When a question has 2-4
   discrete options, present them as structured choices. Use free-text prompts
   for open-ended questions.

5. **Push back on vague answers.** If the developer says "it should handle
   errors gracefully," ask: "What does gracefully mean here? Retry? Show a
   banner? Fail silently? Redirect?" Don't accept hand-wavy responses for
   things that will become EARS requirements.

6. **Resolve dependencies between decisions.** If the answer to question A
   affects what you need to ask about B, resolve A first. Don't ask B with
   assumptions.

### Core Topics to Cover

These are not layers to march through — they're topics to ensure nothing is
missed. The order follows the feature's shape:

**Scope & Context:**
- What area does this touch? Which module, screen, service, domain?
- What type of change? New feature / modification / bug fix / refactor
- One-sentence user goal: "The user can ___" or "The system can ___"

**API & Data:**
- API calls, mutations, endpoints, or external services involved?
- What data must exist before this works? What does it create/modify/delete?

**Flow:**
- Entry points — how does the user/caller reach this?
- Happy path — walk through the ideal flow step by step
- Exit points — where does the user/caller go when done?

**Error & Edge Cases:**
- What can go wrong? Network failures, invalid input, empty states, permission
  issues, race conditions. For each: what should happen?
- Loading / async states — what does the user experience while waiting?
- Degraded operation — offline, partial data, degraded mode?
- Concurrency — can multiple actors interact simultaneously?

**Proactive edge case generation:** Based on the feature type and codebase
patterns, generate a list of likely edge cases. Present them:
> "Based on what you've described, here are edge cases I'd expect. For each:
> in scope, out of scope, or 'let's discuss'."

**Boundaries:**
- Anti-requirements — what does this feature explicitly NOT do?
- Performance constraints — latency, throughput, memory, size limits?
- Accessibility / compliance / i18n considerations?
- Existing patterns to match — reference a similar feature for consistency?
- State complexity — no meaningful state, lightweight state, or full state
  machine?

**Validation Intent:**
- What's critical to validate?
- What's NOT worth validating?
- Existing test, benchmark, audit, or inspection patterns to match?

If testing docs were found during document discovery, present their guidance:
> "Your testing docs at `./docs/TESTING.md` prescribe: [summary]. I'll use
> these as the baseline. Override anything for this feature?"

### Handling Partial Input

When the developer provides a ticket, doc, or detailed prompt:

1. Extract all available answers.
2. Present a pre-populated discovery summary with recommended answers.
3. Ask the developer to verify, then probe gaps depth-first.
4. Don't skip topics — even comprehensive input has gaps in edge cases
   and error handling.

If the developer says "skip discovery" or "I know what I want," present
what you have and flag the gaps. Let them decide whether to fill them or
proceed with gaps noted as Open Questions.

---

## Step 3: Write the Specification

Transform discovery into a precise, testable spec. Read the template at
`${CLAUDE_SKILL_DIR}/references/SPEC_TEMPLATE.md` and the example at
`${CLAUDE_SKILL_DIR}/references/EXAMPLE_SPEC.md`.

### EARS Requirements

Every behavioral requirement MUST use EARS notation:

| Pattern | Keyword | Template | Use When |
|---------|---------|----------|----------|
| Ubiquitous | (none) | The `<system>` shall `<response>` | Always-true constraints |
| Event-driven | **When** | **When** `<trigger>`, the `<system>` shall `<response>` | User actions, API calls |
| State-driven | **While** | **While** `<precondition>`, the `<system>` shall `<response>` | Mode-dependent behavior |
| Unwanted | **If/Then** | **If** `<bad thing>`, **then** the `<system>` shall `<response>` | Error handling |
| Complex | Combined | **While** `<pre>`, **when** `<trigger>`, the `<system>` shall `<response>` | Multi-condition |

**Rules:**
- Each requirement is ONE sentence.
- Use domain language from the codebase and the developer's own words.
- Be specific about the response — "display an error banner with the message
  from the API" not "show an error."
- Number sequentially: R1, R2, R3...
- Group by: Happy Path, Error Handling, State-dependent, Constraints.

### State Model Tiers

Pick the lightest tier that still makes the behavior precise:

**Tier: None**
- Use for trivial request/response behavior with no meaningful mode changes.
- Write: `No meaningful state beyond direct request/response behavior.`

**Tier: Lightweight**
- Use for features with 2-4 important states, guards, or async modes, but not
  a full state machine.
- Include:
  - **Key States:** brief list with observable behavior
  - **Critical Transitions:** only the transitions that matter to behavior
  - **Invariants (INV-n):** 1-3 properties that must always hold

**Tier: Full**
- Use for multi-step, async, concurrent, or failure-heavy behavior where the
  state machine itself is part of the contract.
- Include:
  - **States:** all meaningful states with observable behavior
  - **Transitions:** From → To → Trigger
  - **Invariants (INV-n):** properties that must always hold
  - **Impossible Transitions:** state changes that must never happen

### Contracts (Pre/Post Conditions)

For each significant operation (API call, database write, state transition):

```markdown
### Operation: `OperationName`

**Preconditions:**
- [what must be true before calling]

**Postconditions (success):**
- [what must be true after successful completion]

**Postconditions (failure):**
- [what must be true after failure — typically: state is preserved]
```

Skip trivial getters and pure reads.

**Rule:** Contracts may refine operations, guard conditions, and data effects,
but they may NOT introduce new observable behavior. If a contract clause would
change what a user, caller, or downstream system can observe, add or update
the relevant `R#` or `INV-#` first.

### Anti-Requirements

Explicit list of what this feature does NOT do. Just as important as
requirements — prevents scope creep and gives clear boundaries.

### Dependencies

What must exist before this feature can be built: APIs, migrations, feature
flags, other features. Use checkboxes for tracking.

### Open Questions

Anything unresolved after discovery. These block implementation, not the spec.
Mark resolved with `[x]` as answers come in.

Classify each question:
- **Blocking:** must be resolved before PLAN
- **Assumption-backed:** may proceed if the assumption is written explicitly
- **Non-blocking:** useful follow-up, but does not affect the contract

---

## SPEC Lint Checklist

Before presenting the SPEC for approval, verify:

- Every observable behavior appears in `Requirements`, `Invariants`, or
  `Anti-Requirements`.
- The chosen state-model tier is explicit: none, lightweight, or full.
- Every contract clause refines existing requirements/invariants and does not
  introduce new behavior.
- Every unresolved question is labeled as blocking, assumption-backed, or
  non-blocking.
- The spec is small enough to describe one shippable unit.

---

## Discovery Summary Format

Include at the top of the SPEC, after the Project Context:

```markdown
## Discovery Summary

- **Project / Stack:** [project name, language, framework]
- **Area:** [module/screen/service]
- **Type:** [new feature | modification | bugfix]
- **State model tier:** None | Lightweight | Full
- **User goal:** [one sentence]
- **API surface:** [endpoints/mutations/services, or "none"]
- **Entry:** [how user/caller gets here]
- **Happy path:** [numbered steps]
- **Exit:** [where user/caller goes after]
- **Error states:** [list]
- **Anti-requirements:** [what this does NOT do]
- **Validation focus:** [what must be validated]
```
