---
name: cy-dlc
description: Add or update the project-local AI-DLC-inspired Codex workflow. Use only when the user explicitly invokes cy-dlc init or cy-dlc update.
---

# cy-dlc

This skill manages a project-local copy of the workflow package published by the canonical cy-dlc GitHub repository.

## Source configuration

The canonical source repository is:

    SOURCE_REPOSITORY: https://github.com/gotagitgit/cy-dlc-workflows.git

Use this repository as the source for stable tagged releases. If it cannot be reached or has no valid stable release tag, stop before writing project files and explain the issue.

Use the user's existing GitHub authentication through Git when the repository is private. Never ask for, print, or store credentials or tokens.

## Commands

Recognize these explicit invocations:

- cy-dlc init [version] — install the workflow into the current project. Omitted version means latest stable.
- cy-dlc update [version] — update the current project's installed workflow. Omitted version means latest stable.

Only manage the current project. Do not initialize or update the canonical source repository unless the user explicitly requests that task.

## Resolve a package release

1. Check that Git is available and the canonical repository is configured.
2. If the user supplied a version, use that exact stable tag.
3. Otherwise inspect tags in the canonical GitHub repository and select the highest stable semantic version tag in the form vMAJOR.MINOR.PATCH. Ignore prerelease tags.
4. Fetch that immutable tag into a temporary directory. Read package/package-manifest.json and verify that every listed source file exists.
5. Stop without writing project files if GitHub is unavailable, the tag or manifest is invalid, or the requested version does not exist.

Do not install dependencies or execute scripts from the downloaded package.

## Initialize a project

1. Identify the current project root. Use the Git root when the current directory is inside a Git repository; otherwise use the current working directory.
2. Read the package manifest and inspect every target path before proposing changes.
3. Prepare the package AGENTS.md managed block and the project-local change-impact-review skill.
4. If AGENTS.md already exists, preserve its content outside the CY-DLC markers. If the target skill path already exists, compare it with the incoming file and treat a different file as a conflict.
5. Present the release version, exact files to add or change, diffs for existing files, and any conflicts. Do not write anything until the user explicitly approves this preview.
6. Apply only approved changes. Create .cy-dlc/manifest.json with the source repository, installed version, and SHA-256 baselines for the managed AGENTS block and installed skill.
7. Confirm exactly what was installed and report any file that was skipped or unresolved.

## Update a project

1. Read .cy-dlc/manifest.json to identify the source, installed version, and prior file baselines. If the manifest is missing or invalid, stop and explain how to repair or initialize it.
2. Resolve latest stable by default or use the explicitly requested version.
3. Compare each installed managed region or file with its saved baseline and the incoming release. Identify additions, changes, removals, and local edits.
4. Show a readable diff for every proposed change. Preserve AGENTS.md content outside its managed markers.
5. For each locally modified managed file, ask whether to keep the local file or replace it with the incoming version. Do not automatically merge, replace, or delete a locally modified file.
6. Wait for explicit approval before writing. Recheck files after approval; if any changed since preview, stop and show a fresh preview.
7. Update the version and fingerprints only after all approved changes succeed. Report partial failures accurately and never claim an incomplete update succeeded.

Use an available SHA-256 utility supported by the current shell. Do not install tools just to calculate hashes. For the AGENTS managed block, hash only the content between the CY-DLC markers so unrelated user instructions do not count as local edits.

## Approval and boundaries

- A user invoking init or update authorizes a preview, not the file writes. Wait for approval after showing the preview.
- Never commit, push, create a GitHub repository, publish a release, or modify machine-wide files.
- Do not edit application source code as part of installing or updating this workflow.
- If the user asks a question or requests an explanation, answer it without treating it as approval to apply a pending preview.