# Linkup Agent Skills

Linkup Agent Skills are reusable knowledge modules that help AI agents use [Linkup](https://linkup.so), a web search API that connects AI apps to the internet. Linkup is [#1 in the world for factuality](https://www.linkup.so/blog/linkup-establishes-sota-performance-on-simpleqa), scoring state-of-the-art results on OpenAI's SimpleQA benchmark.

## Available Skills

**linkup-search** teaches agents how to use the Linkup API effectively — choosing between standard and deep search, writing precise queries, selecting output types, scraping webpages, filtering domains, and using proven prompt patterns for business intelligence and research.

## How Skills Work

Skills are **knowledge for AI agents**, not tools. They provide procedural knowledge that agents load when a user request matches the skill's purpose. The agent then follows the skill's guidance to make better API calls — whether through MCP integration, SDK code generation, or direct user instruction.

## Getting Started

### Prerequisites

- A [Linkup API key](https://app.linkup.so) (free tier available)
- Linkup connected to your agent via [MCP](https://docs.linkup.so/pages/integrations/mcp/mcp), [SDK](https://docs.linkup.so/pages/sdk/python/python), or [direct API](https://docs.linkup.so/pages/documentation/get-started/quickstart)

### Install the Skill

```bash
npx skills add LinkupPlatform/skills
```

This installs the `linkup-search` skill into your project. The installer detects your environment and places the skill file where your agent will find it automatically.

### Supported Environments

| Environment | How it loads |
|---|---|
| **Claude Code** | Loaded automatically when relevant |
| **Cursor** | Loaded via `.cursor/rules/` |
| **Windsurf** | Loaded via `.windsurfrules` |
| **Cline / GitHub Copilot** | Loaded from project-level instruction files |

Once installed, no manual invocation is needed — agents detect the skill and reference it whenever they handle web search, content extraction, or research tasks via Linkup.

## Learn More

- [Linkup Docs](https://docs.linkup.so)
- [Linkup API Reference](https://api.linkup.so/docs)
- [Prompt Templates](https://prompt.linkup.so/templates)
- [Discord](https://discord.com/invite/9q9mCYJa86)
