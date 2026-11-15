---
name: change-impact-review
description: Assess a proposed requirements, scope, or design change during an application project. Identify affected phases and documents, prepare consistent revisions, and obtain approval before work resumes.
---

# Change Impact Review

Use this workflow when the user proposes changing an approved requirement, scope, constraint, acceptance criterion, or design decision during an application project.

Follow the repository's `AGENTS.md` instructions. This skill supports its change-impact protocol; it does not replace phase approvals or authorize implementation.

## Review the current state

1. Read the applicable `AGENTS.md` instructions.
2. Determine the current development phase and read the project's current canonical documents:
   - `docs/requirements.md`
   - `docs/architecture.md`
   - `docs/implementation-plan.md`
3. Read relevant project files when needed to understand whether implementation has begun.
4. Identify which decisions are approved, pending approval, or superseded.

If a listed document does not exist, do not invent its contents. Record the gap and continue with available evidence.

## Assess the proposed change

Clarify the user's intended outcome when it is ambiguous. Separate the requested change from assumptions that would materially affect scope or design.

Trace the change through the relevant areas:

- Product behavior, users, scope, and non-goals
- Functional and non-functional requirements
- Acceptance criteria
- Architecture and component responsibilities
- API contracts and integrations
- Data model and storage
- Authentication, authorization, and security
- Testing and verification
- Implementation tasks, dependencies, and affected files
- Existing implementation, if work has begun

Identify the earliest phase affected:

- Return to **Requirements Definition** when behavior, scope, constraints, or acceptance criteria change.
- Return to **Architecture and Design** when the requirements remain valid but the solution design changes.
- Return to **Implementation Planning** when the approved design remains valid but tasks, dependencies, affected files, or verification change.

List downstream documents and implementation work that must be revised. Preserve approvals for unaffected decisions.

## Present the impact review

Before continuing to the next phase, present:

- A concise description of the requested change
- The earliest affected phase and why
- Affected documents and implementation areas
- Decisions or assumptions that need clarification
- Which prior approvals remain valid and which need review
- Any risks, tradeoffs, or acceptance criteria affected

Do not make important product or architecture decisions on the user's behalf. Ask focused questions when needed.

## Prepare and review revisions

After requirements are sufficiently clear, prepare consistent revisions to the affected canonical documents. Keep each document authoritative for its purpose:

- `docs/requirements.md` owns approved requirements, constraints, scope, non-goals, and acceptance criteria.
- `docs/architecture.md` owns the approved solution design.
- `docs/implementation-plan.md` owns implementation tasks, dependencies, affected files, and verification.

Use stable requirement IDs such as `FR-01` and `NFR-01` where useful. Reference relevant IDs from architecture and test documentation. Update or mark superseded decisions so the documents do not contradict one another.

Present the revised documents and a short change summary for user review. Wait for explicit approval of each affected phase before resuming work. Approval of a document revision does not authorize implementation or approval of later phases.

If implementation has already started, include the code, tests, or data that need reconciliation in the revised implementation plan. Do not make those implementation changes until the required phase approvals are in place.

## Completion

Before handing the work back:

- Confirm that affected documents agree with each other.
- Confirm that the change is reflected in acceptance criteria and planned verification where applicable.
- Identify unresolved questions and work that remains blocked on approval.
- Report exactly which documents were revised and which approvals are still needed.