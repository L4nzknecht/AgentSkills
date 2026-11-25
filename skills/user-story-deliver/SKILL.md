---
name: user-story-deliver
description: "Deliver a user story end to end: research, grill, plan tasks, implement with subagents, review, and fix."
argument-hint: "Story URL, number, or key"
disable-model-invocation: true
---

# User-story deliver

Take one story from the tracker to a reviewed implementation. The **brief** `docs\US-<ID>.md` is the one document every step reads and updates. Ask every question in the brief's language as a **multiple choice**, as the `## Questions` section of [`../user-story-research/SKILL.md`](../user-story-research/SKILL.md) defines.

- **Research skill**: `user-story-research-<repo>`, the repository's configured copy. When the repository has none, read [`../user-story-research/SKILL.md`](../user-story-research/SKILL.md) and follow it.
- **Review skill**: `user-story-review`.

The main agent holds the user conversation and the brief; **subagents** do the code work in steps 7 and 9. Every subagent, including those the research and review skills dispatch, runs **sequentially**: each starts after the previous one reported, since they share one working tree and each builds on the last.

## Steps

1. **Research**: run the research skill for the story in the argument. Done when the brief is saved and read back.
2. **Report**: show the research skill's chat report, with its **Next step** line replaced by `Grilling follows`. Done when the report is shown.
3. **Grill**: run the `grilling` skill on the brief's conflicts, open decisions, and every criterion short of `met`. Record each answer in the brief's decision record and update the sections it affects (scope, changes, verification cases). Done when the brief has no open decision left and has been read back.
4. **Summary**: present at most ten lines in the brief's language: goal, scope, key decisions, the change per criterion, risks. Ask whether changes are needed, with "No changes, continue (recommended)" as the first option. Done when the user answers.
5. **Revise**: when the user asks for changes, apply them to the brief. Go back to step 3 when a change opens a new decision, otherwise to step 4. Done when the user chose to continue.
6. **Tasks**: add a `## Tasks` section to the brief, replacing an earlier one. Each task has an ID, a goal, the files it touches, the tasks it depends on, and acceptance criteria a test or a `file:line` can check. Done when every criterion short of `met` and every verification case is covered by a task.
7. **Implement**: for each task in dependency order, dispatch a `general-purpose` subagent with the brief path, the task, and the test commands from `AGENTS.md`. It implements the task, adds or updates its tests, runs the smallest covering tests, and reports each task criterion with `file:line` and the test result. Mark the task `done` in the brief when every criterion is met and its tests pass; otherwise dispatch it again with the failure. Done when every task is `done`; a task failing twice goes to the user as a question.
8. **Review**: run the review skill, and show its chat report with the **Next step** line replaced by `Fixes follow`. Done when the brief's `## Review` section is read back.
9. **Fix**: for each finding from `## Review` that is not a trade-off, in its ranked order, dispatch a `general-purpose` subagent with the brief path, the finding, and the test commands. It applies the fix with the `ponytail` skill, runs the affected tests, and reports the result with each criterion whose status the fix changed, with `file:line`. Mark each finding fixed in `## Review` and update its criteria table. Done when every finding is fixed or recorded in `## Review` as a trade-off, the criteria table matches the subagent reports, and the tests pass.
10. **Present**: show `git diff --stat`, the criteria table from `## Review`, the tasks, and the remaining trade-offs. Commit only when asked.
