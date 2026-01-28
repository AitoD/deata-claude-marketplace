# Gemini Delegate Plugin

Integrate Gemini CLI as a subagent that Claude can delegate work to without interrupting the current workflow.

## Features

- **Background delegation**: Send tasks to Gemini while Claude continues working
- **Full transparency**: See exactly what Gemini is doing with status updates and logs
- **Progress monitoring**: Check output file anytime to see current progress
- **Error reporting**: Immediate notification if something fails, with detailed error info
- **Review and present**: Claude reviews Gemini's output before presenting to user
- **Quality control**: Claude assesses and validates Gemini's work
- **Complete logging**: All output saved to file for later review

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
3. **You get the output file path** - you can check progress anytime
4. **Claude continues working** on other tasks while Gemini runs
5. Gemini executes the task and produces output with status updates
6. **You're notified** when Gemini completes (or if it fails)
7. Claude checks results and reviews for correctness
8. Claude presents a summary with assessment showing:
   - ✓ SUCCESS or ✗ FAILED status
   - What was produced or why it failed
   - Full output location for your review
9. You decide whether to apply the changes

**Key advantages**:
- Claude doesn't wait idle - continues helping you with other tasks
- **Full visibility** - check output file anytime to see what's happening
- **Immediate error notification** - you're told right away if something fails
- **Complete logs** - all output saved for later review

## Components

- `agents/gemini-worker.md` - Worker agent that executes Gemini CLI
- `commands/gemini.md` - User command to delegate tasks

## Installation

This plugin is installed globally at:
```
~/.claude/plugins/gemini-delegate/
```

To use, ensure Gemini CLI is installed and authenticated.
