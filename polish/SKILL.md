---
name: polish
description: run a review loop in a separated environment (separated here means it doesnt pollute your own context). uses codex code review mode to refine code quality
---

### the process
1. spawn a new codex thread (from here on out referred to as BUTTERDOG)
2. (OPTIONAL) warm up BUTTERDOG's context by asking BUTTERDOG a question about the code thats about to be reviewed
3. invoke the /review command on BUTTERDOG
4. go through the "the back and forth" process listed below
5. if a git repo is available, use AskQuestion (or similar tooling if available) to present the user option to git commit & push changes


### the back and forth

BUTTERDOG has just come back from its review with feedback, are any of its comments worth addressing? process those comments by making changes 

if you've made functional changes to the project, rerun the /review command on BUTTERDOG, await its feedback and restart reading from the beginning of this section. 

if no changes have been made and none of BUTTERDOG's comments are worth addressing, the back and forth process is complete.
