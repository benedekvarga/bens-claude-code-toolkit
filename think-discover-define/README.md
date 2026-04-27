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

1. Scan the codebase for existing docs, architecture decisions, and relevant context before asking anything
2. Run interactive discovery — questions with recommended answers based on codebase analysis
3. Generate a formal spec (01-SPEC) with EARS requirements and pre/post contracts
4. Derive an implementation plan (02-PLAN) with tasks mapped to requirements
5. Produce a validation strategy (03-TEST) with test cases derived directly from the spec

Each phase ends at a checkpoint — the skill stops and waits for your approval before continuing.

---

## Pipeline

```
01-SPEC  ──►  02-PLAN  ──►  03-TEST
```

Each phase produces a persistent file in `specs/<feature-slug>/`. Each phase ends at a checkpoint — the skill stops and waits for approval before continuing.

| Phase | Output | What it produces |
|---|---|---|
| 1 · Spec | `01-SPEC.md` | Discovery Q&A, EARS requirements, state model, pre/post contracts |
| 2 · Plan | `02-PLAN.md` | Task checklist with exact file paths, mapped to R-numbers |
| 3 · Test | `03-TEST.md` | Test cases derived directly from requirements |

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
