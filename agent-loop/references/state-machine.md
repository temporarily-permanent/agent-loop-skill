# State machine

The conductor tracks one current state. Do not skip states because a worker volunteered work from a later state.

## States

### `intake`

Capture the requested outcome, current project, repository root, branch, base commit, working-tree state, and supplied issue, pull request, ticket, or spec references.

Allowed transitions:

- Rough idea to `discovery`
- Existing artifact to `triage_pending`

### `discovery`

Build a decision tree for unresolved product behavior, constraints, and scope. Ask the user only about decisions. Inspect the repository or create a read-only research task for facts.

Transition to `spec_pending` when the current decision frontier is settled.

### `triage_pending`

Create one read-only triage task. It verifies the artifact against the repository and returns contradictions, missing facts, and unresolved decisions. Do not let it edit code or rewrite the user's artifact.

- If decisions remain, move to `discovery` with only those decisions.
- If the artifact already supplies a complete behavioral contract, move to `spec_approval`.

### `spec_pending`

Create one read-only specification task. It inspects the repository and drafts the behavioral contract from the settled interview. It may report a contradiction but may not resolve one by assumption.

Move to `spec_approval` when the draft is complete.

### `spec_approval`

Present the contract in plain language. Call out new assumptions, test seams, and exclusions. Wait for explicit approval. Revisions return to `discovery` or `spec_pending`, depending on whether they require a decision or only a rewrite.

Move to `implementation_planning` after approval.

### `implementation_planning`

Decide whether the work fits one fresh task. If not, draft vertical slices and their blockers. Ask for approval of the split. Do not create implementation tasks before the user approves the plan.

Move to `implementing` after approval.

### `implementing`

Create implementation tasks in dedicated worktrees. Work the dependency frontier. Run independent slices concurrently only when expected ownership does not overlap. Require each task to commit its work and return the commit SHA plus evidence.

- One task or a sequential chain moves to `reviewing` after the final verified commit.
- Parallel tasks move to `integrating` after all frontier tasks finish.
- A failure moves to `recovering`.

### `integrating`

Create a dedicated integration task from the approved base. Give it the ordered slice commits and the full contract. It combines the commits, resolves conflicts without changing approved behavior, runs all required checks, and returns one integrated commit.

- Success moves to `reviewing`.
- A product or architecture conflict moves to `decision_required`.
- An execution failure moves to `recovering`.

### `reviewing`

Create two fresh, read-only review tasks from the same fixed base and candidate commit. Wait for both. Keep their reports separate and classify findings.

- No blocking findings move to `ready_for_handoff`.
- Blocking findings covered by the automatic approval policy move to `repairing`.
- Findings that require a decision move to `decision_required`.

### `decision_required`

Present the smallest set of independent decisions, the evidence behind each, and a recommendation. Wait for the user. Record the decision in the repair brief or revised contract.

- Approved repairs move to `repairing`.
- Contract changes that alter planned behavior move to `spec_approval` and require an updated implementation plan.
- A user stop request moves to `stopped`.

### `repairing`

Send the approved repair set to the implementation task that owns the affected code. Use a fresh repair task when ownership spans tasks or the original task cannot continue. Require a new commit and verification evidence.

Move to `reviewing` with two new reviewers. Never reuse the prior review tasks.

### `recovering`

Follow [recovery.md](recovery.md). Recovery returns to the state that failed, moves to `decision_required`, or ends at `stopped`.

### `ready_for_handoff`

Confirm the completion conditions from `SKILL.md`. Consider an optional `AGENTS.md` proposal. Report the branch, commits, checks, reviews, and non-blocking suggestions. Ask for merge or handoff approval.

- User approval permits the specifically approved handoff action.
- A new blocking concern returns to `reviewing` or `decision_required`.
- No approval means stop with the branch ready and unchanged.

### `complete`

The approved handoff finished. Do not start adjacent work automatically.

### `stopped`

Report the last verified commit, active tasks, unfinished work, and safe resume point. Do not label an incomplete run as complete.

## Waiting rules

After creating a task, wait for progress. Preserve the returned cursor and pass it to later waits so completed text is not replayed. Prefer bounded long waits over frequent polling. Do not narrate unchanged snapshots.

If a task requests approval or a product decision, leave the request for the user unless the approval policy already authorizes the exact choice. Factual questions go through the recovery rules.
