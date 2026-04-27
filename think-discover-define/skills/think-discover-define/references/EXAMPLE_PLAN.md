# Implementation Plan: Add Comment to Task

> Derived from: `SPEC.md`
> Tasks reference spec requirements as R1, R2, etc.
> **Note:** File paths and patterns below are project-specific — adapt to your
> codebase's conventions. This example uses generic paths as placeholders.

---

## Component Breakdown

| File | Change Type | Requirements |
|------|------------|--------------|
| `task-detail/comments/CommentList` | New — display comment list, loading, load error, and pagination states | R3, R8, R10, R12, INV-3 |
| `task-detail/comments/CommentInput` | New — input field + send | R1, R4, R9, R11 |
| `task-detail/comments/CommentItem` | New — single comment display | R1, R5 (error state) |
| `task-detail/comments/commentState` | New — state management | R5, R8, R9, R10, INV-1, INV-2, INV-3 |
| `api/comments` | New — API client for comment endpoints | R1, R2, R3, R5, R6, R7, R8, R12 |
| `task-detail/TaskDetail` | Modify — integrate comments section | R3, R8, R10 |
| `task-detail/comments/commentState.test` | New — validation harness for state logic | R5, R8, R9, R10, INV-1, INV-2, INV-3 |

---

## Task Checklist

### Foundation (do first)

- [ ] **T1: Define comment state model** — Create the state representation with cases: `loading`, `loaded`, `empty`, `loadError`, `submitting`, `submitError`, `disabled`. Include the comments collection and pending comment data. Fulfills: R5, R8, R9, R10, INV-1, INV-2, INV-3. Validation: V-9, V-10, V-11, V-14, V-15, V-16, V-17, V-18.
  Done when: State type compiles/passes type check with all states represented.

- [ ] **T2: Implement API client for comments** — `fetchComments(taskId)` and `createComment(taskId, text)`. Handle 200, 403, 404, and network errors. Fulfills: R1, R2, R3, R5, R6, R7, R8, R12. Validation: V-1, V-2, V-3, V-6, V-7, V-8, V-12.
  Done when: API calls return typed responses; error cases are distinguished.

### Core Flow (sequential — depends on T1, T2)

- [ ] **T3: Implement comment fetching** — On task detail load, fetch comments. Transition from `loading` → `loaded`, `empty`, or `loadError`. Populate list and allow retry from `loadError`. Fulfills: R3, R8, R10. Validation: V-3, V-8, V-10, V-13.
  Done when: Opening a task detail shows its comments (or empty state).

- [ ] **T4: Implement comment submission — happy path** — Validate input, show optimistic "sending" comment, call API, on success assign permanent ID and clear input. Fulfills: R1, R2, R9, INV-2. Validation: V-1, V-2, V-9, V-17.
  Done when: Submitting a comment shows it immediately as "sending", then as confirmed. Input clears on success.

- [ ] **T5: Implement input validation** — Prevent empty/whitespace-only submission. Show inline message. Enforce 2000 char limit with counter. Fulfills: R4, R11. Validation: V-4, V-5, V-14, V-15.
  Done when: Empty submit shows validation error. Character counter appears at 1800+ characters.

- [ ] **T6: Implement submission error handling** — On network error: mark comment as failed, show retry. On 404: transition to `disabled` with "task deleted" message. On 403: transition to `disabled` with "no access" message. Fulfills: R5, R6, R7, INV-2. Validation: V-6, V-7, V-8, V-16, V-17.
  Done when: Each error scenario shows the correct UI state and message.

### UI Integration (can parallel with T4-T6)

- [ ] **T7: Build CommentList component** — Renders comments in chronological order. Shows loading and load-error states. Shows empty state when no comments. Supports retry and pagination/lazy loading affordance. Fulfills: R3, R8, R10, R12, INV-3. Validation: V-3, V-8, V-12, V-13, V-18.
  Done when: List renders correctly in all states.

- [ ] **T8: Build CommentInput component** — Text input with send button. Character counter. Disabled state when `submitting` or `disabled`. Fulfills: R1, R9, R11. Validation: V-1, V-9, V-14, V-15.
  Done when: Input works in all states, send button reflects current state.

- [ ] **T9: Build CommentItem component** — Displays author, timestamp, text. "Sending" variant with indicator. Error variant with retry action. Fulfills: R1, R5, R9. Validation: V-1, V-6, V-9.
  Done when: All three variants render correctly.

### Integration & Polish (after core)

- [ ] **T10: Integrate comments into TaskDetail** — Add comments section below task content. Trigger comment fetch on load and preserve task-detail usability during comments loading or load error. Fulfills: R3, R8, R10. Validation: V-3, V-8, V-13.
  Done when: Task detail shows comments section in correct position.

- [ ] **T11: Implement pagination/lazy loading** — Load more comments when scrolling or via "Load more" action. Fulfills: R12. Validation: V-12.
  Done when: Tasks with 50+ comments load incrementally.

---

## Suggested Order

```
T1 → T2 → T3 → T4 → T5 → T6 (sequential core)
              ↘ T7 → T8 → T9  (UI, can start after T1)
                                T10 → T11 (integration, after both tracks)
```

T7-T9 can happen in parallel with T4-T6 if working in separate files.

---

## Key Decisions (not covered by spec)

1. **Optimistic update strategy:** Spec says show the comment immediately with
   "sending" state (R9). Decide whether to use a temporary local ID or a
   placeholder pattern. What does the existing codebase do for optimistic updates?
2. **Pagination approach:** Cursor-based vs offset-based? What does the API support?
3. **Retry placement:** Put the comments-load retry inside the empty/error shell
   or as a section-level banner action? Match existing failure-state patterns.
4. **Double-submit prevention:** Use a flag in state, or debounce the send action?
   Pick the simpler approach that matches existing patterns.
