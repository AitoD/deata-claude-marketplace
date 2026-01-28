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
1. Spawn the gemini-worker agent in the background (non-blocking)
2. Continue working on other tasks while Gemini runs
3. Review results when Gemini completes
4. Present a summary to the user with your analysis

## Steps

1. **Spawn gemini-worker agent in BACKGROUND**:
   - Use the Task tool with `subagent_type: "gemini-delegate:gemini-worker"`
   - **CRITICAL**: Set `run_in_background: true` to avoid blocking
   - Pass the user's task description in the prompt
   - Tell user "Gemini is working on this in the background, I'll review when it's done"
   - Continue with other work while Gemini runs

2. **Check results later**:
   - Use TaskOutput tool to check on the background agent
   - Or wait for notification when it completes

3. **Review Gemini's output**:
   - Analyze what Gemini produced
   - Check for correctness and quality
   - Identify any issues or improvements needed

4. **Present to user**:
   - Summarize what Gemini did
   - Highlight key changes or code generated
   - Provide your assessment (approve, needs changes, or reject)
   - If changes look good, offer to apply them

## Example

User: `/gemini create a Python function to validate email addresses`

You immediately spawn gemini-worker in background:
```
Task(
  subagent_type: "gemini-delegate:gemini-worker",
  run_in_background: true,
  prompt: "Create a Python function to validate email addresses using regex. Include docstring and type hints."
)
```

You respond immediately (don't wait):
```
I've delegated this to Gemini in the background. While it works on that, is there anything else you'd like me to help with?
```

Later, when Gemini completes, you review and present:
```
Gemini finished! Here's what it produced:

**Generated Code:**
[show the code]

**Assessment:** The implementation looks correct. It uses a standard regex pattern and includes proper type hints.

Would you like me to apply this to a specific file?
```
