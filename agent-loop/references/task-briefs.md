# Task briefs

Every worker starts from a complete prompt. Do not refer to facts that exist only in the conductor's conversation.

## Common header

Include this information in every task prompt:

```markdown
You are a worker in an agent-loop run. The conductor owns user communication and product decisions.

Role: <specification | triage | implementation | integration | spec review | standards review | repair>
Repository: <saved Codex project name>
Repository root: <path known to the project>
Base commit: <full SHA>
Candidate commit: <full SHA, when applicable>

Do not broaden scope. Do not ask the user questions in this task. If a product decision is missing, report it as a blocker with the smallest decision statement you can write.
```

Use stable behavior, interfaces, types, commands, and domain terms. Avoid file paths and line numbers in the contract unless a path itself is part of the public behavior. Workers may cite exact files and lines in evidence after inspecting the current checkout.

## Specification task

Give the task the settled interview, requested outcome, constraints, and known exclusions. Ask it to inspect the repository without editing it and return:

```markdown
## Current behavior
## Desired behavior
## User-visible scenarios
## Stable interfaces and domain concepts
## Acceptance criteria
## Test seams and existing prior art
## Out of scope
## Contradictions or missing decisions
```

Every acceptance criterion must name an observation that would fail before the change and pass afterward. The task must not invent a decision to fill a section.

## Triage task

Include the full issue, pull request, ticket, or spec content. Ask the task to inspect the repository without editing it and return:

```markdown
## Verified current behavior
## Claims confirmed by the repository
## Claims contradicted by the repository
## Missing facts found during inspection
## Unresolved product decisions
## Proposed behavioral contract
```

For a pull request, current behavior means the state of the supplied diff. The task must distinguish work already present from work still required.

## Implementation task

Include the approved contract or the complete assigned vertical slice. State the allowed branch or worktree, base commit, blockers already completed, and checks expected by the repository.

Require the task to:

- Inspect repository instructions before editing.
- Implement only the approved contract or slice.
- Test at agreed behavioral seams.
- Run focused checks while working and the full required checks before finishing.
- Commit the completed work.
- Leave unrelated user changes untouched.

Required final response:

```markdown
## Result
Completed | Blocked

## Commit
<full commit SHA or "none">

## Acceptance evidence
- <criterion>: <test, command, or direct observation>

## Checks
- <command>: <pass, fail, or not run with reason>

## Scope notes
- <unexpected constraint, deviation, or "none">

## Blocker
<one decision or external dependency, or "none">
```

## Integration task

Include the approved base commit, ordered slice commits, full contract, and verification commands. Ask the task to combine the commits into a dedicated integration worktree. It may resolve mechanical conflicts. It must stop and report conflicts that require product or architecture decisions.

Require the same final response as an implementation task, plus an ordered list mapping every slice commit to its integrated result.

## Spec review task

Make this task read-only. Include the fixed base commit, candidate commit, full approved contract, and acceptance evidence from implementation.

Ask it to inspect the complete diff and report:

```markdown
## Blocking findings
- [severity] Requirement: <quoted or exact requirement>
  Evidence: <file, line, test, or command>
  Problem: <missing, partial, incorrect, or unrequested behavior>
  Smallest compliant repair: <behavioral change, not a full redesign>

## Non-blocking suggestions
## Acceptance criteria checked
## Verdict
Pass | Fail
```

It must check missing behavior, partial behavior, incorrect behavior, scope creep, error cases, and acceptance criteria. No finding may rely only on preference.

## Standards review task

Make this task read-only. Include the fixed base commit, candidate commit, repository instruction sources, and checks already run. Do not include the spec review report.

Ask it to inspect the complete diff and report:

```markdown
## Blocking findings
- [severity] Rule or risk: <repository rule or named maintainability risk>
  Evidence: <file, line, test, or command>
  Problem: <concrete consequence>
  Smallest repair: <specific change>

## Non-blocking suggestions
## Repository rules checked
## Verdict
Pass | Fail
```

Repository rules override general style preferences. The reviewer must separate documented violations from judgment calls and skip issues already enforced by passing tooling unless the tool output shows a failure.

## Repair task or follow-up

Include only approved findings, the current candidate commit, and the unchanged contract. State which findings the conductor auto-approved and which the user approved. Require a new commit and the implementation final-response format.

The worker may challenge a finding with concrete evidence. It may not silently ignore one.
