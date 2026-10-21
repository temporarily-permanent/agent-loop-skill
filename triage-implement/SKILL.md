---
name: triage-implement
description: break up a request into clearly defined chunks of work, orchestrate execution of the chunks of work afterwards. 
---

break up the user request into tickets. Use [this template](./resources/AGENT-BRIEF.md) as a reference at ticket creation, ignore the fact that it references these terms (PR, issue, "ready-for-agent") and instead focus on the principles of the brief.
for each ticket, create an agent brief, spawn thread on separate worktree.
parallelize tickets when possible, use subagents when thread spawning is not available.