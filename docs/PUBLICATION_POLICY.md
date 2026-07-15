# Publication Policy

This repository is the public OpenNextion ESP32 Marauder example repository. It
must not publish local development coordination records, bring-up notes, thread
handoff documents, generated firmware files, or private environment notes.

## Public Documentation Scope

The public `docs/` tree is intentionally small:

- `docs/BUILD_AND_FLASH.md`
- `docs/RELEASE_FLASHING.md`
- `docs/SUPPORTED_BOARDS.md`
- `docs/PUBLICATION_POLICY.md`
- `docs/images/`

Any new public document should be reviewed before it is committed.

## Files That Must Stay Out Of The Public Tree

The public repository must not track:

- Codex thread coordination records
- milestone or planning records
- local environment notes
- board bring-up acceptance logs
- pull request drafts or private reviewer notes
- release candidate or beta firmware staging folders
- generated firmware binaries such as `opennextion-esp32-marauder-*.bin`
- temporary Arduino build directories
- serial logs that contain private network information

Firmware files are GitHub Release assets only. They are not committed to git.

## Required Release Flow

Before any public repository push:

1. Confirm the staged file list is limited to the intended publication change.
2. Confirm generated firmware binaries are not staged.
3. Confirm README and docs links point to public URLs or planned placeholders.
4. Do not move, rebuild, delete, or retag an existing release tag for README or
   documentation-only updates.

For each public firmware release, create a release tag from the reviewed release
commit and keep older release tags and assets unchanged. Do not rewrite existing
release tags or replace already published assets for documentation-only changes.
