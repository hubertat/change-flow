# Change Flow

Version: 1.1.1 · 2026-11-18

## How to read this

Sections 1, 2 and 8 apply to every session. Then read by role:

| Role | Sections |
| --- | --- |
| Planner | 3, 4, 5, 6 |
| Reviewer | 4, 5 |
| Implementer | 6, 7 |

Read `change-flow/multi-repo.md` as well when the change spans repositories or reaches you as a handoff. Start files from `change-flow/templates/`; do not write their structure from memory.

## Contents

1. [Purpose and scope](#1-purpose-and-scope)
2. [Document layout and ownership](#2-document-layout-and-ownership)
3. [User input](#3-user-input)
4. [Plans and uncertainty](#4-plans-and-uncertainty)
5. [Reviews and decisions](#5-reviews-and-decisions)
6. [Lifecycle and approval](#6-lifecycle-and-approval)
7. [Implementation and completion](#7-implementation-and-completion)
8. [Resuming work and communicating](#8-resuming-work-and-communicating)
9. [Companion files](#9-companion-files)

## 1. Purpose and scope

Use this workflow for a coherent application change carried out with an agent, within one repository or across several repositories. Preserve the user's intent, make uncertainty visible before delegation, record consequential decisions, and compare the delivered result with the approved plan.

This document is an operational specification. **MUST** identifies a requirement; **SHOULD** identifies the default, with justified exceptions; **MAY** identifies an option. These rules operate within the agent tool's higher-priority instructions and the user's explicit authorization.

Keep the process proportional to the change. A small change may need only a few lines per section. Omit irrelevant sections rather than filling them with boilerplate. Record concise rationale, alternatives, decisions and evidence; do not produce internal reasoning transcripts or exhaustive activity logs.

The repository's `AGENTS.md` is the entry point; this document is the detailed reference.

### When to track a change

Do not apply this workflow to every small edit. Use it when the user requests it or repository instructions require it. Otherwise, propose a tracked change, and let the user decide, when the work:

- touches more than one repository, or changes a shared contract or public interface;
- includes a data migration or another change that is hard to reverse;
- is likely to span more than one session or pass between agents;
- depends on user choices about scope or behavior that are still open.

A fix or edit with obvious scope that fits in one session stays untracked.

## 2. Document layout and ownership

A change has one stable ID and one authoritative folder in a designated coordinating repository. For a single-repository change, that is the repository itself. Default naming is `CHG-001-short-title`; an existing project prefix and numbering scheme take precedence. IDs identify coherent changes, not individual commits. Never reuse an existing ID or overwrite another change.

Change records live on the default branch (`main` or `master`) of the coordinating repository. To reserve an ID, take the next free number from an up-to-date checkout of that branch and commit the new change folder there before other work continues. Commit plans, reviews, decisions and status changes to the default branch as well, so that every change is visible from one place. Only progress notes in `implementation.md` MAY stay on the working branch until it merges. Where the default branch accepts changes only through pull requests, merge the record through one promptly instead of leaving it on a feature branch. Committing change-record files this way is part of the workflow; pushing follows the user's authorization (section 6).

Layout:

```text
changes/CHG-001-short-title/
  input.md            (optional, human only)
  plan.md
  review.md
  implementation.md
```

These are four document roles. When a change spans repositories, the plan role contains several files (see `change-flow/multi-repo.md`). Add supporting research or examples only when useful, and link them from a plan. Do not introduce extra status or decision files by default.

| Document | Purpose | Ownership and update policy |
| --- | --- | --- |
| `input.md` (optional) | Original user intent, later notes and human decisions | Human only. Agent MUST NOT create, edit, append, reformat, summarize into, or populate this file. |
| `plan.md` and optional `plans/*.md` | Current solution, uncertainty, scope and acceptance criteria | Agent with optional human edits. Keep each current plan coherent. |
| `review.md` | Review rounds, findings, dispositions and numbered decisions | Agent with optional human contributions. Preserve round history. |
| `implementation.md` | Progress needed for handoff, final outcome and evidence | Agent with optional human contributions. Clearly separate progress from final results. |

The agent MAY create the change folder and agent-owned files. `input.md` is optional: a change MAY be planned, approved and completed without it. When it is absent, record the source of the request in `plan.md` (for example, the conversation and its date), without attributing generated text to the user. Do not create a placeholder, and do not ask the user to write the file before work can continue.

Start `plan.md`, `review.md` and `implementation.md` from the files in `change-flow/templates/`, and keep the plan header exactly as the template has it.

Never overwrite human edits in mixed-authorship files without reconciling them. Use version control for earlier plan versions; avoid making a separate file for every revision.

### Plan header

Every `plan.md` MUST start with its title line followed by these lines, in this order, with these exact labels, at the start of the line and without Markdown decoration:

```text
# CHG-012 — Export archive
Status: Implementing
Revision: r4
Approved: r3 @ 4f2a9c1 (D2)
Related: depends on CHG-009; follow-up of CHG-007
Blocked on: None
Planner: <model that wrote the plan>
```

- `Status` is one value from section 6, with nothing else on the line. It is the only status of the change; no other file carries one.
- `Approved` names the approved revision or revision set, the commit that holds it, and the decision ID. It is `None` until the user approves.
- `Related` uses `depends on`, `supersedes`, `superseded by` and `follow-up of` with change IDs, or `None`.
- `Blocked on` is `None`, or a short note naming the question ID or external dependency.

This keeps the whole project readable without opening each folder:

```sh
grep -H "^Status:" changes/*/plan.md
grep -H "^Blocked on:" changes/*/plan.md | grep -v "None$"
```

### IDs and traceability

Inside a change, use short IDs: `Q1` for open topics, `R1-F1` for review findings, `D1` for decisions, `AC1` for acceptance criteria. Outside the change folder, prefix them with the change ID: `CHG-012-D1`.

Branch names, commit messages and PR titles for tracked work MUST include the change ID, in every participating repository. For example: branch `chg-012-export-archive`, commit `CHG-012: add archive endpoint`. Follow the repository's existing conventions for where the ID goes. `git log --grep "CHG-012"` then lists the code that belongs to a change, and `git blame` leads back to its plan.

### Related changes and splitting

Split work into separate changes when the parts can be approved, delivered and accepted independently, or when one part waits on a decision the rest does not need. Keep it as one change when the parts share a contract and only work together. Link the resulting changes with `Related`.

When a change replaces another, set the old one to `Cancelled` with `Related: superseded by CHG-NNN`.

## 3. User input

The user's request can arrive in conversation, in `input.md`, or both. If the user wants to write an `input.md`, offer `change-flow/templates/input.md` as an optional aid. Free-form notes are valid input; the user does not have to complete a form or create a file before planning can begin.

Distinguish suggested approaches from requirements. A tentative user idea is not automatically a selected design. Treat user-specified exclusions and required technologies as constraints unless the user changes them.

The user MAY append dated notes, answers, approvals and replacements of earlier requirements. If later input is ambiguous or conflicts with earlier input or an approved plan, surface the conflict in the plan and ask for the necessary decision. Do not silently select whichever statement is easiest to implement.

User decisions are valid whether made in conversation or written in `input.md`. The agent MUST record each one in `review.md` as a numbered decision (`D1`, `D2`, …), concise and attributed. Each entry identifies the plan revision it applies to and its source: the conversation message by a stable reference when available, otherwise by date and a specific description, or the place in `input.md`. `review.md` is the single index of decisions; other files refer to decision IDs instead of restating them. Do not require the user to repeat an already clear decision or copy it into `input.md` before work can continue. Never invent a reference or an approval.

## 4. Plans and uncertainty

`plan.md` MUST include:

- The title and header lines defined in section 2.
- The problem and intended outcome, with the agent's interpretation distinguished from explicit requirements.
- Open topics and uncertainty near the top.
- Proposed solution, scope and exclusions, and consequential alternatives or tradeoffs.
- Implementation steps and dependencies.
- Observable acceptance criteria and planned verification.

The first draft SHOULD be useful even when incomplete. Investigate available code and documentation before asking questions the agent can answer. Label unverified claims as assumptions. Do not conceal unresolved choices behind confident prose or vague steps such as "handle edge cases."

### Open-topic record

Use stable question IDs within the change. Each topic MUST identify the uncertainty, a recommendation or next action, its resolution owner, and what work it blocks. States are `Open`, `Investigating`, `Assumed` and `Resolved`. Record a brief resolution and decision/evidence reference when closed.

| ID | Topic | Recommendation or next action | Owner | Blocks | State |
| --- | --- | --- | --- | --- | --- |
| Q1 | Include archived records in exports? | Exclude by default; user to confirm | User | Export behavior and acceptance | Open |
| Q2 | Existing library supports streaming? | Inspect dependency version and API | Agent | Export implementation | Investigating |
| Q3 | Default filename | Use `export-YYYY-MM-DD.csv` | Agent | Nothing; reversible detail within scope | Assumed |

User-owned topics concern intent, scope, preferences, or consequential tradeoffs. Agent-owned topics concern discoverable facts or implementation choices within delegated authority. A recommendation is not a user decision.

An `Assumed` topic is a default the agent chose so that planning could continue. It is not yet a decision. Every assumption still standing MUST be listed in the approval request (section 6), so that the user accepts defaults knowingly. When the user approves, the decision entry names the accepted assumptions, and their topics become `Resolved` with that decision ID.

Present blocking user questions in conversation as well as in the plan. Explain the consequence and provide a recommended answer where possible. The agent MAY continue independent investigation and planning while awaiting an answer. It MUST NOT implement a choice that depends on unresolved user authorization. Block only affected work when other work is already authorized and independent.

### Revision policy

Give each plan a simple revision identifier, such as `r1`. Increment it for changes to behavior, scope, contracts, acceptance criteria, consequential design choices, or implementation strategy. Typographical fixes and clarification that do not change meaning do not need a new revision.

Record the approved revision separately from the current revision. A new revision MUST NOT inherit approval automatically. Preserve the earlier approval reference and identify the changed scope awaiting approval. Do not silently rewrite an approved plan to match implementation after the fact.

Commit each plan revision before asking for its review or approval. The `Approved` header line records the commit that holds the approved text, so `git show <commit>:changes/CHG-NNN-title/plan.md` returns exactly what the user approved, however far the current plan has moved on. Updating header lines does not create a new revision.

## 5. Reviews and decisions

Before implementation approval is requested, the plan MUST pass at least one independent clarity and blind-spot review.

### Independent reviewer

The reviewer MUST be a fresh session running a different model from the planner. It works from the committed plan, `input.md` when present, the codebase and this specification, and not from the planning conversation. The planner MUST NOT review its own plan. Its own checks before handing over are useful, but they are not a review round. A review by the planner's model does not count either, even in a new session.

The usual loop:

1. The planner commits the plan revision and tells the user it is ready for review.
2. The user starts the reviewer session on that revision, or asks the planner to launch one.
3. The reviewer records its findings as a new round in `review.md`, or returns them in conversation for the user to pass on. The reviewer does not edit the plans.
4. The planner records a disposition for every finding, revises the plan and commits the new revision. Findings received through the user are recorded unchanged and attributed to the reviewer.
5. The loop repeats on the changed revision until no blocking finding remains.

Only the user MAY waive independent review for a specific change. Record the waiver as a decision; an agent never waives it for itself.

### What the reviewer checks

- Unstated assumptions, contradictions, vague requirements, and decisions that would otherwise be guessed during implementation.
- Alignment with the user's need, required technologies, exclusions, and rejected approaches.
- Compatibility with the actual codebase and relevant documentation.
- Missing failure behavior, integration dependencies, migrations or rollout considerations where applicable.
- Acceptance criteria that can actually be checked, including cross-repository behavior.

### Findings and dispositions

For each round in `review.md`, record the target plan revision or revisions and their commit, the planner and reviewer models, date, findings and dispositions. Use stable finding IDs, such as `R1-F1`. Findings SHOULD identify a concrete location or claim and explain its consequence.

Every finding MUST be marked as fixed with a revision reference, rejected with a reason, deferred with an owner and consequence, or awaiting user decision. The planner decides dispositions but cannot erase findings: rejected and deferred findings stay in the record and are listed in the approval request (section 6). A deferred finding needs a follow-up change before closure (section 7). Agent-identified blockers cannot be cleared by merely relabeling them; their underlying issue must be resolved or explicitly accepted by the user when within the user's authority.

Update the current plan with resulting changes and open questions. Keep the review file as history, not a competing current specification. Preserve previous outcomes and add follow-up entries rather than erasing objections or approvals.

A review outcome of `Ready for approval` is an agent assessment. It is not user approval. Stop repeating reviews when relevant findings have been resolved; more rounds are not a substitute for evidence.

## 6. Lifecycle and approval

The `Status` line in `plan.md` is the only status of a change. Use exactly one of these values:

| Status | Meaning |
| --- | --- |
| Draft | Investigation, planning, questions, or review are underway. |
| Ready for approval | Independent review is done. Blocking topics and review findings are resolved, or a proposed partial scope is explicitly identified. |
| Approved | The user authorized implementation of identified revisions and scope. |
| Implementing | Authorized work is in progress. |
| Ready for acceptance | Implementation and verification results are documented for user review. |
| Complete | The user accepted the result, or previously delegated acceptance under recorded criteria and those criteria were met. |
| Deferred | Work is paused; preserve its record and reason. |
| Cancelled | Work is abandoned or superseded; preserve its record and reason. |

Statuses MAY move backwards when new evidence requires replanning. If blocked, keep the current status and set the `Blocked on` header line, naming the affected work.

### Approval request

When asking for approval, the agent MUST present in conversation:

- the revision or revision set, and the commit that holds it;
- every topic still `Assumed`, with the default chosen;
- review findings that were rejected or deferred, and a waived review if there was one;
- the proposed scope, when it is partial.

Approval MUST identify the plan revision and scope. For several plans, use a revision set, for example: `plan.md r3; backend.md r2; frontend.md r4`. A clear instruction such as "implement this plan" is valid approval when the referenced revision set is unambiguous. Approval also accepts the assumptions listed in the request, unless the user says otherwise. Record the approval as a decision that names the revision set, the commit and the accepted assumptions, then set the `Approved` header line. Silence, an agent recommendation, and a general expression of interest are not approval.

Do not introduce repeated permission prompts for work already authorized. When a new decision changes approved behavior, a shared contract, scope, a major tradeoff, or acceptance criteria, update the plan and seek a decision for that change. Routine choices within approved scope do not require renewed approval.

Read-only investigation and planning do not require implementation approval. A prototype that changes code or external state needs authorization appropriate to that action; record its purpose and distinguish experimental output from the approved deliverable.

Approval to implement does not automatically authorize publishing, deploying, merging, or other external actions. Follow the user's actual authorization for each action; document rollout needs where relevant without inventing extra approval gates.

## 7. Implementation and completion

Implement the approved scope and respect local repository instructions. During execution, keep only enough progress information in `implementation.md` for reliable continuation: completed work, remaining steps, current blockers and relevant references.

If new user input or a technical discovery affects the approved plan, assess its impact. Record material deviations and the corresponding decision. Continue independent authorized work when possible; never silently guess a consequential unresolved choice.

Before reporting readiness for acceptance, `implementation.md` MUST state:

- Delivered behavior and affected repositories.
- The approved plan revisions followed.
- Acceptance-criterion results, with verification evidence and limitations.
- Material deviations and their decision references.
- Remaining work, unresolved issues, and explicitly unverified behavior.
- Relevant commits, branches or PRs, and merge/deployment state where applicable.

Use stable acceptance IDs such as `AC1` to connect plans with results. Mark criteria `Passed`, `Failed`, `Not run`, or `Blocked`; do not claim verification based only on code being written. Missing test access or integration environments must remain visible. Run checks proportionate to the change and follow required repository checks.

Distinguish implemented, verified, merged, deployed and accepted. A local implementation can be ready for acceptance without being deployed, if that matches the approved scope. Do not mark a partial delivery Complete unless the user explicitly accepts a revised scope and the record reflects that decision.

Before a change becomes `Complete`, every deferred finding and every item of remaining work MUST have one of two outcomes: a new change that carries it, or a recorded user decision to drop it. A `Draft` plan with `Related: follow-up of CHG-NNN` is enough for the new change, and small items MAY share one follow-up change. List the outcomes in `implementation.md`. Nothing stays pending inside a closed folder.

After closure, preserve the change folder as history. Update current architecture, interface and usage documentation where needed; future agents should not have to reconstruct the current system from historical change folders.

## 8. Resuming work and communicating

At the start of a session:

1. Read applicable repository instructions and this specification.
2. Locate the selected change and its coordinating record or supplied handoff. If multiple changes are plausible and context does not resolve the choice, ask which one applies. Plans that predate this workflow, as named in `AGENTS.md`, are history and not change records: read them for context, but do not resume, convert or update them as tracked changes unless the user asks.
3. Read user input when present, overall and relevant scoped plans, review decisions, and implementation progress. Establish approved revisions, unresolved questions and the next authorized step.
4. Compare the recorded state with the actual working tree and available code/commit/test evidence. Do not overwrite unrelated work or assume old verification still covers changed code.
5. Continue within existing authorization. If blocked, ask only for the missing decision and continue independent authorized work where possible.

Before overwriting a mixed-authorship file, re-read it if another actor may have changed it. Reconcile concurrent edits rather than replace them. With several agents, agree ownership of plan files and use one coordinating writer for shared records; do not start multiple writers on the same file without coordination. This workflow does not itself require launching subagents.

Conversation is the interface for questions and progress; the files are the durable record. Updates SHOULD state what changed, what needs a decision, and what happens next. Refer to stable question, finding and acceptance IDs. Do not require the user to inspect files to discover a blocking question.

## 9. Companion files

These live in the `change-flow/` folder next to this document.

| File | Use |
| --- | --- |
| `multi-repo.md` | Coordinating repository, scoped plans and handoffs. MUST be read for a change that spans repositories or arrives as a handoff. |
| `templates/plan.md`, `templates/review.md`, `templates/implementation.md` | Starting structure for the agent-owned files. |
| `templates/scoped-plan.md` | One per repository in a multi-repository change. |
| `templates/input.md` | For the human to copy or adapt. Agents MUST NOT write it into a change's `input.md`. |

Templates are minimal starting structures. Replace the comment hints, preserve useful existing content, and omit sections that do not apply.
