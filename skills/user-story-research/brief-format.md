# Brief and chat report

## The brief

Write the brief in the configured language to `docs\US-<ID>.md` in the target repository. When the file exists, read it first and refresh the research, preserving the manually recorded decisions, the open questions, the `## Tasks` section written by `user-story-deliver`, and the `## Review` section written by `user-story-review`. On later runs the original sources stay the specification; the brief is research output.

The brief lets a coding session implement the story without redoing the research. It holds:

- A `Language: <configured language>` line, then the story link, metadata, revision, research date, and the inspected branch/commit (marking uncommitted changes).
- Goal, scope, and every acceptance criterion in its exact tracker wording with its status and evidence.
- Relevant knowledge-base pages and specifications with links/paths, version/date, and requirements; binding requirements kept apart from proposals and unresolved conflicts.
- For each criterion short of `met`: the files to change or create, the intended behavior, affected endpoints, services, schema, migrations, and configuration, and the dependencies between changes. When everything is `met`, the supporting evidence instead.
- Verification cases mapped to the criteria, with edge cases and the repository's test commands, labelled as not yet executed.
- Open decisions, contradictions, related-story dependencies, and blockers.
- The decision record, attributed to the user.

Completion criterion: the file exists, has been read back, accounts for every criterion, and gives every gap concrete changes and verification cases or names its blocker. If saving fails, report the failure.

## The chat report

Return exactly these sections in this order, in the configured language (headings and fixed phrases translated), starting on the first line. `ask_user` forms and blocker messages keep their own format.

**US: Title** · Status · Assignee · Priority · Story points · Epic

**Goal:** One to two sentences.

**Implementation status:**

| Criterion | Status | Evidence |
| --- | --- | --- |
| Exact tracker wording | status | `file:line` or `missing` |

**Specification:** One line per relevant knowledge-base page or specification: title, version/date, binding requirement. When none is relevant, write exactly: `No relevant specification found`.

**Conflicts:** Contradictions, missing decisions, ambiguities, and related stories. When there are none, write exactly: `None`.

**Next step:** `/grill-me` on `docs\US-<ID>.md`, then implement, then `/user-story-review` (the brief is named only after a successful read-back).
