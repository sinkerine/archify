# Pi artifacts delivery

When the Pi `artifact_publish` tool is available in the session, publish every Archify HTML output through `pi-artifacts` instead of delivering only a local file path or launching a desktop browser opener.

## Workspace location

- Put each new Archify artifact in its own subdirectory under `~/.cache/archify/<project-name>/`:
  `~/.cache/archify/<project-name>/<type>-<slug>-<YYYYMMDD-HHMMSS>/`
  where `<project-name>` is the current repository/project directory name (e.g. `basename` of the repo root or working directory).
- For Code Analysis, pass `--out ~/.cache/archify/<project-name>/code-analysis-<YYYYMMDD-HHMMSS>`.
- Keep `candidate.json` (or `candidate.archify.md`), receipts, and `<slug>.html` in that folder unless the user explicitly asks to write files into the repository.
- Run `node <skill-dir>/bin/archify.mjs finalize ...` as usual, passing the absolute HTML output path inside that subdirectory.

## Publishing

After `finalize` (native diagram, explainer, story) or `code-analysis prepare` succeeds:

1. Call `artifact_publish` with:
   - `title`: concise human-readable title
   - `kind`: `"html"`
   - `entrypoint`: absolute path to the generated `<slug>.html` (or `repo.analysis.html`)
   - `files`: explicit list containing the entrypoint (plus any sibling drilldown `.html` files in the same directory; omit `.json` candidates and receipts)
2. For iterative refinements or layout repairs of the same diagram in this conversation, pass the existing artifact `id` to `artifact_publish` so the open preview tab live-updates to the new revision.
3. Lead the reply with the clickable `previewUrl` (and include `revisionUrl` when a pinned snapshot is helpful). Do not run `open-artifact.mjs` or `--open` when `artifact_publish` succeeds.
4. For a repository README diagram where the user explicitly asks for an embedded static SVG, export the SVG into the repository as requested; if an interactive preview is also useful, publish the HTML via `artifact_publish`.

## HTML vs React (`kind: "html"` vs `kind: "react"`)

- Always publish Archify outputs with `kind: "html"`. Archify compiles JSON-IR into a self-contained SVG + vanilla JS viewer (`template.html`) with built-in pan/zoom, lenses, dark/light themes, and SVG/PNG/WebM export; wrapping it in `kind: "react"` would add esbuild overhead without improving the diagram viewer.
- Prefer `pi-artifacts` `kind: "react"` (with `recharts`, `lucide-react`, Tailwind) for dashboards, stateful UI mockups, or interactive data explorers—use cases Archify explicitly excludes.
