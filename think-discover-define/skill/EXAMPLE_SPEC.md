# Feature Spec: Add Comment to Task

> **Branch:** `feature/task-comments`
> **Date:** 2026-04-12
> **Status:** Approved

---

## Discovery Summary

- **Project / Stack:** Acme Task Manager (stack-independent — this spec works for any client or backend)
- **Area:** Task Detail module, comments subsystem
- **Type:** New Feature
- **User goal:** The user can add a text comment to a task to communicate with collaborators.
- **API surface:** New endpoint: `POST /tasks/{taskId}/comments`. Existing: `GET /tasks/{taskId}` (already returns task data, will need to include comments).
- **Entry:** User is viewing a task detail screen/page
- **Happy path:** 1. User views task → 2. Types comment in input field → 3. Taps/clicks "Send" → 4. Comment appears in the comment list with author and timestamp → 5. Input field clears
- **Exit:** User remains on task detail. Comment is persisted and visible to all collaborators.
- **Error states:** Network failure on send, empty comment submission, task deleted while commenting, user loses edit permission
- **Anti-requirements:** Does NOT support editing or deleting comments. Does NOT support attachments or rich text. Does NOT support @mentions or notifications.
- **Test focus:** Comment submission flow, error handling, input validation, optimistic update behavior

---

## Requirements

### Happy Path

- **R1:** When the user submits a non-empty comment, the system shall send the comment to the API and display it in the comment list with the author name and a timestamp.
- **R2:** When the API confirms the comment was created, the system shall clear the input field and assign the comment a permanent ID from the server response.
- **R3:** When the task detail is loaded, the system shall display all existing comments in chronological order (oldest first).

### Error Handling

- **R4:** If the user submits an empty or whitespace-only comment, then the system shall prevent the API call and display an inline validation message.
- **R5:** If the comment submission fails due to a network error, then the system shall display an error indicator on the failed comment and offer a "Retry" action.
- **R6:** If the comment submission fails because the task no longer exists (404), then the system shall display a message that the task was deleted and disable further comment input.
- **R7:** If the comment submission fails because the user lost permission (403), then the system shall display a message that the user no longer has access and disable further comment input.

### State-Dependent

- **R8:** While a comment is being submitted (API call in flight), the system shall show the comment in the list as "sending" and disable the send button.
- **R9:** While the comment list is loading, the system shall display a loading indicator in the comments section (not block the full task detail).

### Constraints

- **R10:** The system shall enforce a maximum comment length of 2000 characters and display a character counter when the user is within 200 characters of the limit.
- **R11:** The comment list shall support pagination or lazy loading if there are more than 50 comments.

---

## State Model

### States

| State | Description | Observable Behavior |
|-------|-------------|---------------------|
| `loading` | Comments are being fetched | Loading indicator in comments section; task detail is still visible |
| `loaded` | Comments fetched successfully | Comment list and input field visible |
| `empty` | Loaded but no comments exist | Empty state message ("No comments yet") and input field visible |
| `submitting` | A comment is being sent | New comment shows as "sending"; send button disabled |
| `submitError` | Comment failed to send | Failed comment shows error indicator with retry option |
| `disabled` | Task deleted or user lost access | Input field disabled; message explains why |

### Transitions

| From | To | Trigger |
|------|----|---------|
| `loading` | `loaded` | API returns comments (1+) |
| `loading` | `empty` | API returns zero comments |
| `loaded` / `empty` | `submitting` | User submits a valid comment |
| `submitting` | `loaded` | API confirms creation |
| `submitting` | `submitError` | API returns error |
| `submitError` | `submitting` | User taps "Retry" |
| `submitError` | `loaded` | User dismisses the failed comment |
| any | `disabled` | API returns 404 or 403 |

### Invariants

- **INV-1:** Successfully submitted comments are never lost from the UI — once the API confirms, the comment persists in the list even if subsequent loads fail.
- **INV-2:** The input field content is preserved across failed submissions — the user should never have to re-type their comment.
- **INV-3:** Comment ordering is always chronological (oldest first). New comments appear at the bottom.

### Impossible Transitions

- `loading` → `submitting` (cannot submit a comment before the list loads)
- `disabled` → `submitting` (cannot submit when the feature is disabled)
- `submitting` → `empty` (submitting means at least one comment exists or is pending)

---

## Contracts

### Operation: `CreateComment`

**Preconditions:**
- User is authenticated and has comment permission on the task
- `taskId` is a valid, existing task ID
- Comment text is non-empty after trimming and ≤ 2000 characters
- No other comment submission is currently in flight (prevent double-submit)

**Postconditions (success):**
- A new comment exists on the task in the backend with correct author and timestamp
- The comment list in the UI includes the new comment with its server-assigned ID
- The input field is cleared

**Postconditions (failure):**
- No comment is created on the backend
- The user's input text is preserved in the input field or in the failed comment
- An actionable error message is displayed

### Operation: `FetchComments`

**Preconditions:**
- User is authenticated
- `taskId` is a valid, existing task ID
- Task detail screen is loading or refreshing

**Postconditions (success):**
- Comment list is populated with all comments for the task
- Comments are ordered chronologically (oldest first)
- State transitions to `loaded` (1+ comments) or `empty` (0 comments)

**Postconditions (failure — network error):**
- Comment list shows an error state with a retry option
- Rest of the task detail remains visible and functional

**Postconditions (failure — 404/403):**
- State transitions to `disabled` with appropriate message
- Comment input is disabled

---

## Anti-Requirements

- This feature does NOT support editing or deleting existing comments.
- This feature does NOT support rich text, markdown, or any formatting.
- This feature does NOT support file attachments or inline images.
- This feature does NOT support @mentions, notifications, or tagging.
- This feature does NOT support real-time/websocket updates — new comments from other users appear on next load only.
- This feature does NOT add comments to the task list view — comments are only visible on task detail.

---

## Dependencies

- [x] `GET /tasks/{taskId}` endpoint exists and returns task detail data
- [x] User authentication and permission model is in place
- [ ] `POST /tasks/{taskId}/comments` endpoint is deployed and accessible
- [ ] `GET /tasks/{taskId}` response schema updated to include `comments` array (or separate `GET /tasks/{taskId}/comments` endpoint exists)

---

## Open Questions

- [x] Should we show an optimistic comment immediately, or wait for the API? → Show optimistically with "sending" state (R8).
- [x] What happens if the user scrolls up while a comment is submitting? → Comment appears at the bottom; auto-scroll to it on success.
- [ ] Is there a rate limit on comment creation? → Need to check with backend team.

---

## File Manifest

| File | Last Known State | Referenced By |
|------|-----------------|---------------|
| `task-detail/TaskDetail` | baseline (initial spec) | R3 |
| `api/tasks` | baseline (initial spec) | R1, R5, R6, R7 (API error handling) |

---

## Change Log

<!-- No changes yet — spec is freshly approved. -->
