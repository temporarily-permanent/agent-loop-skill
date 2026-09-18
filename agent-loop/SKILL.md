---
name: agent-loop
description: Run an explicit, interview-led engineering loop that uses separate Codex tasks for specification, implementation, integration, and independent review. Use only when the user invokes $agent-loop or directly asks to run the agent loop.
disable-model-invocation: true
---

# Agent loop

Act as the conductor in the current Codex task. Take work from an idea or an existing artifact to a reviewed branch. Create separate Codex tasks for all specification, triage, implementation, integration, and review work. Never use collaboration sub-agents.

The user's instructions take precedence over this skill. Do not expand the requested outcome or authority.

## Invocation contract

Run this workflow only after explicit invocation. Invocation authorizes you to create and wait on Codex tasks needed for the requested work. It does not authorize merging, publishing, destructive Git operations, external messages, or unrelated changes.

The current task remains the conductor for the whole run. It owns user interviews, task creation, task waiting, decision records in conversation context, and the final handoff. Worker tasks do not make product decisions.

Do not write briefs, specifications, task state, or review reports into the repository. Put each brief in the worker task's opening prompt. Git holds implementation history. Task history holds the briefs and reports. The only optional process-document change is an approved update to `AGENTS.md` at the end.

## Required capabilities

Use the Codex task-management tools for this workflow:

- List projects to resolve the current saved Git project.
- Create tasks in Codex worktrees.
- Wait for tasks and preserve their wait cursors.
- Read a task when its final result lacks required evidence.
- Send a follow-up to an existing task for repairs or missing evidence.

Never call `spawn_agent` or any other collaboration-agent tool. These workers must be separate, user-visible Codex tasks.

Before dispatching work, confirm that the current repository is a saved Codex project and that it is a Git repository. If either condition fails, explain what the user must configure and stop before creating workers.

## Load the operating rules

Read these references before dispatching the first worker:

1. [state-machine.md](references/state-machine.md)
2. [task-briefs.md](references/task-briefs.md)
3. [approval-policy.md](references/approval-policy.md)
4. [recovery.md](references/recovery.md)

## Core rules

1. Start from the user's actual checkout state. Record the base commit, current branch, and whether relevant uncommitted changes exist. Never clean, reset, or overwrite the user's checkout.
2. Use a dedicated worktree and branch for implementation. Use fresh worktrees for reviewers.
3. Interview the user only about decisions. Send factual research to a worker or inspect the repository yourself.
4. Give every worker a complete, behavioral brief. Do not depend on hidden conductor context.
5. Keep work small enough for one fresh task. Split large work into independently verifiable vertical slices with explicit blockers.
6. Run unblocked slices concurrently only when their expected code ownership does not overlap. Sequence shared infrastructure, migrations, and cross-cutting edits.
7. Require commits from implementation and integration workers. Require exact commit SHAs and verification evidence in their final responses.
8. Review every integrated implementation with two fresh tasks. One reviews spec compliance. The other reviews repository standards and maintainability.
9. Auto-approve repairs only under [approval-policy.md](references/approval-policy.md). Send all other decisions to the user with a recommendation.
10. Run fresh review tasks after every repair. Never ask a previous reviewer to validate its own findings.
11. Do not merge before explicit approval. Stop at a reviewed, ready-to-merge branch and ask the user to approve the merge or handoff.

## Intake

Classify the input without asking the user to repeat information already available.

- A rough idea enters discovery. Interview the user about unresolved behavior, constraints, and scope. Then create a fresh specification task to inspect the repository and draft the contract.
- An issue, pull request, written spec, or detailed ticket enters triage. Create a fresh triage task to verify the current repository state, identify contradictions, and list only the missing decisions.

Both paths must end with one approved behavioral contract containing current behavior, desired behavior, acceptance criteria, interfaces or domain concepts that must remain stable, and explicit exclusions.

When interviewing, ask the full set of independent decisions whose prerequisites are settled. Give a recommendation and the consequence of each recommendation. Ask later questions only after their prerequisites have answers. Present the completed contract and wait for approval before implementation planning.

## Implementation planning

Choose the smallest plan that fits the work.

- If the work fits one fresh context window, create one implementation task.
- Otherwise, propose vertical slices. Each slice must deliver testable behavior across every affected layer, fit one fresh task, and name its blockers.
- Show the proposed slices and blocking edges to the user. Dispatch only after approval.

For parallel slices, start each task from the same approved base commit. After they finish, create a dedicated integration task from that base. Give it the ordered commit SHAs to combine. The integration task resolves conflicts, runs the full verification set, and returns one integrated commit. For sequential slices, start each later task from the last verified commit.

## Review and repair

Start two fresh review tasks from the same integrated commit and fixed base commit.

- The spec reviewer checks the approved contract, acceptance criteria, missing or partial behavior, incorrect behavior, and scope creep.
- The standards reviewer checks repository instructions, required tests, maintainability, and code-quality risks. It distinguishes repository-rule violations from judgment calls.

Keep the two reports separate when presenting findings. Classify each finding as blocking or non-blocking. Non-blocking suggestions do not keep the loop open.

For blocking findings, apply the approval policy. Send approved repairs to the implementation task that owns the affected code. If several tasks own the affected code or the original task cannot continue, use a dedicated repair task from the integrated commit. Require a new commit, then run two new review tasks.

## Completion

Declare the branch ready only when all of the following are true:

- Every approved acceptance criterion has evidence.
- Tests, type checks, and repository-required checks pass.
- Both fresh review tasks report no blocking findings.
- Every auto-approved repair has passed fresh review.
- The implementation branch is ready to merge without touching the user's checkout.

Summarize non-blocking suggestions separately.

Before handoff, consider whether the work revealed one reusable repository instruction. If so, propose a concise `AGENTS.md` change and explain why it applies to future work. Edit the file only after explicit user approval. Never put run history, task IDs, feature-specific notes, or review logs in `AGENTS.md`.

End by naming the ready branch, base commit, final commit, checks run, review result, and any non-blocking suggestions. Ask the user to approve the merge or choose a different handoff. Do not merge on their behalf without that approval.
