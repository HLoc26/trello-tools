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

0. Ground yourself — you have no memory of previous sessions:
   - Read `CLAUDE.md`/`README.md` and the codebase areas the card touches so
     your changes follow existing patterns.
   - For decisions and related specs, **don't read all of `specs/` yourself** —
     it may be large. Spawn the **`spec-reader`** subagent with a query like
     "decisions and specs relevant to card `$1` and its implementation steps".
     Honor every decision it surfaces; never contradict or re-ask. (Still read
     this card's OWN spec file directly — it's one file, see step 2.)
1. Locate the refined spec FIRST — it holds the real ids so you don't have to
   fetch the whole board. Use `$2` if given, else find `specs/*.md` whose
   metadata header `card:`/`cardShortLink:` matches `$1` (or filename slug
   matches). Read its header to get the **24-hex `card` id**, `board` id, and
   `checklist` id, plus the Acceptance Criteria / Out-of-scope that bound your
   work.
   - The Trello tools need the **24-hex card id**, not a URL slug. If `$1` is
     already 24-hex, use it. Otherwise use the `card:` id from the spec header.
     Only if no spec/header exists, fall back to `trello_get_board` to resolve
     the slug to a real card id (and say the spec is missing — suggest
     `/trelol:refine`).
2. Call `trello_get_card` for the resolved 24-hex card id with checklists
   included; find the "Implementation" checklist (or `trello_list_checklists`).
   If there's no spec, work from the card description alone but say so.
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
- When you make a non-obvious implementation choice (a library, a pattern, a
  trade-off), or I answer a question for you, append it to `specs/decisions.md`
  (same ledger `/trelol:refine` uses) so the next session inherits it.

## Wrap-up

When every item is complete:
- Post a summary comment on the card (`trello_add_comment`): what was changed,
  how it was verified, anything the reviewer should look at.
- Give me the same summary in chat, including files touched and how to verify
  locally.

If you had to stop early (blocked/ambiguous), leave checked items checked, post
a comment noting where you stopped and why, and tell me what you need.
