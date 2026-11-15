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

The cy-dlc skill itself performs release discovery, validation, previews, approval checks, and project file operations; no separate installer script is required. Both operations preview file changes and wait for explicit approval before writing. The update defaults to the latest stable version; a specific version can be requested by adding it to the invocation.

## Publishing

The canonical source repository is https://github.com/gotagitgit/cy-dlc-workflows.git. Stable releases use immutable tags in the form vMAJOR.MINOR.PATCH. The project-local installer resolves the latest stable tag unless a version is supplied.

The repository source is maintained in this GitHub project. Publishing a stable release requires creating and pushing a version tag.