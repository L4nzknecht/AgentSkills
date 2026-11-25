---
name: repo-init
description: Adapt my general skills to the current repository by writing the repo-specific context they rely on (AGENTS.md, build/test commands, conventions, glossary, docs layout). Use for "repo-init", "set up this repo for my skills".
---

# repo-init

Make the general skills (Matt Pocock, Ponytail) work well in *this* repo by recording repo facts once, in files the skills already read.

## Steps

1. **Inspect** the repo: languages, package manager, build/test/lint commands, folder layout, existing `AGENTS.md`/`CLAUDE.md`/`CONTRIBUTING.md`/`GLOSSARY.md`/`docs/adr`, CI config, issue tracker (GitHub, Azure DevOps, ...). Only record what you verified.
2. **Check installed skills**: if the plugins from the `init` skill are missing, tell the user to run `init` first.
3. **Run `setup-matt-pocock-skills`** if available, to configure issue tracker, triage labels and docs location. Skip questions the repo already answers.
4. **Write/extend `AGENTS.md`** (create if missing; never overwrite existing content, append or edit sections). Include:
   - exact build, test, lint commands (smallest-test command too)
   - repo conventions and layout that are not obvious from the code
   - where specs, ADRs and the glossary live
   - preferences: e.g. "prefer the smallest change (ponytail)", "test-first for bug fixes (tdd)"
5. **Create `GLOSSARY.md`** only if the repo has real domain terms; otherwise skip.
6. **Show the diff** and ask the user to confirm before committing. Don't commit unasked.

Keep every file short. No speculative content, no duplicated upstream skill text.
