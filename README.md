# Linkup Agent Skills

Reusable knowledge modules that teach AI agents how to use [Linkup](https://linkup.so), the web search API for AI. One command installs them into your project:

```bash
npx skills add LinkupPlatform/skills
```

The installer detects your environment and places the skills where your agent finds them automatically — no manual invocation needed.

> **Source of truth:** these skills are generated from
> [**linkup-for-agents**](https://github.com/LinkupPlatform/linkup-for-agents),
> the full Linkup knowledge pack (knowledge files + 18 workflow recipes).
> Installing from either repo gives you the same skills; browse the pack for
> the underlying knowledge and ready-to-adapt recipes.

## Available skills

| Skill | Use for |
|-------|---------|
| `linkup-search` | Any web lookup or research query — the default. Depth selection, output types, query-as-retrieval-plan |
| `linkup-fetch` | Reading one known URL as clean Markdown |
| `linkup-research` | Minutes-long, multi-source investigations via `/v1/research` |
| `linkup-extract` | Bulk structured rows from one listing page via `/v1/extract` |
| `linkup-workflow` | Turning a business goal into a multi-step Linkup workflow, with 18 bundled recipes |

Each skill is self-contained: the knowledge files it relies on are bundled in its own `references/` directory.

## How skills work

Skills are **knowledge for AI agents**, not tools. They provide procedural knowledge that agents load when a user request matches the skill's purpose. The agent then follows the skill's guidance to make better API calls — whether through MCP integration, SDK code generation, or direct REST calls.

## Prerequisites

- A [Linkup API key](https://app.linkup.so) (free tier available)
- Linkup connected to your agent via [MCP](https://docs.linkup.so/pages/integrations/mcp/mcp), the [SDKs](https://docs.linkup.so/pages/sdk/python/python), or the [raw API](https://docs.linkup.so/pages/documentation/get-started/quickstart)

## Supported environments

| Environment | How it loads |
|---|---|
| **Claude Code** | Loaded automatically when relevant |
| **Cursor** | Loaded via `.cursor/rules/` |
| **Windsurf** | Loaded via `.windsurfrules` |
| **Cline / GitHub Copilot** | Loaded from project-level instruction files |

## Learn More

- [linkup-for-agents](https://github.com/LinkupPlatform/linkup-for-agents) — the full knowledge pack
- [Linkup Docs](https://docs.linkup.so) — [For AI agents](https://docs.linkup.so/pages/documentation/get-started/for-agents)
- [Discord](https://discord.com/invite/9q9mCYJa86)
