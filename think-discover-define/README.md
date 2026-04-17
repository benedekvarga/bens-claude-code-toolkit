# /think-discover-define

A Claude Code skill for spec-driven feature development. Front-loads thinking before code — guided discovery, formal-methods-flavored contracts, and a fully traceable pipeline from requirement to test.

---

## Installation

### Via Plugin Marketplace (Recommended)

Install directly from Claude Code in two commands:

```
/plugin marketplace add https://github.com/benedekvarga/bens-claude-code-toolkit
/plugin install think-discover-define@bens-claude-code-toolkit
```

Then use it by typing `/think-discover-define` in Claude Code.

---

## Usage

### Start a new feature

```
/think-discover-define
```

> "I want to add rate limiting to the API"

The skill will:

1. Silently orient itself — reads the codebase to understand existing structure before asking anything
2. Ask discovery questions across five layers: scope, behavior, edge cases, constraints, and testing approach
3. Generate a formal spec with EARS requirements and pre/post contracts
4. Produce an implementation plan with tasks mapped to requirements
5. Derive test cases directly from the spec

Each phase ends at a checkpoint — Claude stops and waits for your approval before continuing.

---

## Pipeline

```
ORIENT → DISCOVER → SPECIFY → PLAN → TEST
```

Each phase produces a persistent file in `specs/<ticket>--<feature-slug>/`. Each phase ends at a checkpoint — Claude stops and waits for approval before continuing.

| Phase | Output | What it produces |
|---|---|---|
| 0 · Orient | `<slug>-ORIENT.md` | Silent codebase recon before asking anything |
| 1 · Discover | `<slug>-DISCOVER.md` | Five-layer Q&A: scope, behavior, edge cases, constraints, testing |
| 2 · Specify | `<slug>-SPEC.md` | EARS requirements, state model, pre/post contracts |
| 3 · Plan | `<slug>-PLAN.md` | Task checklist mapped to R-numbers |
| 4 · Test | `<slug>-TEST.md` | Test cases derived directly from requirements |

Every requirement (R-number) traces to a task (T-number) and a test case (TC-number). If a requirement changes, the chain breaks visibly.

---

## Formal methods connections

The skill borrows structure from formal specification without mathematical notation or tooling.

| Skill construct | Origin |
|---|---|
| Pre/post condition contracts | Design-by-contract (Dafny, VDM, Eiffel) |
| State invariants + impossible transitions | TLA+, Alloy |
| EARS requirements | Semi-formal requirements engineering (Rolls-Royce airworthiness) |
| R → T → TC traceability | Refinement (B-Method, Event-B) |
| Test derivation from spec | Specification-based testing |

EARS sits at the formal/informal boundary: each pattern (When / While / If-Then) maps to a logical form and is directly translatable into a test — the same property formal specs are designed to have. What the skill omits: mathematical notation, model checking, temporal logic, and proof tooling. The formal influence is in the structure of thinking, not the machinery.

---

## When to skip it

Only trivial changes: typos, renames, single-line config tweaks. For everything else, run the full pipeline.
