# Saber Arsenal

Saber Arsenal is Saber's open-source GTM and RevOps skills library for Claude Code, inspired by [obra/superpowers](https://github.com/obra/superpowers). It packages reusable sales and revenue intelligence skills that plug directly into Claude Code via the plugin system. Many skills are powered by the [Saber CLI](https://saber.app) — each skill will check for the CLI and offer a fallback path if it is not installed.

## Installation

### Marketplace install (recommended)

```
/plugins add-marketplace saberapp/arsenal-marketplace
/plugins install saber-arsenal
```

### Direct URL install (contributors and testers)

```
/plugins install --url https://github.com/saberapp/arsenal
```

## Available skills

| Skill | Description | Requires Saber CLI |
|---|---|---|
| `signal-discovery` | Define buying signals that match your ICP — start here. | Optional |
| `create-company-signals` | Activate company-level signal tracking. | Yes |
| `create-contact-signals` | Activate contact-level signal tracking. | Yes |
| `build-account-list` | Build a target account list and run company signals. | Yes |
| `build-contact-list` | Build a target contact list and run contact signals. | Yes |

## Contributing a new skill

1. Create a directory under `skills/` named after your skill (e.g. `skills/my-skill/`)
2. Add a `SKILL.md` file inside that directory with YAML frontmatter and prompt content:

```markdown
---
name: my-skill
description: One-line description of what this skill does.
---

# My Skill

Prompt content goes here. Describe what Claude should do when this skill is invoked.
If the skill uses the Saber CLI, include a "Saber CLI check" section that handles the
case where the CLI is not installed.
```

3. Open a pull request — skills are automatically picked up from the `skills/` directory.

## License

MIT — see [LICENSE](./LICENSE)
