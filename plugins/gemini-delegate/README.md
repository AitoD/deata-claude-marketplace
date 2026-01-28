# Gemini Delegate Plugin

Integrate Gemini CLI as a subagent that Claude can delegate work to without interrupting the current workflow.

## Features

- **Background delegation**: Send tasks to Gemini while Claude continues working
- **Review and present**: Claude reviews Gemini's output before presenting to user
- **Quality control**: Claude assesses and validates Gemini's work

## Prerequisites

1. Install Gemini CLI: https://github.com/google-gemini/gemini-cli
2. Authenticate: `gemini auth login`

## Usage

### Command: `/gemini <task>`

Delegate a specific task to Gemini:

```
/gemini create a utility function to parse ISO dates
/gemini refactor this function to use async/await
/gemini add unit tests for the UserService class
```

### Automatic Delegation

Claude may also use the gemini-worker agent internally to parallelize work when appropriate.

## How It Works

1. You request a task via `/gemini` or Claude decides to delegate
2. The gemini-worker agent spawns **in the background** (non-blocking)
3. **Claude continues working** on other tasks while Gemini runs
4. Gemini executes the task and produces output
5. Claude checks results when Gemini completes
6. Claude reviews the results for correctness
7. Claude presents a summary with assessment to you
8. You decide whether to apply the changes

**Key advantage**: Claude doesn't wait idle - it can continue helping you with other tasks while Gemini works in parallel.

## Components

- `agents/gemini-worker.md` - Worker agent that executes Gemini CLI
- `commands/gemini.md` - User command to delegate tasks

## Installation

This plugin is installed globally at:
```
~/.claude/plugins/gemini-delegate/
```

To use, ensure Gemini CLI is installed and authenticated.
