---
name: delegate-luna
description: Delegate a task to GPT-6 Luna with high reasoning, then independently validate the deliverable before accepting it. Use when Luna delegation or delegated execution with validation is requested.
---

# Delegate and validate with Luna

Delegate the requested task to Luna and retain responsibility for checking the result against the user's requirements.

## Assign the task

Define the expected deliverable, scope, constraints, and observable acceptance criteria. Include relevant file paths, context, and commands the worker needs. Specify whether it may edit files or should return findings only. Agents share the workspace, so avoid overlapping edits with the worker.

Call `collaboration.spawn_agent` directly with:

```json
{
  "task_name": "luna_task",
  "model": "gpt-6-luna",
  "reasoning_effort": "high",
  "fork_turns": "none",
  "message": "<Self-contained task, relevant context, constraints, deliverable, acceptance criteria, and requested validation evidence>"
}
```

Use `fork_turns: "none"` because explicit model and reasoning overrides require it. The worker receives no parent history: include all necessary user requirements and applicable repository instructions in the message. Ask it to report artifacts or changed files, checks run and their outcomes, and any unresolved issues. Request evidence and concise rationale, not private reasoning.

If the tool or the exact model/reasoning setting is unavailable, report the limitation instead of silently substituting another model or claiming delegation occurred. Delegation does not expand the user's authorization or bypass tool permissions.

## Validate the result

Wait for the worker's completed result; use messages for targeted clarification or progress as needed. You may perform independent work while it runs, provided it does not conflict with its edits.

Inspect the actual deliverable rather than accepting the worker's summary as proof. Compare it with the acceptance criteria and check for unintended changes. For code, review the diff and run checks appropriate to the changed behavior. For analysis or writing, check the underlying evidence, calculations, coverage, and requested format. Scale validation to the task's impact; avoid checks that merely restate the implementation.

If a material defect remains, send a focused correction to the same Luna worker with `collaboration.followup_task`, then inspect and validate its revised output. Keep corrections within the original scope. If repeated attempts do not improve the result, investigate or finish the repair directly when authorized, or explain the specific blocker; do not keep retrying without new evidence.

Accept the result only when the relevant criteria are satisfied. Report the completed outcome and meaningful validation evidence, and distinguish any remaining uncertainty or checks that could not be run. Do not present an unchecked worker response as a validated result.
