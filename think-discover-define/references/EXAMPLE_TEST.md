# Test Strategy: Add Comment to Task

> Derived from: `SPEC.md`
> Every test traces to a spec requirement (R#) or invariant (INV-#).
> **Note:** Test naming below uses `snake_case`. Adapt casing to your
> project's convention (camelCase, PascalCase, etc.).

---

## Naming Convention

```
test_[subject]_[condition]_[expectedOutcome]
```

- Reads as a sentence: "test [that] subject [when/with] condition [then] expected outcome"
- No `testThat`, `should`, `verify` — just the behavior
- Under ~60 characters
- Adapt casing to project convention

---

## Test Cases

### Comment Submission — Happy Path

**TC-1:** `test_submit_validComment_appearsInList` → R1
- **Setup:** Task detail loaded with existing comments
- **Action:** Submit comment with text "Looks good"
- **Assert:** Comment appears in list with correct author and text

**TC-2:** `test_submit_success_clearsInput` → R2
- **Setup:** Submit a comment, API returns success with assigned ID
- **Action:** Await API response
- **Assert:** Input field is empty; comment in list has server-assigned ID

**TC-3:** `test_loadComments_displaysChronologically` → R3, INV-3
- **Setup:** API returns 3 comments with timestamps [t1, t3, t2]
- **Action:** Load task detail
- **Assert:** Comments displayed in order [t1, t2, t3] (oldest first)

### Input Validation

**TC-4:** `test_submit_emptyText_showsValidation` → R4
- **Setup:** Input field is empty
- **Action:** Tap/click "Send"
- **Assert:** No API call made; inline validation message displayed

**TC-5:** `test_submit_whitespaceOnly_showsValidation` → R4
- **Setup:** Input contains only spaces/newlines
- **Action:** Tap/click "Send"
- **Assert:** No API call made; inline validation message displayed

**TC-6:** `test_input_nearLimit_showsCharCounter` → R10
- **Setup:** Input has 1801 characters (within 200 of 2000 limit)
- **Action:** Observe input field
- **Assert:** Character counter is visible showing remaining characters

**TC-7:** `test_input_atLimit_preventsMoreText` → R10
- **Setup:** Input has exactly 2000 characters
- **Action:** Attempt to type another character
- **Assert:** Input does not accept additional characters (or counter shows 0 remaining)

### Submission — Error Handling

**TC-8:** `test_submit_networkError_showsRetry` → R5, INV-2
- **Setup:** API is configured to return a network error
- **Action:** Submit a valid comment
- **Assert:** Comment shows error indicator with "Retry" action; user's text is preserved

**TC-9:** `test_submit_retry_resendsComment` → R5
- **Setup:** Comment is in `submitError` state
- **Action:** Tap/click "Retry"
- **Assert:** Comment transitions to "sending" state; API is called again

**TC-10:** `test_submit_taskDeleted_disablesInput` → R6
- **Setup:** API returns 404 (task not found)
- **Action:** Submit a comment
- **Assert:** State transitions to `disabled`; message indicates task was deleted; input is disabled

**TC-11:** `test_submit_noPermission_disablesInput` → R7
- **Setup:** API returns 403 (forbidden)
- **Action:** Submit a comment
- **Assert:** State transitions to `disabled`; message indicates no access; input is disabled

### State-Dependent Behavior

**TC-12:** `test_submit_inFlight_disablesSendButton` → R8
- **Setup:** Comment submission is in progress (API not yet responded)
- **Action:** Observe send button
- **Assert:** Send button is disabled; "sending" indicator is visible on the comment

**TC-13:** `test_loadComments_showsLoadingIndicator` → R9
- **Setup:** API call for comments is in progress
- **Action:** Observe comments section
- **Assert:** Loading indicator visible in comments area; rest of task detail is not blocked

### State Machine Integrity

**TC-14:** `test_submit_whileLoading_isBlocked` → Impossible Transitions
- **Setup:** Comment list is still loading (state: `loading`)
- **Action:** Attempt to submit a comment
- **Assert:** Submission does not occur; send button is disabled or input is not yet visible

**TC-15:** `test_submit_whileDisabled_isBlocked` → Impossible Transitions
- **Setup:** State is `disabled` (task deleted or no permission)
- **Action:** Attempt to submit a comment
- **Assert:** Submission does not occur; input field is disabled

### Invariants

**TC-16:** `test_confirmedComment_persistsAfterReload` → INV-1
- **Setup:** Submit a comment, API confirms
- **Action:** Trigger a reload of the comment list (simulating navigation away and back)
- **Assert:** Comment is still present in the list

**TC-17:** `test_failedSubmit_preservesInputText` → INV-2
- **Setup:** Type "Important update" in input. Submission fails.
- **Action:** Check input field content
- **Assert:** Input still contains "Important update"

---

## Explicitly NOT Tested

- Visual layout, spacing, or typography of comment items (design, not logic)
- Exact timestamp formatting (covered by the project's date formatting utility tests)
- API client HTTP handling (tested in its own module)
- Scroll behavior and auto-scroll (manual QA, depends on platform specifics)
- Pagination edge cases beyond "it loads more" (T11 implementation may warrant additional tests)

---

## Test File Placement

Place tests alongside the state management / business logic for comments.
Match existing test patterns in the codebase for:
- Mock setup (API mocks, state initialization)
- Async test patterns (how the project handles async assertions)
- Fixture data (how the project creates test data)

Check the codebase for existing patterns before writing tests.
