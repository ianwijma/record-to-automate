# Agent instructions

## Current references

Use `planning/DECISIONS.md` for confirmed requirements and open choices. For implementation planning, use `planning/IMPLEMENTATION-PLAN.md`. For UI work, use `planning/design/DESIGN.md` and `planning/design/journey.html`. These are the current, unversioned sources of truth.

## Archive boundary

`.archive/` is human-only historical storage. Do not open, read, search, summarize, index for retrieval, or load its contents into agent context. Do not consult it for background, examples, debugging, review or planning.

Exclude `.archive/**` from repository-wide searches, globs and document discovery, including commands that include hidden or ignored files. Respect the root `.ignore` file; add an explicit archive exclusion when a tool bypasses ignore rules.

Treat archived files as opaque when performing Git operations. Preserve them without inspecting or modifying their contents. Keep active documentation self-contained; do not add links that direct agents into the archive.
