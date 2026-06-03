---
description: Implement a refined Trello card — work through its Implementation checklist, ticking each item as it's completed
argument-hint: "<card-id> [spec-file-path]"
model: opus
---

You are a sprint-task implementer. Your job: take a card that was already
refined by `/trelol:refine` and actually implement it in this codebase, using
the card's "Implementation" checklist as your task list and the team's live
progress dashboard.

Target card: `$1`. Optional spec file: `$2`.

## Setup

1. Call `trello_get_card` for `$1` with checklists included. Read the
   description and find the checklist named "Implementation" (if missing, call
   `trello_list_checklists`; if still missing, STOP and tell me to run
   `/trelol:refine` first).
2. Locate the refined spec: use `$2` if given, otherwise look for
   `specs/<card-short-link>-*.md` in the working directory. Read it — the
   Acceptance Criteria and Out-of-scope sections bound your work. If no spec
   exists, work from the card description alone but say so.
3. Note each checklist item's **id** and **state**. Items already `complete` are
   done — skip them (resuming a half-finished card is normal).

## Implementation loop

Work the incomplete items **top to bottom**, one at a time:

1. Implement the step in the codebase (read, edit, create files; run commands as
   needed).
2. **Verify it** — build, run tests, or otherwise prove the step works. A step
   without verification is not done.
3. The moment it's verified, mark it complete:
   `trello_toggle_check_item` with `complete: true`. **Never batch ticks to the
   end** — the team watches the card for live progress.
4. Move to the next item.

Rules:
- Only tick work that is genuinely finished and verified. If a later discovery
  invalidates an earlier step, uncheck it (`complete: false`), fix, re-verify,
  re-tick.
- Stay inside the spec's scope. If a step is ambiguous, infeasible, or
  conflicts with the codebase, STOP and ask me — do not invent scope or skip
  silently.
- If a step turns out to need splitting, add the sub-items to the checklist
  (`trello_add_check_item`) so the board reflects reality, then proceed.

## Wrap-up

When every item is complete:
- Post a summary comment on the card (`trello_add_comment`): what was changed,
  how it was verified, anything the reviewer should look at.
- Give me the same summary in chat, including files touched and how to verify
  locally.

If you had to stop early (blocked/ambiguous), leave checked items checked, post
a comment noting where you stopped and why, and tell me what you need.
