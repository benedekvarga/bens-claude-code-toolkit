# Implementation Plan: Add Comment to Task

> Derived from: `SPEC.md`
> Tasks reference spec requirements as R1, R2, etc.
> **Note:** File paths and patterns below are project-specific — adapt to your
> codebase's conventions. This example uses generic paths as placeholders.

---

## Component Breakdown

| File | Change Type | Requirements |
|------|------------|--------------|
| `task-detail/comments/CommentList` | New — display comment list | R3, R9, R11, INV-3 |
| `task-detail/comments/CommentInput` | New — input field + send | R1, R4, R8, R10 |
| `task-detail/comments/CommentItem` | New — single comment display | R1, R5 (error state) |
| `task-detail/comments/commentState` | New — state management | State Model |
| `api/comments` | New — API client for comment endpoints | Contracts |
| `task-detail/TaskDetail` | Modify — integrate comments section | R3 |
| `task-detail/comments/commentState.test` | New — unit tests | TEST.md |

---

## Task Checklist

### Foundation (do first)

- [ ] **T1: Define comment state model** — Create the state representation with cases: `loading`, `loaded`, `empty`, `submitting`, `submitError`, `disabled`. Include the comments collection and pending comment data. Fulfills: State Model. Tests: TC-12, TC-13, TC-14, TC-15.
  Done when: State type compiles/passes type check with all states represented.

- [ ] **T2: Implement API client for comments** — `fetchComments(taskId)` and `createComment(taskId, text)`. Handle 200, 403, 404, and network errors. Fulfills: Contracts (CreateComment, FetchComments).
  Done when: API calls return typed responses; error cases are distinguished.

### Core Flow (sequential — depends on T1, T2)

- [ ] **T3: Implement comment fetching** — On task detail load, fetch comments. Transition from `loading` → `loaded` or `empty`. Populate list. Fulfills: R3, R9. Tests: TC-3, TC-13.
  Done when: Opening a task detail shows its comments (or empty state).

- [ ] **T4: Implement comment submission — happy path** — Validate input, show optimistic "sending" comment, call API, on success assign permanent ID and clear input. Fulfills: R1, R2, R8, INV-2. Tests: TC-1, TC-2, TC-12.
  Done when: Submitting a comment shows it immediately as "sending", then as confirmed. Input clears on success.

- [ ] **T5: Implement input validation** — Prevent empty/whitespace-only submission. Show inline message. Enforce 2000 char limit with counter. Fulfills: R4, R10. Tests: TC-4, TC-5, TC-6, TC-7.
  Done when: Empty submit shows validation error. Character counter appears at 1800+ characters.

- [ ] **T6: Implement submission error handling** — On network error: mark comment as failed, show retry. On 404: transition to `disabled` with "task deleted" message. On 403: transition to `disabled` with "no access" message. Fulfills: R5, R6, R7. Tests: TC-8, TC-9, TC-10, TC-11.
  Done when: Each error scenario shows the correct UI state and message.

### UI Integration (can parallel with T4-T6)

- [ ] **T7: Build CommentList component** — Renders comments in chronological order. Shows loading indicator during fetch. Shows empty state when no comments. Auto-scrolls to bottom on new comment. Fulfills: R3, R9, INV-3. Tests: TC-3, TC-13, TC-16.
  Done when: List renders correctly in all states.

- [ ] **T8: Build CommentInput component** — Text input with send button. Character counter. Disabled state when `submitting` or `disabled`. Fulfills: R1, R8, R10. Tests: TC-6, TC-7, TC-12, TC-15.
  Done when: Input works in all states, send button reflects current state.

- [ ] **T9: Build CommentItem component** — Displays author, timestamp, text. "Sending" variant with indicator. Error variant with retry action. Fulfills: R1, R5. Tests: TC-8, TC-9.
  Done when: All three variants render correctly.

### Integration & Polish (after core)

- [ ] **T10: Integrate comments into TaskDetail** — Add comments section below task content. Trigger comment fetch on load. Fulfills: R3.
  Done when: Task detail shows comments section in correct position.

- [ ] **T11: Implement pagination/lazy loading** — Load more comments when scrolling or via "Load more" action. Fulfills: R11.
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
   "sending" state (R8). Decide whether to use a temporary local ID or a
   placeholder pattern. What does the existing codebase do for optimistic updates?
2. **Pagination approach:** Cursor-based vs offset-based? What does the API support?
3. **Comment timestamp format:** Relative ("2 minutes ago") or absolute
   ("Apr 12, 2026 3:45 PM")? Check existing date formatting patterns.
4. **Double-submit prevention:** Use a flag in state, or debounce the send action?
   Pick the simpler approach that matches existing patterns.
