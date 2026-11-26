---
name: user-story-review
description: "Review a user story's implementation against its brief, docs/US-<ID>.md, including bloat and duplication, and draft a copy-ready tracker status comment. Use when the user asks to review a story's implementation or for its implementation status comment, or a skill needs the brief's Review section."
argument-hint: "Story ID, or path to docs/US-<ID>.md"
---

# User-story review

Review the implementation of one story against the **brief** `user-story-research` saved in `docs\US-<ID>.md`. Two agents run **sequentially**, each a read-only, high-effort subagent. The main agent alone writes the review, as the brief's `## Review` section; code, the tracker, and the rest of the brief stay unchanged.

## Input

1. Resolve the story ID from the skill argument or the conversation and read the brief. If it is missing, tell the user to run `user-story-research` first and stop.
2. Take the language from the brief's `Language:` line (English when absent).
3. Determine the **scope**: the changes since the branch point from the default branch, uncommitted changes, and every file the brief names.

Done when the scope is a concrete file list. When no changes are found or the base branch is ambiguous, ask the user with the candidate branches or scopes as a **multiple choice**, as the `## Questions` section of [`../user-story-research/SKILL.md`](../user-story-research/SKILL.md) defines.

## Agent 1 — conformance

Give it the brief, the scope, and the repository. Tell it to give **every acceptance criterion** a status (`met`, `partial`, `not met`, `unverifiable`, as in the brief) and evidence read from the code itself, and to check against the implementation:

- each change the brief lists;
- each verification case, including whether a test exists and covers it;
- each entry of the decision record;
- **scope creep**: behavior in the changes that no criterion or decision asks for.

Completion criterion: every criterion, listed change, verification case, and decision has a status with `file:line` or `missing`, and scope creep has an explicit result.

## Agent 2 — simplicity

Give it the scope, the conformance report, and the repository. Tell it to run the `ponytail-review` skill over the scope and report each finding as `bloat`, `duplication`, or `simplification`, with `file:line` and the replacement. A finding that would lower a criterion below `met` is marked as a trade-off.

Completion criterion: every file in the scope has findings or an explicit `no findings`.

## Main-agent synthesis

Rank the findings: unmet criteria (`gap`), missing tests (`test`), scope creep (`scope`), then `bloat`, `duplication`, `simplification`.

### Save the review

Write the `## Review` section at the end of the brief in the brief's language, replacing an earlier `## Review` section while keeping its manually recorded decisions. It holds the reviewed branch/commit (marking uncommitted changes), the criteria table, the ranked findings with proposed fixes, and the open questions.

Completion criterion: the section has been read back and accounts for every criterion and every finding. If saving fails, report the failure.

### Chat report

Return exactly these sections in this order, in the brief's language (headings and fixed phrases translated), starting on the first line:

**US: Title** · Verdict (`ready`, `needs changes`, or `blocked`)

**Criteria:**

| Criterion | Status | Evidence |
| --- | --- | --- |
| Exact tracker wording | status | `file:line` or `missing` |

**Findings:** Ranked, one line each: type, `file:line`, proposed fix. When there are none, write exactly: `None`.

**Next step:** Apply the fixes from the `## Review` section of `docs\US-<ID>.md` (named only after a successful read-back).

### Tracker comment

When the user asks for a status comment, end the chat report with one copy-ready comment in a single plain-text code block, in the brief's language. It holds one list item per acceptance criterion, in the tracker's wording:

- No symbol: `met`.
- `⚠️`: `partial`, dependent, or to be added later.
- `❌`: `not met` or currently not implementable.
- Every `⚠️` and `❌` item names the concrete open point.

```text
*Implementation status:*
- *<criterion>:* <concrete status>
- *⚠️ <criterion>:* <what is done, what remains>
- *❌ <criterion>:* <what is missing, e.g. the missing endpoint>
```

The comment stays in the chat; the tracker is read-only.
