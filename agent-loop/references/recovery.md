# Recovery

Recover within the authority of the original invocation. A failure does not grant permission for broader changes.

## First response to a stalled or failed task

1. Read the latest task status and final or partial output.
2. Inspect the repository state without changing it. Record the task branch, HEAD, commits beyond its base, worktree cleanliness, and any running checks the task reported.
3. Decide whether the blocker is factual, operational, or a product decision.

## Factual blocker

Research the answer in the repository or with an available read-only tool. Send one focused follow-up to the same task with the fact and its source. Do not answer from guesswork.

## Operational blocker

Examples include a transient command failure, an incomplete final response, a missing commit SHA, or a worker that stopped before running an available check.

Send one focused follow-up to the same task. State the unfinished requirement and the evidence already available. Preserve its branch and commits.

If the task still cannot proceed, create one replacement task from the last verified commit. Give the replacement a narrower brief, the prior task's useful evidence, and the remaining acceptance criteria. Do not tell it to redo verified work. If the replacement hits the same blocker, return to `decision_required` or stop with a safe resume point. Do not keep creating replacements.

## Product or authority blocker

Return to the conductor's `decision_required` state when progress requires:

- A change to behavior, scope, architecture, or acceptance criteria
- New credentials or permissions
- Destructive recovery
- An external write not covered by the original request
- Merge or publication approval

Present the smallest decision and a recommendation. Do not turn the worker's full transcript into the user's problem.

## Conflict recovery

For conflicts between parallel slice commits:

- A dedicated integration task may resolve mechanical conflicts when both intended behaviors remain unchanged.
- If keeping one edit changes or removes the other slice's behavior, stop and request a decision.
- Never resolve a conflict by dropping a commit, deleting uncommitted work, or rewriting shared history.

## Stop conditions

Stop the loop and report a safe resume point when:

- The user asks to stop.
- Required authority is unavailable.
- The repository or saved project cannot be accessed.
- The replacement task fails for the same reason and no narrower safe task remains.

The stop report must include the base commit, last verified commit, branches and task IDs still relevant, checks completed, unfinished acceptance criteria, and the exact next action needed. Do not call the loop complete.
