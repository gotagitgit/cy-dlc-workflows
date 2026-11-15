# cy-dlc workflow package

This repository is the source for a reusable, project-local AI-DLC-inspired workflow for Codex desktop and Codex CLI.

## Package contents

- The project workflow and approval gates in package/AGENTS.md.
- The change-impact-review skill installed into each target project.
- Package metadata in package/package-manifest.json.
- The cy-dlc entry-point skill in .agents/skills/cy-dlc/SKILL.md.

The package does not contain document templates. AGENTS.md directs Codex to create requirements, architecture, implementation-plan, and ADR documents when those phases begin.

## Use in Codex

Install the cy-dlc skill into the user's Codex skills once per machine using the built-in $skill-installer and this repository as its source.

In a target project, invoke:

    $cy-dlc init

To update a project-local installation:

    $cy-dlc update

Both operations preview file changes and wait for explicit approval before writing. The update defaults to the latest stable version; a specific version can be requested by adding it to the invocation.

## Publishing

The canonical GitHub repository owner, name, and visibility have not been selected. Before publishing, set the source repository in the cy-dlc skill and package manifest, review the files, and create an immutable stable Git tag or release.

This local checkout has no remote configured. Repository creation, commits, pushes, and release publication require separate user authorization.