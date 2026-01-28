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

2. **Monitor progress** (while agent runs in background):
   - The agent saves all output to a file (path shown when spawned)
   - You can check progress anytime with: `Read` tool on the output file
   - Or use: `Bash tail -f <output_file>` to follow live updates
   - System will notify you when agent completes
   - **Tell the user where to find the output file** so they can check progress themselves

3. **When agent completes** (or if it fails):
   - You'll receive a notification
   - Check the agent's output immediately
   - **If it failed:** Tell user immediately what went wrong and why
   - **If it succeeded:** Proceed to review

4. **Review Gemini's output**:
   - Analyze what Gemini produced
   - Check for correctness and quality
   - Identify any issues or improvements needed
   - **Show the user key excerpts** from Gemini's output

5. **Present to user**:
   - **Status:** SUCCESS or FAILED
   - **Summary:** What Gemini did (or why it failed)
   - **Key changes:** Highlight code generated or modifications proposed
   - **Assessment:** Your professional review (approve, needs changes, or reject)
   - **Output location:** Remind them where full output is saved
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
I've delegated this to Gemini in the background.

**Status:** Gemini is working on: "Create a Python function to validate email addresses"
**Output file:** /path/to/output.txt (you can check this anytime for progress)

I'll let you know when it finishes. While it works on that, is there anything else you'd like me to help with?
```

Later, when Gemini completes, you review and present:

**SUCCESS case:**
```
Gemini finished successfully! Here's the result:

**Status:** ✓ SUCCESS
**Task:** Create Python email validation function
**Output saved to:** /path/to/output.txt

**Generated Code:**
[show the code excerpt]

**Assessment:** The implementation looks correct. It uses a standard regex pattern and includes proper type hints.

Would you like me to apply this to a specific file?
```

**FAILURE case:**
```
Gemini encountered an error:

**Status:** ✗ FAILED
**Task:** Create Python email validation function
**Error:** API connection timeout (ERR_STREAM_PREMATURE_CLOSE)
**Output saved to:** /path/to/output.txt

The API connection was interrupted. This might be a temporary issue with Gemini's servers.

Would you like me to:
1. Retry the task
2. Try a different approach
3. Implement it myself
```
