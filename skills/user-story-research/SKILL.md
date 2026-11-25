---
name: user-story-research
description: Research a user story from the issue tracker (Azure DevOps, Jira, ...) and save an implementation brief under docs.
argument-hint: "Work-item URL or story key/number"
disable-model-invocation: true
---

# User-story research

Run three read-only subagents **sequentially**, passing each completed report to the next. The main agent alone writes the implementation brief; application code, tickets, and knowledge-base pages stay unchanged.

## Input

Resolve the user-story reference from the skill argument or the current conversation. Accept either:

- a tracker URL to a work item or board card (Azure DevOps `workitem=<number>`, Jira `browse/<KEY-123>`), or
- `USER STORY <number>` / a bare ID / a Jira key.

Take tracker, organization or site, project, and knowledge base from the Configuration table below. Done when all have values: a URL input fills tracker, organization or site, and project; ask the user (`ask_user`) for any that remain `<unset>`. Preserve the story ID resolved from the input. If the reference cannot be resolved, stop and report the missing input.

## Configuration

`repo-init` fills this table for a repository; the general copy keeps `<unset>`.

| Setting | Value |
| --- | --- |
| Tracker (Azure DevOps, Jira, ...) | `<unset>` |
| Organization or site | `<unset>` |
| Project | `<unset>` |
| Knowledge base (Azure DevOps wiki, Confluence space, ...; name or URL) | `<unset>` |
| Language (chat report, questions, brief) | `English` |

Resolve the target repository from the current coding workspace and verify its relationship to the ticket. If no writable repository workspace is available or several repositories could apply, obtain the workspace or clarification before proceeding. The chat scratch directory is not a target repository.

## Agent 1 — work item and knowledge base

Dispatch one read-only, high-effort subagent.

Give it:

- the resolved tracker, organization or site, project, story ID, and source URL;
- the instruction to read the complete work item, including title, status, assignee, priority, story points, epic, description, acceptance criteria, relations, comments, and revisions when available;
- the instruction to search the configured knowledge base for pages relevant to this user story.

Require a structured report containing:

1. Exact acceptance-criteria wording from the tracker.
2. Work-item metadata and links to related tickets.
3. Each relevant knowledge-base page with its title, URL/path, and the requirement it establishes.
4. An explicit `none found` result when no relevant knowledge-base entry exists.
5. All unresolved story requirements, contradictions, and missing decisions, each tied to its source or affected acceptance criterion. Separate unavailable sources from sources that were successfully searched but contain insufficient information.

Completion criterion: every acceptance criterion is quoted verbatim, every metadata field requested above is either evidenced or marked unavailable, and the knowledge-base search has an explicit result.

## Clarification gate — main agent

After Agent 1 returns, check whether the story and knowledge base define the intended behavior sufficiently to judge every acceptance criterion. Resolve unclear scope, business rules, terminology, boundary cases, contradictory requirements, and missing acceptance criteria with the user **before dispatching Agent 2 or Agent 3**. A missing knowledge base alone does not require questions when the story is sufficient.

Use `ask_user` in the configured Language for every required question. State the affected criterion and the concrete missing decision; group related questions in a focused form. Present alternatives as proposals, not established requirements. Use further forms when answers expose additional ambiguities, until every identified story ambiguity is resolved.

Keep a decision record with each question, the user's answer, and the affected requirement. Preserve the original tracker acceptance-criteria wording; record user clarifications separately. Pass this record alongside the research reports to both remaining agents.

Completion criterion: every identified story ambiguity has an explicit user answer or a supporting source that resolves it. If the user cancels, declines a required answer, or defers a decision, pause the pipeline and report the blocker; resume only once it is resolved. Source-access failures also remain explicit blockers when they prevent understanding the story. When the evidence is already sufficient, pass the gate without asking redundant questions.

## Agent 2 — repository documentation and PFS

After the clarification gate passes, dispatch a second read-only, high-effort subagent.

Give it the work-item report and the repository workspace. Tell it to inspect the repository's `docs` folder and identify the repository's meaning of **PFS** from filenames, configuration, or existing documentation before searching. Then inspect every relevant document and PFS entry for requirements related to the user story.

Require a structured report containing:

1. Relevant documents and PFS entries, each with a repository-relative path.
2. The exact requirement or decision each source contributes.
3. Any conflict between the repository material and the tracker story.
4. An explicit `none found` result when no relevant document or PFS entry exists.

Completion criterion: all relevant `docs` and PFS sources have been checked, each reported source has a path and extracted requirement, and conflicts are stated explicitly.

## Agent 3 — codebase evidence

After Agent 2 completes, dispatch a third read-only, high-effort subagent. If Agent 2 uncovered new story ambiguities or contradictions with the confirmed decisions, first resolve them through the clarification gate and update the decision record.

Give it both previous reports and the repository workspace. Tell it to locate the implementation covering the ticket and inspect all relevant layers:

- endpoints and routes;
- services and business logic;
- schemas and models;
- migrations;
- tests;
- configuration;
- related integrations and callers.

For **every acceptance criterion**, judge the implementation as `met`, `partial`, `not met`, or `unverifiable`. Cite concrete evidence as `file:line`, read from the code itself rather than inferred from filenames. For missing or incomplete behavior, name the concrete files that would need changes.

Completion criterion: every acceptance criterion has a status, at least one precise evidence citation or `missing`, and missing behavior has concrete candidate files or an explicit reason no file can be identified.

## Main-agent synthesis

After all three agents finish, reconcile their reports. Prefer direct tracker wording for acceptance criteria, repository evidence for implementation status, and the most specific source when documents conflict. Keep uncertainty explicit.

### Save the implementation brief

Write a Markdown document in the configured Language to `docs\US-<ID>.md` relative to the target repository root. Create `docs` if needed. Read an existing document at that path first; refresh the research while preserving manually recorded decisions and unresolved questions. The generated brief is research output; on later runs the original sources stay the specification.

The document must let a subsequent coding session implement the story without reconstructing the research. Include:

- Work-item link, metadata, revision, research date, and inspected repository branch/commit; distinguish uncommitted changes when present.
- Goal, scope, and every acceptance criterion in its exact tracker wording, with implementation status and `file:line` evidence or an explicit evidence gap.
- Relevant knowledge-base, docs, and PFS sources with links/paths, their available version/date, and applicable requirements. Distinguish binding requirements from proposals and unresolved source conflicts.
- Concrete changes needed for each incomplete criterion: existing or proposed file paths, intended behavior, affected endpoints/services/schema/migrations/configuration, and actual dependencies between changes.
- Focused verification cases mapped to the criteria, including relevant edge cases and repository-standard commands identified from the project. Label proposed checks as not executed.
- Open decisions, genuine contradictions, related-ticket dependencies, and blockers. Keep unresolved behavior explicit.
- The clarification decision record, clearly attributed to the user and separate from the original story and knowledge-base text.

When nothing needs changing, document the supporting evidence.

Completion criterion: the file exists in the target repository, has been read back, accounts for every acceptance criterion, and names concrete changes and verification cases for every identified gap or explains the blocker. If saving fails, report the failure.

### Chat report

Return the completed research report in the configured **Language** as exactly these sections in this order, starting directly with the first line, with headings and fixed phrases translated. Intermediate `ask_user` forms and blocker messages follow their own format.

**US: Title** · Status · Assignee · Priority · Story points · Epic

**Goal:** One to two sentences.

**Implementation status:** One row per acceptance criterion.

| Criterion | Status | Evidence |
| --- | --- | --- |
| Exact tracker wording | what is the case | `file:line` or `missing` |

**Specification:** One line per relevant knowledge-base page, document, or PFS source with title, version/date, and binding requirement. When no source is relevant, write exactly: `No relevant specification found`.

**Conflicts:** Genuine contradictions, missing decisions, ambiguities, and related tickets. When there are none, write exactly: `None`.

**Next step:** `grilling` skill based on `docs\US-<ID>.md` (the document is referenced only after a successful read-back).