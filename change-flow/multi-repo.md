# Change Flow — Multiple repositories

Companion to `change-flow.md`, which still applies in full. Read this file when a change spans more than one repository, or when the change reaches you as a handoff.

## Layout

```text
changes/CHG-001-short-title/
  input.md            (optional, human only)
  plan.md
  plans/
    backend.md
    frontend.md
  review.md
  implementation.md
```

In the overall `plan.md`, add the line `Coordinating repository: ...` directly after the plan header. Start each scoped plan from `templates/scoped-plan.md`.

## Coordinating repository

Choose one coordinating repository. It owns the complete change record: user input, shared rationale and decisions, all authoritative plans, review history, and consolidated outcomes. Other repositories MUST NOT maintain independently editable copies of that record.

The coordinating repo MAY be a main application repo or a dedicated coordination repo. Record stable repository identifiers or URLs and relevant branches/base commits when needed; do not rely solely on machine-specific absolute paths.

## Overall and scoped plans

The overall `plan.md` MUST identify:

- Participating repositories, their scoped plan paths and revisions, and each repository's local state: `Pending`, `Implementing` or `Done`.
- Shared interface contracts and consequential cross-repository decisions.
- Dependencies and sequencing, including compatibility or rollout order where relevant.
- Integration acceptance criteria, the responsible verifier, and unresolved integration questions.
- The revision set proposed for approval or already approved.

Each scoped plan MUST identify its target repository, objective, local changes, relevant shared contracts, dependencies, acceptance criteria, and verification. Include enough shared context to implement correctly; omit unrelated history. Scoped plans reference shared decisions rather than redefine them. If a contradiction is found, stop affected work and reconcile it in the authoritative record.

Scoped plans carry no status or approval line. The overall plan holds the change status, the approved revision set and the per-repository state.

## Access and handoffs

When an agent can read the coordinating record, provide a direct reference to its scoped plan and required overall context. Copying is unnecessary.

When it cannot, supply a scoped handoff in the target repository or task context. If stored as a file, a suitable path is `changes/CHG-001-short-title/handoff.md`. This is a derived delivery copy, not an additional authoritative document role. It MUST include:

- Change ID, target repository, and the coordinating source location.
- Source plan paths/revisions and a commit reference when available.
- Approval reference and authorized scope.
- Relevant shared decisions, contracts, dependencies and acceptance criteria.
- A statement that changes must be returned to the coordinating record rather than treated as local amendments to the approved plan.

Do not copy `input.md` or the full review history by default. The coordinating agent owns refreshing handoffs after material changes. Do not issue a handoff known to be stale; a receiving agent must flag a known revision mismatch before affected work continues. An offline recipient should state which supplied revision it is following and request an updated handoff when new information conflicts with it.

Local agents report evidence, blockers, proposed amendments, and commit/PR references back to the coordinating agent or user. If they cannot write the coordinating repo, provide a concise result handoff for reconciliation; do not claim the authoritative record was updated. The coordinating agent records these results in the canonical `review.md` and `implementation.md`.

A change to a shared contract requires impact assessment across all affected plans and revision sets. Do not allow each repository to independently choose a conflicting contract. Repositories may finish at different times; local completion is distinct from overall completion. Integration criteria remain pending until actually verified or explicitly removed from scope by the user.
