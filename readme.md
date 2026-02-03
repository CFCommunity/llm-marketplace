# CFML LLMs Marketplace

CFML skills marketplace for Claude Code (and other LLMs that implement the marketplace spec)

## Quick Start

```bash
# 1. Add marketplace (one-time)
/plugin marketplace add cfcommunity/llm-marketplace

# 2. Install plugin
/plugin install taffy
```

> **Note:** Marketplace = catalog. Plugin = what you install from it.

## Skills Included

- **taffy** - Taffy syntax reference and guidance for building and maintaining Taffy-powered REST APIs.
- Add yours via PR!

## Skills Ecosystem

Part of a connected suite of tools for Claude Code power users. These projects work together to give Claude Code persistent memory, better search, framework expertise, and self-improving skills.

## Local Development

```bash
# Clone marketplace
mkdir -p ~/dev/cfcommunity
git clone git@github.com:cfcommunity/llm-marketplace.git ~/dev/cfcommunity/llm-marketplace

# 1. Add marketplace
/plugin marketplace add ~/dev/cfcommunity/llm-marketplace

# 2. Install plugin
/plugin install taffy
```

**Note:** Plugin files are cached at `~/.claude/plugins/cache/`. After editing source files, reinstall or manually sync cache.

## Versioning

Bump `version` in `plugins/svelte-skills/.claude-plugin/plugin.json` on changes.

## Credits

Inspiration / copied from: https://github.com/spences10/svelte-skills-kit
