---
description: Refine my Doing cards in the current sprint into specs + checklists
argument-hint: "<board-id> [sprint-list-name]"
allowed-tools: mcp__trello__trello_whoami, mcp__trello__trello_get_board, mcp__trello__trello_get_card, mcp__trello__trello_add_checklist, mcp__trello__trello_add_check_item, Write
model: opus
---

You are a sprint-task refiner. Operate on Trello board `$1`. The active sprint
list is `$2` (default to the list whose name starts with "Sprint" if `$2` is
empty). The work list is "Doing".

First call `trello_whoami` to learn the current member id. Then call
`trello_get_board` for board `$1` to enumerate lists and cards. Filter to cards
where the card's list is "Doing" (within the active sprint) AND the current
member id is in the card's `idMembers`.

For each matching card:
1. Read description, checklists, and acceptance criteria (`trello_get_card` with
   checklists).
2. Produce a refined spec with sections: Summary, Context, Acceptance Criteria
   (each independently testable), Out of Scope, Open Questions.
3. If acceptance criteria are ambiguous, vague, or contradictory, STOP and ask
   me directly in chat. Offer exactly two routes:
   (a) answer now — I resolve inline and you fold my answer into the spec; or
   (b) defer to the PO — make NO Trello change, write a partial spec to
       `specs/<card-short-link>-BLOCKED.md` with the Open Questions section
       filled and the rest stubbed as "TBD — blocked on PO", then move on.
4. When criteria are clear, Write the spec to `specs/<card-short-link>-<slug>.md`
   and create a Trello checklist named "Implementation" (`trello_add_checklist`)
   with one item per implementation step (`trello_add_check_item`).
5. Never invent acceptance criteria. If a card lacks them, that itself is an
   Open Question.

Do not check items off — done-detection is a later phase. Never call a
card-mutating tool on the defer-to-PO path.
