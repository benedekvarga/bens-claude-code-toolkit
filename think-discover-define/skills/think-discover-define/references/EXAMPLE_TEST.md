# Validation Strategy: Add Comment to Task

> Derived from: `SPEC.md`
> Every validation item traces to a spec requirement (R#) or invariant (INV-#).
> **Note:** Test naming below uses `snake_case`. Adapt casing to your
> project's convention (camelCase, PascalCase, etc.).

---

## Validation Naming Convention

Each validation item gets a stable `V-#` ID.

```
test_[subject]_[condition]_[expectedOutcome]
```

- Reads as a sentence: "test [that] subject [when/with] condition [then] expected outcome"
- No `testThat`, `should`, `verify` — just the behavior
- Under ~60 characters
- Adapt casing to project convention

---

## Validation Items

### Executable Tests

**V-1:** Submit valid comment appears in list → R1
- **Type:** Executable test
- **Concrete artifact:** `test_submit_validComment_appearsInList`
- **Method:** Load task detail with existing comments and submit "Looks good".
- **Pass criteria:** Comment appears in the list with the correct author and text.

**V-2:** Successful submit clears input and persists server ID → R2
- **Type:** Executable test
- **Concrete artifact:** `test_submit_success_clearsInput`
- **Method:** Submit a comment and await a successful API response with an assigned ID.
- **Pass criteria:** Input field is empty and the comment in the list has the server-assigned ID.

**V-3:** Loaded comments display chronologically → R3, INV-3
- **Type:** Executable test
- **Concrete artifact:** `test_loadComments_displaysChronologically`
- **Method:** Mock 3 comments with timestamps [t1, t3, t2] and load task detail.
- **Pass criteria:** Comments render in order [t1, t2, t3] (oldest first).

**V-4:** Empty submit shows validation → R4
- **Type:** Executable test
- **Concrete artifact:** `test_submit_emptyText_showsValidation`
- **Method:** Attempt to send with an empty input field.
- **Pass criteria:** No API call is made and an inline validation message is displayed.

**V-5:** Whitespace-only submit shows validation → R4
- **Type:** Executable test
- **Concrete artifact:** `test_submit_whitespaceOnly_showsValidation`
- **Method:** Attempt to send with input containing only spaces/newlines.
- **Pass criteria:** No API call is made and an inline validation message is displayed.

**V-6:** Network error on submit shows retry → R5, INV-2
- **Type:** Executable test
- **Concrete artifact:** `test_submit_networkError_showsRetry`
- **Method:** Configure the submit API to return a network error, then submit a valid comment.
- **Pass criteria:** The failed comment shows an error indicator with a retry action, and the user's text is preserved.

**V-7:** Retry resends failed comment → R5
- **Type:** Executable test
- **Concrete artifact:** `test_submit_retry_resendsComment`
- **Method:** Start from a `submitError` state and trigger Retry.
- **Pass criteria:** Comment transitions back to "sending" and the API is called again.

**V-8:** Error states disable input or show section retry affordance → R6, R7, R8
- **Type:** Executable test
- **Concrete artifact:** `test_errorStates_disableCommenting`
- **Method:** Trigger submit-time 404, submit-time 403, and load-time network failure scenarios.
- **Pass criteria:** 404 and 403 disable further input with the correct message; load-time network failure shows inline comments-section error with retry while preserving task-detail usability.

**V-9:** In-flight submit shows sending state and disables send button → R9
- **Type:** Executable test
- **Concrete artifact:** `test_submit_inFlight_disablesSendButton`
- **Method:** Hold the submit API open after a valid comment is sent.
- **Pass criteria:** Send button is disabled and the pending comment is shown as "sending".

**V-10:** Comment list loading shows section loading indicator → R10
- **Type:** Executable test
- **Concrete artifact:** `test_loadComments_showsLoadingIndicator`
- **Method:** Hold the comments fetch API open during task-detail load.
- **Pass criteria:** Loading indicator is visible in the comments area.

**V-11:** Submit while loading is blocked → Impossible Transitions
- **Type:** Executable test
- **Concrete artifact:** `test_submit_whileLoading_isBlocked`
- **Method:** Attempt to submit while the comments section is still in `loading`.
- **Pass criteria:** Submission does not occur and the input is disabled or not yet visible.

**V-12:** Large comment set paginates or lazy loads → R12
- **Type:** Executable test
- **Concrete artifact:** `test_commentsOverFifty_loadIncrementally`
- **Method:** Seed more than 50 comments and load the task detail.
- **Pass criteria:** Initial render shows only the first slice and exposes a working load-more or lazy-load mechanism.

**V-14:** Near-limit input shows character counter → R11
- **Type:** Executable test
- **Concrete artifact:** `test_input_nearLimit_showsCharCounter`
- **Method:** Enter 1801 characters into the comment input.
- **Pass criteria:** Character counter is visible and shows the remaining count.

**V-15:** Input at limit rejects more text → R11
- **Type:** Executable test
- **Concrete artifact:** `test_input_atLimit_preventsMoreText`
- **Method:** Enter exactly 2000 characters, then attempt one more.
- **Pass criteria:** Additional input is rejected or the UI clearly prevents overflow at the limit.

**V-16:** Failed submit preserves input text → INV-2
- **Type:** Executable test
- **Concrete artifact:** `test_failedSubmit_preservesInputText`
- **Method:** Type "Important update", force submission failure, then inspect the draft state.
- **Pass criteria:** User text is still available for retry without retyping.

**V-17:** Confirmed comment survives reload → INV-1
- **Type:** Executable test
- **Concrete artifact:** `test_confirmedComment_persistsAfterReload`
- **Method:** Submit a comment successfully, then reload the comment list.
- **Pass criteria:** Confirmed comment remains present after reload.

**V-18:** Submit while disabled is blocked → Impossible Transitions
- **Type:** Executable test
- **Concrete artifact:** `test_submit_whileDisabled_isBlocked`
- **Method:** Attempt to submit while the feature state is `disabled`.
- **Pass criteria:** Submission does not occur and the input remains disabled.

### Audit / Manual Checks

**V-13:** Comments loading does not block the rest of task detail → R10
- **Type:** Audit/manual check
- **Concrete artifact:** `none`
- **Method:** Throttle comments loading in a running app, then interact with non-comments task-detail controls while the comments section is loading and while it is in load-error state.
- **Pass criteria:** The rest of the task detail remains usable and visibly separate from comments-section loading/error treatment.

---

## Explicitly NOT Validated

- Visual layout, spacing, or typography of comment items (design, not logic)
- Exact timestamp formatting (covered by the project's date formatting utility tests)
- API client HTTP handling (tested in its own module)
- Pagination micro-optimizations beyond the contractual "supports load more/lazy loading" behavior

---

## Validation Placement

Place executable tests alongside the state management / business logic for comments.
Match existing test patterns in the codebase for:
- Mock setup (API mocks, state initialization)
- Async test patterns (how the project handles async assertions)
- Fixture data (how the project creates test data)

Document manual validation `V-13` in the implementation PR checklist or QA note.
