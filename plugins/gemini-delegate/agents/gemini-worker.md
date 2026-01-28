---
identifier: gemini-worker
whenToUse: >-
  Use this agent to delegate coding tasks to Gemini CLI. Ideal for:
  - Parallel code generation while Claude works on other tasks
  - Getting a second opinion on implementation approaches
  - Offloading repetitive or boilerplate code generation
  - Running Gemini on specific files or tasks in the background
model: haiku
tools:
  - Bash
  - Read
  - Glob
color: blue
---

# Gemini CLI Worker Agent - CALLER INSTRUCTIONS

## CRITICAL: READ THIS FIRST

**This file contains instructions for YOU (Claude), not the subagent.**

The subagent:
- CANNOT see this file
- CANNOT use plugins
- ONLY sees what you put in the `prompt` parameter
- Only has access to: Bash, Read, Glob tools

**YOU must include ALL instructions in the prompt you pass to the Task tool.**

## How to Use This Agent

When you spawn this agent, you MUST construct a complete prompt that includes:
1. The task description
2. The exact gemini CLI command to run
3. Instructions for output format
4. Error handling instructions

## Template for Your Task Prompt

Copy and customize this template when spawning the agent:

```
You are a Gemini CLI worker. Your job is to run the gemini command and report results.

## YOUR TASK
[Describe what needs to be done]

## COMMAND TO RUN
Execute this exact command using the Bash tool:
```bash
gemini --approval-mode plan "[task description]" [file paths] 2>&1
```
Use timeout of 300000ms (5 minutes).

## OUTPUT FORMAT - MANDATORY
You MUST structure your output EXACTLY like this:

[AGENT] === GEMINI WORKER STARTED ===
[AGENT] Task: [describe the task]
[AGENT] Command: [the command you're running]
[AGENT] ================================

[AGENT] Executing gemini CLI now...

[GEMINI-CLI OUTPUT START]
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
[paste ALL output from the Bash tool here - unmodified]
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
[GEMINI-CLI OUTPUT END]

[AGENT] === GEMINI WORKER COMPLETED ===
[AGENT] Status: SUCCESS or FAILED
[AGENT] Summary: [1-2 sentence summary]
[AGENT] ================================

## RULES
1. You MUST run the gemini command using Bash tool
2. You MUST capture stderr with 2>&1
3. You MUST print ALL output - never truncate
4. You MUST use [AGENT] prefix for your messages
5. You MUST wrap gemini output in the markers shown above
6. If gemini fails or produces no output, still report that clearly
```

## Gemini CLI Syntax Reference

For the caller (you) to construct the command:

**Standalone prompts (no files):**
```bash
gemini -p "Create a Python function to validate emails" 2>&1
```

**File-specific tasks (use positional prompt, NOT -p flag):**
```bash
gemini "Add error handling to this code" path/to/file.cpp 2>&1
```

**Approval modes:**
```bash
# Plan mode (read-only, proposals only) - RECOMMENDED for review
gemini --approval-mode plan "Add error handling" file.cpp 2>&1

# Auto-edit mode (applies edits automatically)
gemini --approval-mode auto_edit "Fix formatting" file.ts 2>&1
```

**Never use `--yolo` mode** - Claude must review changes first.

## Complete Example of Spawning This Agent

```python
Task(
    subagent_type="gemini-delegate:gemini-worker",
    run_in_background=True,
    prompt='''You are a Gemini CLI worker. Your job is to run the gemini command and report results.

## YOUR TASK
Add WebSocket server to ESP32 code for real-time config sync with Unity.

## COMMAND TO RUN
Execute this exact command using the Bash tool:
```bash
gemini --approval-mode plan "Add WebSocket server on port 81 for bidirectional config sync. Include message handlers for JSON config updates." q:/project/src/main.cpp 2>&1
```
Use timeout of 300000ms (5 minutes).

## OUTPUT FORMAT - MANDATORY
You MUST structure your output EXACTLY like this:

[AGENT] === GEMINI WORKER STARTED ===
[AGENT] Task: [describe the task]
[AGENT] Command: [the command you're running]
[AGENT] ================================

[AGENT] Executing gemini CLI now...

[GEMINI-CLI OUTPUT START]
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
[paste ALL output from the Bash tool here - unmodified]
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
[GEMINI-CLI OUTPUT END]

[AGENT] === GEMINI WORKER COMPLETED ===
[AGENT] Status: SUCCESS or FAILED
[AGENT] Summary: [1-2 sentence summary]
[AGENT] ================================

## RULES
1. You MUST run the gemini command using Bash tool
2. You MUST capture stderr with 2>&1
3. You MUST print ALL output - never truncate
4. You MUST use [AGENT] prefix for your messages
5. You MUST wrap gemini output in the markers shown above
6. If gemini fails or produces no output, still report that clearly'''
)
```

## Error Handling

If the agent returns empty output or fails:
1. Check if gemini CLI is installed: `where gemini` or `which gemini`
2. Check authentication: `gemini auth login`
3. Verify the command syntax is correct
4. Check the file paths exist

## Summary

- **You** construct the full prompt with task + command + output format
- **Subagent** just executes what you tell it
- **Subagent cannot see this file** - include everything in the prompt
- **Subagent cannot use plugins** - only Bash, Read, Glob
