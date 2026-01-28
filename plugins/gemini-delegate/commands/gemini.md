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
1. Spawn the gemini-worker agent with the task
2. Review the results Gemini produces
3. Present a summary to the user with your analysis

## Steps

1. **Spawn gemini-worker agent** with the user's task:
   - Use the Task tool with `subagent_type: "gemini-delegate:gemini-worker"`
   - Pass the user's task description in the prompt
   - Run in background if it's a long task

2. **Review Gemini's output**:
   - Analyze what Gemini produced
   - Check for correctness and quality
   - Identify any issues or improvements needed

3. **Present to user**:
   - Summarize what Gemini did
   - Highlight key changes or code generated
   - Provide your assessment (approve, needs changes, or reject)
   - If changes look good, offer to apply them

## Example

User: `/gemini create a Python function to validate email addresses`

You spawn gemini-worker with:
```
prompt: "Create a Python function to validate email addresses using regex. Include docstring and type hints."
```

Then review and present:
```
Gemini produced an email validation function. Here's my review:

**Generated Code:**
[show the code]

**Assessment:** The implementation looks correct. It uses a standard regex pattern and includes proper type hints.

Would you like me to apply this to a specific file?
```
