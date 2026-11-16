---
name: cy-dlc
description: Add or update the project-local AI-DLC-inspired Codex workflow. Use only when the user explicitly invokes cy-dlc init or cy-dlc update.
---

# cy-dlc

Use this skill to install or update the workflow package in the current project. The skill itself is the installer: perform the discovery, validation, preview, and file operations with the available Codex tools. Do not require a separate installer script.

## Source repository and commands

Canonical source:

    https://github.com/gotagitgit/cy-dlc-workflows.git

Recognize only these explicit operations:

- `$cy-dlc init [version]` — prepare the workflow in the current project. Omitted version means latest stable.
- `$cy-dlc update [version]` — prepare an update to the installed workflow. Omitted version means latest stable.

An invocation authorizes discovery and a preview, not writes. After showing the complete preview, wait for the user's explicit approval before changing project files. Do not commit, push, create tags/releases, or change machine-wide files.

## Common procedure

1. Determine the project root. If the current directory is inside a Git worktree, use `git rev-parse --show-toplevel`; otherwise use the current working directory. If this does not identify the project clearly, ask which directory to manage.
2. Check that Git is available and the source can be read. Use the user's existing Git authentication for a private repository. Never request, print, or save credentials.
3. Resolve a package release as described below and fetch it into a unique temporary directory. Use Git commands only; do not execute scripts, hooks, binaries, or other code from the downloaded package. Remove only the temporary directory created for this operation after finishing.
4. Validate the release and every source and target path before preparing changes.
5. Read the relevant target files and project install manifest. Prepare all proposed content in memory or temporary files; do not write to the project during discovery or preview.
6. Show the release version, project root, each file to add/change/keep, and readable diffs for every proposed change. Call out conflicts and local edits with clear choices. Include the install manifest change.
7. Wait for explicit approval. A question, clarification, or request to explain the preview is not approval. If the user asks to revise or clarify it, update the preview and ask again.
8. After approval, re-read every affected file and confirm it still matches the state used for the preview. If anything changed, stop and show a refreshed preview.
9. Apply only the approved file decisions. Write the project install manifest last. If any write fails, report exactly what succeeded and failed; do not claim the installation/update completed.

## Resolve and validate a release

- For an explicit version, require a stable tag exactly matching `vMAJOR.MINOR.PATCH` (for example `v0.1.0`).
- Otherwise list remote tags with `git ls-remote --tags --refs https://github.com/gotagitgit/cy-dlc-workflows.git`, keep only tags matching that exact pattern, and choose the highest numeric semantic version (compare major, then minor, then patch; do not sort these strings lexically).
- Fetch/check out only that tag into a unique temporary directory. If the repository is unreachable, no stable tags exist, or the requested tag is absent, stop without changing project files.
- Read `package/package-manifest.json`. Require `schemaVersion` 1, package ID `cy-dlc-project-workflow`, a version matching the selected tag without its leading `v`, and a non-empty `files` array.
- For every file entry, require a source path within the fetched repository and a relative target path within the project. Reject absolute paths, `..` traversal, duplicate targets, missing sources, malformed managed-region markers, or unsupported manifest fields that affect installation. Never let manifest paths escape either root.
- The package currently installs `package/AGENTS.md` with a managed region, the change-impact-review skill, and team-owned engineering guidance under `docs/engineering/`. Use `package/package-manifest.json` as the source of truth for all package files; do not assume a fixed file list.

## Prepare `init`

1. Stop if `.cy-dlc/manifest.json` already exists; direct the user to `$cy-dlc update` instead. If a target skill already exists, treat it as a conflict rather than overwriting it.
2. For an existing `AGENTS.md`, preserve every byte outside the package's managed markers. If both markers occur once in the correct order, replace only the enclosed managed block as the proposed change. If neither marker exists, propose appending the complete marked block with a separating newline. If only one marker exists, either marker is duplicated, or the order is invalid, stop and explain the conflict.
3. If `AGENTS.md` does not exist, propose adding the package file with its managed markers intact.
4. Propose adding each other package target. If a target exists, compare it with the incoming file. Identical content can be reported as already present; different content is a conflict and must not be overwritten without an explicit choice in the preview.
5. Propose `.cy-dlc/manifest.json` with the schema below and fingerprints for the resulting installed content.

## Prepare `update`

1. Read `.cy-dlc/manifest.json`. Require schema version 1, the expected package ID, a recognized source repository, an installed version, and fingerprints for all managed files. If it is missing or invalid, stop and explain that the project must be initialized or its manifest repaired.
2. Resolve the requested or latest stable release. If it is already installed, report that and make no writes.
3. For each installed file, compare the current managed content with `installedSha256` in the manifest. For `AGENTS.md`, hash only the content between the markers; for a whole-file skill, hash the entire file. Compare the incoming package content/managed block with `sourceSha256` from the prior manifest to identify upstream changes.
4. If current content differs from `installedSha256`, or the previously installed source fingerprint differs from the locally installed fingerprint, treat it as locally customized. Show the local and incoming differences and ask per file whether to keep local content or replace with the incoming version. Do not merge automatically.
5. Show upstream additions, changes, removals, and unchanged files. Do not silently delete files. For an incoming removal, preview it and ask explicitly before deletion. Unknown files inside the package install manifest are not touched.
6. If a target file is missing or the manifest cannot safely map it to a package target, stop and explain the conflict rather than recreating or deleting it silently.
7. After the user's approved choices are applied, set the release version in the project manifest. For every managed file store both the release-source fingerprint and the actual installed fingerprint. This preserves a record of local choices so later updates can detect them.

## Project manifest

Write `.cy-dlc/manifest.json` as UTF-8 JSON with this shape (fingerprints are lowercase hexadecimal SHA-256):

    {
      "schemaVersion": 1,
      "packageId": "cy-dlc-project-workflow",
      "sourceRepository": "https://github.com/gotagitgit/cy-dlc-workflows.git",
      "installedVersion": "0.1.1",
      "files": {
        "AGENTS.md": {
          "managedRegion": true,
          "sourceSha256": "<hash of incoming managed block>",
          "installedSha256": "<hash of installed managed block>"
        },
        ".agents/skills/change-impact-review/SKILL.md": {
          "managedRegion": false,
          "sourceSha256": "<hash of incoming whole file>",
          "installedSha256": "<hash of installed whole file>"
        }
      }
    }

Hash UTF-8 file content with SHA-256 using an available system utility or Codex tool. Hash only the text between the exact AGENTS markers, excluding the markers themselves. Do not install utilities to compute hashes. Validate the resulting JSON before writing it.

## Choices, failure handling, and boundaries

- For a locally customized file, accept only a clear per-file keep/replace decision. A general approval of the preview does not resolve an explicitly presented conflict unless the user clearly approves the listed resolution.
- Keeping local content advances the package version but records the incoming source fingerprint and the preserved local installed fingerprint. Replacing content records the incoming fingerprint for both.
- If the user declines, make no project changes. If the user asks a question, explain without applying anything.
- If a file changes after preview, do not apply stale content; regenerate the preview.
- Preserve user-owned `AGENTS.md` text outside the managed markers. Do not touch unrelated project files.
- Never store credentials in the project or manifest. Do not execute downloaded package content.
- Do not commit or push the target project. Report the installed version and exact files changed only after successful completion.
