# Roadmap

Deferred, **cross-project** follow-ups — work that concerns the `standards` repo itself
or the shared tooling, and is therefore agnostic from any single consuming project. These
were deliberately postponed so the core convention (submodule + devcontainer + `CLAUDE.md`
imports) could land first.

Project-specific follow-ups do **not** belong here — they live in each project's own
`docs/`.

---

## 1. Render `standards` as a static documentation site

**Status:** deferred (not started).

**What:** publish these markdown docs as a browsable static site for humans (e.g. MkDocs
Material or Docusaurus), built in CI on each push/tag.

**Why:** nicer reading/navigation/search than raw markdown for people. The markdown in
this repo **stays the source of truth** — the site is only a generated view, so the AI
assistant always reads the files directly from `.standards/` and never depends on the site.

**Rough approach:**
- Add a static-site generator config at the repo root.
- GitHub Actions workflow: build on push to `main` and on tags; deploy to GitHub Pages.
- Keep zero impact on consumers — the submodule contract is unchanged.

**Done when:** tags publish a versioned site without changing how projects consume the
markdown.

---

## 2. Claude Code plugin for reviewers

**Status:** deferred (not started).

**What:** package the guidelines as a Claude Code **plugin** distributed from a git repo
and installed in each devcontainer — turning the specs from passive reference docs into
*active* behaviors (rules + slash commands), aimed especially at code review.

**Why:** today the guidelines are imported as context via `CLAUDE.md`. A plugin can enforce
them — e.g. `/api-review` checking a diff against `api-design.md`, or a security checklist
from `security.md` — consistently across every project, without each repo re-implementing
the tooling.

**Rough approach:**
- Author the plugin (slash commands / rules) referencing these documents.
- Distribute via a marketplace/git repo; install in each project's devcontainer (a
  devcontainer feature or `postCreateCommand` step).
- Start read-only/advisory; tighten over time.

**Done when:** a reviewer in any devcontainer can invoke shared, standards-backed review
commands.

---

_When an item is picked up, move its details into a PR and record the outcome in
[CHANGELOG.md](CHANGELOG.md)._
