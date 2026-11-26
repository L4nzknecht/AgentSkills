---
name: user-story-research
description: "Research a user story from the issue tracker (Azure DevOps, Jira, ...) into an implementation brief, docs/US-<ID>.md. Use when the user asks to research or prepare a story, or a skill needs a story brief."
argument-hint: "Story URL, number, or key"
disable-model-invocation: true
---

# User-story research

Research one story and save its **brief**, the document a later coding session implements from. Three agents run **sequentially**, each a read-only, high-effort subagent receiving the previous reports. The main agent alone writes the brief; code, the tracker, and the knowledge base stay unchanged.

## Configuration

`repo-init` fills this table for a repository; the general copy keeps `<unset>`.

| Setting | Value |
| --- | --- |
| Tracker (Azure DevOps, Jira, ...) | `<unset>` |
| Organization or site | `<unset>` |
| Project | `<unset>` |
| Knowledge base (Azure DevOps wiki, Confluence space, ...; name or URL) | `<unset>` |
| Specifications (repo path and the repo's name for them, e.g. `docs`, PFS) | `docs` |
| Language (questions, brief, chat report) | `English` |

## Questions

Ask every question with `ask_user`, in the configured language, as a **multiple choice**: offer two to four concrete options drawn from the evidence. List the recommended option first, mark it "(recommended)" and preselect it. The free-text field lets the user give their own answer. Group related questions in one form.

## Input

1. Resolve the story from the skill argument or the conversation: a tracker URL (Azure DevOps `workitem=<number>`, Jira `browse/<KEY-123>`), a number, or a Jira key (`123` and `PROJ-123` both resolve to the full key). The resolved ID is `<ID>` throughout.
2. Fill every `<unset>` setting: a URL supplies tracker, organization or site, and project; ask the user for the rest.
3. Resolve the target repository: the git checkout of the codebase the story belongs to (`git rev-parse --show-toplevel`). Ask the user when several repositories could apply.

Done when the story ID, every setting, and the target repository are known. If the story cannot be resolved, report the missing input and stop.

## Agent 1 — story and knowledge base

Give it the tracker settings, story ID, and source URL. For Jira and Confluence it also reads [`tracker-jira.md`](tracker-jira.md), which defines the Telecontext calls, key format, and fetch bounds. Tell it to read the complete story (title, status, assignee, priority, story points, epic, description, acceptance criteria, relations, subtasks, comments, revisions) and search the knowledge base for pages relevant to it.

Require a structured report:

1. Every acceptance criterion, verbatim.
2. Story metadata, links to related stories, and the decisions and open questions found in comments and revisions.
3. Each relevant knowledge-base page: title, URL, last-updated date, the requirement it sets per acceptance criterion.
4. Every unresolved requirement, contradiction, and missing decision, tied to its source or criterion; unavailable sources kept apart from searched sources that lacked information. Contradicting tracker and knowledge-base statements are quoted verbatim side by side, with no winner picked.

Completion criterion: every criterion is verbatim, every metadata field is evidenced or marked unavailable, every requirement the sources do not state directly is marked `Unverified`, and the knowledge-base search has an explicit result (`none found` when empty).

## Clarification gate — main agent

Judge whether the story and knowledge base define the behavior well enough to judge every acceptance criterion. Resolve unclear scope, business rules, terminology, boundary cases, contradictions, and missing criteria with the user **before Agent 2**; a missing knowledge base alone needs no questions when the story is sufficient.

For each question, name the affected criterion and the missing decision. Repeat until no ambiguity remains.

Keep a **decision record** (question, answer, affected requirement), separate from the original story wording, and pass it to every later agent.

Completion criterion: every ambiguity has a user answer or a resolving source. When the user cancels or defers a required answer, or a source failure blocks understanding the story, report the blocker and pause until it is resolved.

## Agent 2 — repository specifications

Give it the Agent 1 report, the decision record, the Specifications setting, and the repository. Tell it to find every specification in the configured location relevant to the story (functional specs, requirement docs, ADRs, under the repo's own name for them) and extract what each requires.

Require a structured report: each source with its repo-relative path, the requirement or decision it contributes, and every conflict with the story.

Completion criterion: every relevant specification has a path and an extracted requirement, conflicts are explicit, and the search has an explicit result (`none found` when empty).

## Agent 3 — codebase evidence

When Agent 2 surfaced new ambiguities, run the clarification gate again first.

Give it both reports, the decision record, and the repository. Tell it to locate the implementation covering the story across every relevant layer: endpoints and routes, services and business logic, schemas and models, migrations, tests, configuration, integrations and callers.

For **every acceptance criterion**, it assigns a **status**:

| Status | Meaning |
| --- | --- |
| `met` | fully implemented |
| `partial` | implemented in part |
| `not met` | absent |
| `unverifiable` | cannot be judged from the code |

Each status carries **evidence**: `file:line` read from the code itself, or `missing`. Missing behavior names the files that would change, or the reason none can be named.

Completion criterion: every criterion has a status and evidence, and every gap has candidate files or a reason.

## Main-agent synthesis

Reconcile the reports. Prefer tracker wording for criteria, code evidence for status, and the most specific source when specifications conflict. Then write the brief and the chat report as [`brief-format.md`](brief-format.md) defines them.
