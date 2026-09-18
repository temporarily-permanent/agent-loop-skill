# Approval policy

The conductor may approve a repair without asking the user only when every condition below is true.

1. The finding has concrete evidence from the approved contract, an acceptance criterion, a failing test, a compiler or checker result, or a documented repository rule.
2. The repair preserves the approved user-visible behavior and exclusions.
3. The repair adds no dependency, data migration, public API decision, security-policy choice, or new architectural boundary.
4. The repair is narrow, reversible, and owned by the current implementation scope.
5. The smallest compliant repair is clear. Two reasonable repairs with different tradeoffs means the choice is not automatic.
6. The repair does not require destructive Git operations, external writes, new credentials, increased permissions, or a merge.

Do not use a numeric confidence score. Apply the conditions directly and record the evidence in the repair prompt.

## Examples the conductor may approve

- Add a missing error case that the approved acceptance criteria name.
- Fix a regression demonstrated by a focused failing test.
- Restore a documented repository convention when the change has one mechanical form.
- Remove unrequested code introduced by the current candidate when it has no caller and lies outside the approved scope.
- Add a missing test for behavior already implemented and explicitly required.

## Examples that require the user

- Change visible behavior or reinterpret an acceptance criterion.
- Choose between incompatible API shapes or data models.
- Add or replace a dependency.
- Add a migration or change persistent data semantics.
- Broaden the feature to handle a nearby use case.
- Accept a security, privacy, performance, or compatibility tradeoff.
- Resolve disagreement between reviewers when both readings fit the contract.
- Merge, publish, deploy, or send an external message.

## Interview format

Group independent decisions that are ready at the same time. For each decision, provide:

- The decision in user-facing language
- The evidence that made it necessary
- The available outcomes and their concrete consequences
- One recommendation

Do not ask the user for facts that the repository, task history, or available tools can establish.

## Review loop

Every approved repair returns to implementation and produces a new commit. Start two fresh review tasks against that commit. An automatic approval reduces user interruption. It never waives review.

Non-blocking suggestions do not trigger repairs unless the user asks. Record them in the final handoff.
