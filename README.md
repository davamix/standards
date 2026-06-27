# standards

Single source of truth for the **shared specifications** of a set of related projects.
Each project is its own independent repo and devcontainer; they stay aligned by consuming
this repo as a pinned git **submodule** at `.standards/`.

## Contents

| Document | Purpose |
| --- | --- |
| [working-with-standards.md](working-with-standards.md) | **Start here.** How the whole convention works (submodule + devcontainer + `CLAUDE.md` imports + versioning). |
| [architecture.md](architecture.md) | Shared architecture guidelines. |
| [security.md](security.md) | Shared security guidelines. |
| [api-design.md](api-design.md) | Shared API design guidelines. |
| [ROADMAP.md](ROADMAP.md) | Deferred, cross-project follow-ups. |
| [CHANGELOG.md](CHANGELOG.md) | What changed per release. |

## Reusing this repo

The **structure and convention** here are project-agnostic — the consumption mechanism
(submodule + devcontainer hydration + `CLAUDE.md` imports), the versioning workflow, and
this layout work for any set of projects (see
[working-with-standards.md](working-with-standards.md)). The **guideline content** is
stack-specific: [architecture.md](architecture.md), [security.md](security.md) and
[api-design.md](api-design.md) capture *our* decisions. To reuse this repo, **fork it**,
keep the structure, and replace those files with your own stack's guidelines.

## Quick start (consuming project)

```bash
# 1. Add as a submodule
git submodule add https://github.com/<your-org>/standards.git .standards

# 2. Hydrate on container create — in .devcontainer/devcontainer.json:
#    "postCreateCommand": "git submodule update --init --remote .standards"

# 3. Import the docs you need — in CLAUDE.md:
#    @.standards/architecture.md
#    @.standards/security.md
#    @.standards/api-design.md
```

See [working-with-standards.md](working-with-standards.md) for the full explanation and the
versioning workflow.
