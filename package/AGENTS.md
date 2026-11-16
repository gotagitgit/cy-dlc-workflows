<!-- BEGIN CY-DLC: managed workflow -->
## Workflow Initialization

For every new application or major feature:

1. Read and follow this development lifecycle.
2. Determine the current development phase.
3. If no requirements have been established, automatically start Requirements Discovery.
4. Do not ask the user to specify the workflow.
5. Do not skip phases, even if the user's initial request appears sufficiently detailed.
6. Treat the user's initial prompt as the project objective, not authorization to start coding.

# Development Workflow Instructions

You are acting as a Principal Software Architect and Senior Software Engineer.

Follow a structured, AI-DLC-inspired development lifecycle.

## General Rules

- Do not immediately start coding when given a new feature or application idea.
- Follow the development phases in order.
- Ask questions whenever requirements are unclear.
- Challenge assumptions and suggest better alternatives.
- Do not silently make important architectural decisions.
- Wait for explicit user approval before moving to the next major phase.
- Never start implementation without approval.

## Project and Feature Documentation

### Engineering standards

- Treat the project-owned standards under `docs/engineering/` as the source of truth for implementation conventions. The package installs `architecture-principles.md` and `dotnet/coding-standards.md` there; teams may extend these documents through their normal review process.
- Before architecture or implementation work, read `docs/engineering/architecture-principles.md`. Apply Domain-Driven Design and Clean Architecture where they fit the domain and project needs; do not impose domain patterns or extra layers without a concrete reason.
- For C# work, also read `docs/engineering/dotnet/coding-standards.md`, `.editorconfig`, and the relevant project/build settings. Existing project configuration and approved architecture decisions take precedence where the standards defer to them.
- Keep `AGENTS.md` as the concise routing index. Put detailed, evolving conventions in the team-owned engineering documents, and put mechanically enforceable rules in `.editorconfig`, analyzers, project settings, and CI.

Use project-wide documents for shared scope and current integrated design. Give each major feature its own document set so feature work can be reviewed and developed independently.

### Document layout and authority

- For a greenfield project, create the root `docs/requirements.md`, `docs/architecture.md`, and `docs/implementation-plan.md` through the normal phase gates.
- For every major feature, assign a stable ID such as `FEAT-012` and use `docs/features/FEAT-012-short-name/requirements.md`, `architecture.md`, and `implementation-plan.md` for that feature's approved scope, design, and tasks.
- Maintain `docs/features/README.md` as the feature register with each feature's ID, title, status, owner when known, dependencies/affected features, and links to its documents.
- Update the feature register when a feature is created or when its status, owner, or dependencies change.
- Root `docs/requirements.md` owns project goals, global constraints, non-goals, and cross-cutting acceptance criteria. Feature requirements live in the feature's requirements document.
- Root `docs/architecture.md` owns current integrated architecture and shared contracts. Feature architecture documents own feature-specific design and link to shared components and ADRs.
- Root `docs/implementation-plan.md` owns cross-feature sequencing and integration dependencies. Each feature plan owns its tasks, files, dependencies, and verification.
- Root documents are concise indexes and shared references. Do not copy full feature specifications into them. Keep project-wide ADRs under `docs/decisions/` and link to them from affected feature documents.
- Create these documents as a project or feature reaches the relevant phase. Do not install document templates or create phase documents during `$cy-dlc init`.

### Independent feature work

- Treat each feature as its own requirements-to-validation workstream with separate phase status and approvals.
- When the team uses Git branches, keep isolated feature work in its own branch. Do not switch branches, merge work, or resolve another owner's conflicting edits without direction.
- Keep changes inside the feature workspace when shared architecture, contracts, and other features are unaffected. Coordinate edits to root documents, shared contracts, or another feature's current behavior.
- Before editing, inspect the current branch and working tree. If a relevant document has concurrent or uncommitted changes, surface the conflict and coordinate rather than overwriting them.

### Changes affecting earlier features

When a proposed feature changes approved behavior or design in an earlier feature, automatically use the `change-impact-review` skill; do not wait for the user to name it. The user asking for an explanation or clarification alone does not trigger a scope change.

The change-owning feature records the new or changed requirement and links to each affected feature and requirement ID. Preserve the earlier feature's original approved baseline and acceptance history. Add an amendment or supersession note to the affected earlier feature document only where needed to keep its current behavior clear, linking to the change-owning feature rather than duplicating its full specification. Update root documents only for project-wide or integrated effects. Identify shared files, contracts, dependencies, migration, and verification work, then obtain approvals for each affected phase before implementation resumes. Preserve approvals for unaffected features.
## Phase 1: Requirements Discovery

Your first responsibility is to interview the user.

Ask questions about:
- Whether this is a greenfield project or a feature in an existing project.
- For a feature, its ID/title, owner when known, dependencies, and prior features or shared contracts it may affect.
- Business objectives and application purpose
- Functional requirements
- Non-functional requirements
- Expected users and use cases
- Technology preferences and constraints
- Security, performance, and scalability
- Data storage and integrations

Ask questions conversationally, in manageable groups.

Do not generate code during this phase.

## Phase 2: Requirements Definition

After gathering answers:
- Summarize functional requirements.
- Define non-functional requirements.
- Identify assumptions and constraints.
- Identify out-of-scope functionality.
- Define acceptance criteria.

Create or update the requirements document for the active workstream:
- Greenfield project: `docs/requirements.md`.
- Major feature: `docs/features/<FEATURE-ID>/requirements.md`.
Update root requirements only for project-wide requirements and link to feature requirements.

Present it to the user for review.

Wait for explicit approval.

## Phase 3: Architecture and Design

Propose:
- High-level architecture
- Component responsibilities
- Project and folder structure
- API design and endpoints
- Data model and database design
- Authentication and authorization
- Error handling and logging
- Caching strategy, if applicable
- Testing strategy
- Deployment architecture

Include architecture diagrams where useful.

Create or update the architecture document for the active workstream:
- Greenfield project: `docs/architecture.md`.
- Major feature: `docs/features/<FEATURE-ID>/architecture.md`.
Update root architecture for integrated design or shared contracts and link to feature architecture.

Wait for explicit approval.

## Architecture Decision Records

During Architecture and Design, record significant technical decisions when they have lasting consequences, involve meaningful tradeoffs, or are difficult to reverse. Examples include data storage, caching strategy, authentication, external integrations, and deployment architecture.

Store approved records in `docs/decisions/` as `ADR-NNN-short-title.md`. Each record should briefly state the context, decision, alternatives considered, rationale, and consequences.

Keep `docs/architecture.md` as the current design reference. Link to relevant ADRs from it. Mark a superseded ADR and link to the decision that replaces it; do not silently rewrite the history of an approved decision.
## Phase 4: Implementation Planning

Break implementation into:
- Development phases
- Features and tasks
- Dependencies
- Files to create or modify
- Testing requirements
- Acceptance criteria

Create or update the implementation plan for the active workstream:
- Greenfield project: `docs/implementation-plan.md`.
- Major feature: `docs/features/<FEATURE-ID>/implementation-plan.md`.
Update the root plan for cross-feature sequencing and integration dependencies; link to feature plans.

Present the complete plan.

Wait for explicit approval before coding.

## Phase 5: Construction

Only after approval:
- Implement the approved architecture.
- Follow the approved implementation plan.
- Work incrementally.
- Build and test after meaningful changes.
- Do not introduce unapproved architectural changes.
- Report deviations and request approval when necessary.

## Phase 6: Testing and Validation

- Execute unit tests.
- Execute integration tests.
- Validate API contracts.
- Check error handling and edge cases.
- Review security and performance concerns.
- Report test results and remaining issues.

## Change Impact and Document Sync

For proposed changes to approved requirements, scope, constraints, acceptance criteria, or design decisions during a project, use the change-impact-review skill when available, then follow this section's approval rules.

When the user proposes a change during or after a phase:

1. Pause work on the affected decisions and assess the change against the approved requirements, acceptance criteria, architecture, API contracts, data model, security, tests, and implementation plan.
2. Identify the earliest phase affected. If product behavior, scope, constraints, or acceptance criteria change, return to Requirements Definition. If those remain valid but the solution changes, return to Architecture and Design. Update the implementation plan when its tasks, dependencies, files, or verification need to change.
3. Update every affected canonical document together. Mark superseded decisions and record what changed and why; do not leave conflicting versions in place.
4. Present the impact summary and revised documents to the user. Obtain explicit approval for each affected major phase before resuming work. Approval for unaffected phases remains valid.
5. If implementation has already started, identify existing code, tests, or data affected by the change and include the required reconciliation work in the revised plan.

Use stable requirement identifiers where useful (for example, `FR-01` for a functional requirement and `NFR-01` for a non-functional requirement). Reference relevant identifiers in architecture, API, and test documentation so omissions are easier to detect. Treat root `docs/requirements.md` as the source of truth for project-wide requirements and acceptance criteria; the relevant `docs/features/<FEATURE-ID>/requirements.md` is authoritative for detailed requirements and acceptance criteria for that feature.
## Approval Rules

The user must explicitly approve each major phase.

Approval of one phase does not automatically approve all subsequent phases.

If requirements change, revisit the affected design and implementation decisions before proceeding.
<!-- END CY-DLC: managed workflow -->
