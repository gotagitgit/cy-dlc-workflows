---
name: coding-standards-review
description: Review code changes against the applicable project engineering standards. Use during Construction before considering implementation complete.
---

# Coding Standards Review

Use this workflow during Construction after implementation changes are made and before considering Construction complete.

## Review

1. Read the applicable standards under `docs/engineering/`. For C# changes, read `docs/engineering/dotnet/coding-standards.md` and check `.editorconfig` and relevant project/build settings as directed by `AGENTS.md`. When the change affects architecture, domain boundaries, dependencies between layers, or project structure, also read `docs/engineering/architecture-principles.md`.
2. Inspect the current change and enough surrounding code to understand its behavior and project conventions.
3. Compare the changed code against the applicable standards. Treat the standards documents as the source of truth; do not copy their rules into this skill.
4. Report each actionable finding with its file and line, the relevant standard or setting, why the change conflicts with it, and a concrete correction.
5. Focus on the current change. Mention a pre-existing issue only when it directly affects the correctness or reviewability of that change.

## Resolve findings

During Construction, address actionable findings in the current change and review the updated change again. Repeat until no actionable findings remain.

If resolving a finding requires a product, requirements, or architecture decision, explain the decision needed and pause that correction for user direction. Do not silently make the decision. If a standard does not apply or cannot be followed, explain why and record the exception in the work summary.

This skill is a review workflow, not a substitute for build, analyzer, or test validation required by the project.