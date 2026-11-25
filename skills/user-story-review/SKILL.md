---
name: user-story-review
description: Review the whole implementation of a user story against its research brief, and check it for bloat, duplication, and simplifications.
argument-hint: "Story ID, or path to docs/US-<ID>.md"
disable-model-invocation: true
---

# User-story review

Review the implementation of one user story against the brief that `user-story-research` saved. Run two read-only subagents **sequentially**; the main agent alone writes the review. Application code, tickets, and the brief stay unchanged.

## Input

Resolve the story ID from the skill argument or the current conversation, then read `docs\US-<ID>.md` in the target repository. If the brief is missing, tell the user to run `user-story-research` first and stop.

Take the output language from the brief; it is the Language the research used.

Determine the implementation scope: the changes since the branch point from the default branch plus uncommitted changes, and every file the brief names. Done when the scope is a concrete list of changed and brief-named files; ask the user (`ask_user`) when no changes can be found or the base is ambiguous.

## Agent 1 — conformance

Dispatch one read-only, high-effort subagent.

Give it the brief, the scope list, and the repository workspace. Tell it to judge **every acceptance criterion** in the brief as `met`, `partial`, `not met`, or `unverifiable`, citing `file:line` read from the code itself, and to check each of these against the implementation:

- each concrete change the brief lists;
- each verification case, including whether a test exists and covers it;
- each decision in the clarification record;
- behavior in the changes that no criterion or decision asks for (scope creep).

Require a structured report with one row per criterion, one row per listed change and verification case, and the scope-creep findings.

Completion criterion: every criterion, listed change, verification case, and recorded decision has a status with a `file:line` citation or `missing`.

## Agent 2 — simplicity

After Agent 1 completes, dispatch a second read-only, high-effort subagent.

Give it the scope list, the conformance report, and the repository workspace. Tell it to read the changed code together with its callers and neighbours, and to find:

- **Bloat**: code, abstraction, configuration, or dependency the story does not need;
- **Duplication**: logic repeated within the change, or already present elsewhere in the repository and reusable;
- **Simplifications**: a smaller, more direct solution that keeps every criterion `met`, stated concretely.

Require each finding with `file:line`, why it is excess, and the replacement. A finding that would break a criterion is marked as a trade-off.

Completion criterion: every changed file is covered, and each has either findings or an explicit `no findings`.

## Main-agent synthesis

Reconcile both reports. Rank findings by impact: unmet criteria first, then missing tests, then scope creep, then bloat, duplication, and simplifications. Keep uncertainty explicit.

### Save the review

Write a Markdown document in the configured language to `docs\US-<ID>-review.md`. Read an existing document at that path first and refresh it, preserving manually recorded decisions. Include the story link from the brief, the reviewed branch/commit (marking uncommitted changes), the criteria table, the ranked findings with proposed fixes, and the open questions.

Completion criterion: the file exists, has been read back, and accounts for every criterion and every finding. If saving fails, report the failure.

### Chat report

Return the report in the configured language as exactly these sections in this order, starting directly with the first line, with headings and fixed phrases translated:

**US: Title** · Verdict (`ready`, `needs changes`, or `blocked`)

**Criteria:** One row per acceptance criterion.

| Criterion | Status | Evidence |
| --- | --- | --- |
| Exact tracker wording | `met`, `partial`, `not met`, or `unverifiable` | `file:line` or `missing` |

**Findings:** Ranked, one line each: type (`gap`, `test`, `scope`, `bloat`, `duplication`, `simplification`), `file:line`, proposed fix. When there are none, write exactly: `None`.

**Next step:** Apply the fixes from `docs\US-<ID>-review.md` (referenced only after a successful read-back).
