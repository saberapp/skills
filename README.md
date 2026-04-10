# Arsenal

Arsenal is Saber's open-source GTM and RevOps skills library for Claude Code, inspired by [obra/superpowers](https://github.com/obra/superpowers). It packages reusable sales and revenue intelligence skills that plug directly into Claude Code via the plugin system.

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

| Skill | Description |
|---|---|
| `hello-world` | Placeholder skill confirming Arsenal is installed. Use as a template for new GTM skills. |

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
```

3. Open a pull request — skills are automatically picked up from the `skills/` directory.

## License

MIT — see [LICENSE](./LICENSE)
