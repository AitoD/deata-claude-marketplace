---
description: Delegate a task to Gemini CLI and review the results
argument-hint: "<task description>"
allowed-tools:
  - Task
  - Read
  - Glob
  - Bash
---

# Gemini Delegation Command

The user wants to delegate a task to Gemini CLI. You will:
1. Construct a COMPLETE prompt for the subagent (including gemini command + output format)
2. Spawn the gemini-worker agent in the background
3. Review results when Gemini completes
4. Present a summary to the user with your analysis

## CRITICAL: Subagent Cannot See Plugin Files

The subagent:
- **CANNOT** see the gemini-worker.md instructions
- **CANNOT** use plugins
- **ONLY** sees what you put in the `prompt` parameter
- **ONLY** has access to: Bash, Read, Glob tools

**YOU must construct the COMPLETE prompt including the gemini command and output format.**

## Step 1: Construct the Gemini CLI Command

Based on the user's task, construct the appropriate gemini command:

**For file-specific tasks:**
```bash
gemini --approval-mode plan "task description" /path/to/file.cpp 2>&1
```

**For standalone prompts (no files):**
```bash
gemini -p "task description" 2>&1
```

## Step 2: Spawn Agent with COMPLETE Prompt

Use this template - fill in [TASK] and [COMMAND]:

```
Task(
  subagent_type: "gemini-delegate:gemini-worker",
  run_in_background: true,
  prompt: '''You are a Gemini CLI worker. Your job is to run the gemini command and report results.

## YOUR TASK
[TASK DESCRIPTION HERE]

## COMMAND TO RUN
Execute this exact command using the Bash tool:
```bash
[GEMINI COMMAND HERE]
```
Use timeout of 300000ms (5 minutes).

## OUTPUT FORMAT - MANDATORY
You MUST structure your output EXACTLY like this:

[AGENT] === GEMINI WORKER STARTED ===
[AGENT] Task: [describe the task]
[AGENT] Command: [the command you will run]
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
1. You MUST run the gemini command using Bash tool - this is your primary job
2. You MUST capture stderr with 2>&1
3. You MUST print ALL output - never truncate
4. You MUST use [AGENT] prefix for your messages
5. You MUST wrap gemini output in the markers shown above
6. If gemini fails or produces no output, still report that clearly
7. NEVER skip running the gemini command'''
)
```

## Complete Example

User: `/gemini add error handling to src/main.cpp`

You construct and spawn:
```
Task(
  subagent_type: "gemini-delegate:gemini-worker",
  run_in_background: true,
  prompt: '''You are a Gemini CLI worker. Your job is to run the gemini command and report results.

## YOUR TASK
Add comprehensive error handling to the main.cpp file.

## COMMAND TO RUN
Execute this exact command using the Bash tool:
```bash
gemini --approval-mode plan "Add comprehensive error handling with try-catch blocks, input validation, and meaningful error messages" src/main.cpp 2>&1
```
Use timeout of 300000ms (5 minutes).

## OUTPUT FORMAT - MANDATORY
You MUST structure your output EXACTLY like this:

[AGENT] === GEMINI WORKER STARTED ===
[AGENT] Task: [describe the task]
[AGENT] Command: [the command you will run]
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
1. You MUST run the gemini command using Bash tool - this is your primary job
2. You MUST capture stderr with 2>&1
3. You MUST print ALL output - never truncate
4. You MUST use [AGENT] prefix for your messages
5. You MUST wrap gemini output in the markers shown above
6. If gemini fails or produces no output, still report that clearly
7. NEVER skip running the gemini command'''
)
```

You respond immediately:
```
I've delegated this to Gemini in the background.

**Task:** Add error handling to src/main.cpp
**Output file:** /path/to/output.txt

I'll review the results when Gemini finishes.
```

## Step 3: Monitor and Review

- The agent saves output to a file (path shown when spawned)
- Tell the user the output file path so they can check progress
- When notified of completion, read the output file
- Look for the `[GEMINI-CLI OUTPUT START/END]` markers to find Gemini's response

## Step 4: Present Results

**SUCCESS case:**
```
Gemini finished! Here's the result:

**Status:** SUCCESS
**Task:** Add error handling to src/main.cpp

**Gemini's Proposal:**
[extract and show the key changes from between the GEMINI-CLI markers]

**Assessment:** [your review - approve/needs changes/reject]

Would you like me to apply these changes?
```

**FAILURE case:**
```
Gemini failed:

**Status:** FAILED
**Error:** [extract error from output]

Would you like me to:
1. Retry the task
2. Try a different approach
3. Implement it myself
```

## Troubleshooting

If output is empty or agent didn't run gemini:
1. Check if gemini CLI is installed: `where gemini` (Windows) or `which gemini` (Unix)
2. Check authentication: `gemini auth login`
3. Verify the file paths in the command exist
