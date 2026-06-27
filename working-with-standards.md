# Working with `standards`

This repository is the **single source of truth** for the cross-cutting specifications
shared across a set of related projects. Each project stays fully independent — its own
code, containers, repo and devcontainer — yet they all stay aligned because they consume
the *same* pinned copy of these documents.

This page explains how that works so that anyone (human or AI assistant) opening any
project knows the convention.

## The problem this solves

Every project is isolated: a separate repo, a separate devcontainer. That isolation is a
feature — but it means common specifications (architecture, security, API design) would
otherwise be copy-pasted and drift apart. It also means an AI assistant working *inside*
one devcontainer has no visibility into the other projects, so anything it should follow
has to be **physically present on disk in that container**.

The design therefore has three layers:

1. **Source of truth** — these specs live here, in one versioned repo.
2. **Materialization** — each project pulls this repo onto disk as a git **submodule** at
   `.standards/`, hydrated automatically when the devcontainer is created.
3. **AI surfacing** — each project's `CLAUDE.md` `@import`s the relevant docs from
   `.standards/`, so the shared guidelines load into the assistant's context
   automatically alongside the project-specific instructions.

The key consequence: the moment anyone works in *any* devcontainer, the same pinned
guidelines are on disk and in context — without the project knowing the other projects
exist.

## Repository layout

```
standards/
  README.md                  # short overview + quick start
  working-with-standards.md  # this document — the convention
  architecture.md            # shared architecture guidelines
  security.md                # shared security guidelines
  api-design.md              # shared API design guidelines
  ROADMAP.md                 # deferred, cross-project follow-ups
  CHANGELOG.md               # what changed per release
```

## How a project consumes the standards

Each consuming repo follows the same two-folder convention:

```
<project>/
  docs/         # specs that belong to THIS project only
  .standards/   # this repo, as a pinned git submodule (shared, do not edit here)
  CLAUDE.md     # @imports the shared docs it cares about
```

### 1. Add the submodule (once per project)

```bash
git submodule add https://github.com/<your-org>/standards.git .standards
git commit -m "Add standards submodule"
```

### 2. Hydrate it automatically in the devcontainer

In `.devcontainer/devcontainer.json`:

```jsonc
"postCreateCommand": "git submodule update --init --remote .standards"
```

So every freshly created container has `.standards/` populated before any work begins.

### 3. Surface it to the AI assistant

In the project's `CLAUDE.md`:

```md
@.standards/architecture.md
@.standards/security.md
@.standards/api-design.md
```

These are ordinary filesystem paths relative to `CLAUDE.md`; once `.standards/` is
hydrated they resolve and the shared guidelines load into context automatically.

## Versioning workflow

The specs are **pinned per project** — a project records the exact commit of `standards`
it was built against, so guidelines never change under a project by surprise, and the AI
assistant always sees a consistent, known version.

- Changes here go through a **pull request** and a **tagged release** (`v0.1.0`,
  `v0.2.0`, …) recorded in `CHANGELOG.md`.
- A submodule pins a **commit**. A project upgrades deliberately, when it's ready:

  ```bash
  cd .standards && git fetch && git checkout v0.2.0 && cd ..
  git add .standards && git commit -m "Bump standards to v0.2.0"
  ```

- `postCreateCommand` uses `--remote` for convenience, but the committed submodule pointer
  is what defines the project's pinned version. Commit the pointer to lock it.

## What belongs here vs. in a project

- **Here (`standards`)** — anything that should be true across *all* projects:
  architecture guidelines, security guidelines, API design conventions, shared design
  tokens, cross-project decisions. Also cross-project follow-ups in `ROADMAP.md`.
- **In a project's `docs/`** — anything specific to that one project: its domain model,
  its own decisions, its own backlog.

Rule of thumb: if a change would benefit more than one project, it belongs here. If it
only matters to one project, it belongs in that project's `docs/`.

## Editing rules

- Never edit files under a consuming project's `.standards/` directory — that's a checkout
  of this repo. Change things **here**, release, then bump the project's pointer.
- Keep documents short, prescriptive, and skimmable. These are guidelines projects must
  follow, not essays.
