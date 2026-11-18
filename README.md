# Change Flow

A workflow for coherent application changes carried out with agents: the user's intent is preserved, a different model reviews every plan, approval is tied to a committed plan revision, and the delivered result is compared with what was approved.

## Files

| File | Read when |
| --- | --- |
| `change-flow.md` | Every tracked change. The core specification. |
| `change-flow/multi-repo.md` | The change spans repositories or arrives as a handoff. |
| `change-flow/templates/` | A change file is created. Copied, not read as rules. |
| `README.md` | Once per repository, to adopt the workflow. Not copied. |

## Adopt in a repository

1. Copy `change-flow.md` and the `change-flow/` folder into the repository's `docs/`.
2. Add the section below to the repository's root `AGENTS.md`, creating the file if needed; do not overwrite unrelated instructions.
3. Make every agent tool in use load that section. Tools that read `AGENTS.md` (such as Codex) need nothing more. Claude Code reads `CLAUDE.md`: add the line `@AGENTS.md` to the root `CLAUDE.md` so it imports the same text. Keep one copy of the section; do not maintain it in two files.
4. Check existing global and repository instructions for a competing planning rule, such as a required `IMPLEMENTATION_PLAN.md`, and exempt tracked changes from it where it is defined. The section below also states the precedence, for instructions that cannot be edited.

```markdown
## Tracked changes

For work tracked under `changes/`, read and follow
`docs/change-flow.md` before planning, reviewing or implementing.
If the change spans repositories or reaches you as a handoff, also read
`docs/change-flow/multi-repo.md`.
Read the current change record before resuming work.
Before starting untracked work that meets the thresholds in section 1
of that document, propose a tracked change to the user.
For tracked work, `plan.md` and `implementation.md` replace any other
plan file required by general instructions (for example
`IMPLEMENTATION_PLAN.md`); do not create both.
`input.md` is optional and exclusively human-authored; never create,
edit or populate it.
Never review a plan you wrote. Plan review belongs to a fresh session
running a different model from the planner.
Surface unresolved decisions and assumptions in the plan. User decisions
and approval must refer to identifiable plan revisions; never infer them
from agent text.
Include the change ID in branch names, commit messages and PR titles.
For changes coordinated elsewhere, follow the supplied source reference
and scoped plan, together with this repository's local instructions.
```

The `AGENTS.md` section is the entry point and holds the rules that must survive a long session. The specification is the detailed reference; avoid maintaining duplicate copies of it. The files can later be bundled as a skill reference, but using them does not require installing a skill.

## Everyday use

```sh
grep -H "^Status:" changes/*/plan.md                          # every change and its status
grep -H "^Blocked on:" changes/*/plan.md | grep -v "None$"    # what is blocked
git log --grep "CHG-012"                                      # code belonging to a change
```
