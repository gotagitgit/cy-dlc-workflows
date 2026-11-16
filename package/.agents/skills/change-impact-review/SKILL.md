---
name: change-impact-review
description: Assess a proposed requirements, scope, or design change during an application project. Identify affected phases and documents, prepare consistent revisions, and obtain approval before work resumes.
---

# Change Impact Review

Use this workflow automatically when the user proposes changing an approved requirement, scope, constraint, acceptance criterion, or design decision during an application project, including a change that affects one or more prior features. Do not wait for the user to name the skill. A request to explain or clarify existing text is not, by itself, a proposed scope change.

Follow the repository's `AGENTS.md` instructions. This skill supports its change-impact protocol; it does not replace phase approvals or authorize implementation.

## Review the current state

1. Read the applicable `AGENTS.md` instructions.
2. Determine the current development phase and read the project's current canonical documents:
   - `docs/requirements.md`
   - `docs/architecture.md`
   - `docs/implementation-plan.md`
   - `docs/features/README.md`, when present, and the feature documents linked from it
   - Relevant shared ADRs and existing change/amendment references
3. Read relevant project files when needed to understand whether implementation has begun.
4. Identify which decisions are approved, pending approval, or superseded.

If a listed document does not exist, do not invent its contents. Record the gap and continue with available evidence. Determine the active feature ID and inspect only the feature documents and cross-references relevant to the proposed change; do not require reading every unrelated feature document. Before editing, check for concurrent edits to affected files and surface conflicts instead of overwriting them.

## Assess the proposed change

Clarify the user's intended outcome when it is ambiguous. Separate the requested change from assumptions that would materially affect scope or design.

Trace the change through the relevant areas:

- Product behavior, users, scope, and non-goals across the project and affected features
- Stable feature IDs and requirement IDs; dependencies and references between features
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

List downstream documents and implementation work that must be revised. Preserve approvals for unaffected decisions and feature workstreams. The change-owning feature owns the new delta. Link it to affected prior feature/requirement IDs; amend prior feature documents only where needed to keep current behavior accurate, preserving the original approved baseline and linking to the amendment instead of duplicating the new specification. Update root documents only for shared or integrated effects.

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

- Root `docs/requirements.md` owns project goals, shared constraints, non-goals, cross-cutting acceptance criteria, and links to feature requirements.
- `docs/features/<FEATURE-ID>/requirements.md` owns the approved requirements and acceptance criteria for that feature.
- Root `docs/architecture.md` owns the current integrated design and shared contracts.
- `docs/features/<FEATURE-ID>/architecture.md` owns the design for that feature and links to shared architecture and ADRs.
- Root `docs/implementation-plan.md` owns cross-feature sequencing and integration dependencies.
- `docs/features/<FEATURE-ID>/implementation-plan.md` owns tasks, dependencies, files, and verification for that feature.
- `docs/features/README.md` indexes feature IDs, status, owners when known, dependencies, and document links.

Use stable feature IDs such as `FEAT-012` and feature-scoped requirement IDs such as `FEAT-012-FR-01`. Reference affected requirement IDs across feature documents. Preserve prior approvals and original baselines; append amendment/supersession links when current behavior changes. Update root shared references only when needed, and keep the feature register accurate. Update or mark superseded ADRs so the documents do not contradict one another.

Present the revised documents and a short change summary for user review. Wait for explicit approval of each affected phase before resuming work. Approval of a document revision does not authorize implementation or approval of later phases.

If implementation has already started, include the code, tests, or data that need reconciliation in the revised implementation plan. Do not make those implementation changes until the required phase approvals are in place.

## Completion

Before handing the work back:

- Confirm that root references, the feature register, affected feature documents, and relevant ADRs agree with each other.
- Confirm that original approvals remain traceable and unaffected feature documents were left unchanged.
- Confirm that the change is reflected in acceptance criteria and planned verification where applicable.
- Identify unresolved questions and work that remains blocked on approval.
- Report exactly which documents were revised and which approvals are still needed.