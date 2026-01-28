# Deata's Claude Marketplace

Personal Claude Code plugin marketplace for custom tools and integrations.

## Installation

Add this marketplace to Claude Code:

```bash
claude marketplace add https://github.com/Deata/deata-claude-marketplace
```

Or add locally:

```bash
claude config add plugins /path/to/deata-claude-marketplace
```

## Plugins

### gemini-delegate

Delegate tasks to Gemini CLI as a subagent. Claude reviews and presents the results.

**Usage:**
```
/gemini <task description>
```

**Features:**
- Background delegation to Gemini CLI
- Claude reviews output before presenting
- Quality control and assessment

## Adding New Plugins

1. Create a new folder under `plugins/`
2. Add `.claude-plugin/plugin.json` to your plugin
3. Add your plugin to `.claude-plugin/marketplace.json`
