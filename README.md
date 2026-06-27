# standards

Single source of truth for the **shared specifications** of the application ecosystem
(calendar, kanban, mind map, AI assistant, …). Each app is its own independent repo and
devcontainer; they stay aligned by consuming this repo as a pinned git **submodule** at
`.standards/`.

## Contents

| Document | Purpose |
| --- | --- |
| [working-with-standards.md](working-with-standards.md) | **Start here.** How the whole convention works (submodule + devcontainer + `CLAUDE.md` imports + versioning). |
| [architecture.md](architecture.md) | Shared architecture guidelines. |
| [security.md](security.md) | Shared security guidelines. |
| [api-design.md](api-design.md) | Shared API design guidelines. |
| [ROADMAP.md](ROADMAP.md) | Deferred, ecosystem-level follow-ups. |
| [CHANGELOG.md](CHANGELOG.md) | What changed per release. |

## Quick start (consuming project)

```bash
# 1. Add as a submodule
git submodule add https://github.com/davamix/standards.git .standards

# 2. Hydrate on container create — in .devcontainer/devcontainer.json:
#    "postCreateCommand": "git submodule update --init --remote .standards"

# 3. Import the docs you need — in CLAUDE.md:
#    @.standards/architecture.md
#    @.standards/security.md
#    @.standards/api-design.md
```

See [working-with-standards.md](working-with-standards.md) for the full explanation and the
versioning workflow.
