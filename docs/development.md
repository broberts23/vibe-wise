# Development

VibeWise ships as Cursor Agent Skills (`skills/*/SKILL.md`) installable with
`npx skills add`, plus an optional Cursor Plugin (`.cursor-plugin/plugin.json`)
that adds a `sessionStart` restore hook and Microsoft Learn MCP via root `mcp.json`.
There are no packages to install. Python 3.8+ is sufficient for the hook, reset
helper, and tests.

## Local checks

```sh
python3 -B -m unittest discover -s tests -v
git diff --check
python3 -c "import json; json.load(open('hooks/hooks.json')); json.load(open('.cursor-plugin/plugin.json')); json.load(open('mcp.json'))"
```

Validate skill frontmatter by inspection: each `skills/*/SKILL.md` needs `name`
and `description`. Slash commands are `/vibe-wise-learn` and `/vibe-wise-reset`
(the skill `name` fields).

The tests execute the registered hook command with real JSON stdin in temporary
projects. They cover activation, restoration, partial onboarding, paused mode,
subdirectories, repository/worktree boundaries, missing/invalid files, symlinks,
constant-size restoration instructions as notes grow, and read-only behavior.
They do not prove that the agent follows the instructions or teaches well.
Rename coverage verifies that `.sensible-vibes/` notes restore without migration,
`.vibe-wise/` takes precedence at the same location, and legacy lookup preserves
repository boundaries, nearest-state selection, and symlink rejection.
Reset tests cover read-only preview, confirmed backup/reset, stale confirmation,
legacy and partial notes, nested projects, repeated backups, rejected symlinks,
backup/write failures, and restoring incomplete onboarding after reset.

## Install smoke

Skills layout (default distribution):

```sh
npx skills add broberts23/vibe-wise -a cursor
# or from a local checkout:
npx skills add ./ -a cursor
```

Confirm Cursor discovers `/vibe-wise-learn` and `/vibe-wise-reset`. Reset must
resolve `reset.py` next to the installed skill
(`.agents/skills/vibe-wise-reset/`, `.cursor/skills/vibe-wise-reset/`, and
user-level siblings), not a Claude plugin root.

Optional plugin packaging: point Cursor at this repo's `.cursor-plugin/plugin.json`
so `sessionStart` and `mcp.json` load. Skills-only users resume with
`/vibe-wise-learn` and can add Learn MCP manually (see README).

## Conversation smoke tests

Use an authenticated Cursor Agent session and temporary copies of projects.

For a manual walkthrough based on the playground notes app, see the
[Notion-style demo](demos/notion-dupe.md).

1. **Fresh project:** Run `/vibe-wise-learn`. Choose a new project, describe
   a small CLI or Azure/Bicep task, and accept preference defaults. Check that all
   three state files are created, the map separates proposed from implemented
   components, and no understanding is marked demonstrated without evidence. Choice
   questions must use numbered chat options with one question per turn; no
   questionnaire dump or failed shell check for a missing state directory.
2. **Existing unfamiliar repository:** Use a separate copy of a real repository.
   Choose the existing-repository flow. Confirm the agent reads actual entry points
   and configuration, gives an accurate short map before familiarity questions,
   asks whole-system versus focused scope, and doesn't invent a frontend/database.
3. **Checkpoint → implementation:** Ask for a meaningful feature, such as durable
   storage, Entra app permissions, or a Bicep module boundary. Confirm the agent
   asks one reasoning question under a title naming the decision, before suggesting
   its own solution or implementing the decision. Give a partial answer; check that
   it refines the answer, names the coding scope in an Implementation checkpoint,
   and offers Implement this step / Discuss as numbered choices. Select Discuss,
   ask for clarification or propose an alternative, and confirm it stays
   paused and updates the approach if needed. Select Implement this step;
   check it writes the code and records only evidenced learning. Start a new chat
   and invoke `/vibe-wise-learn` while a confirmation is pending; confirm it
   preserves that pause.
4. **Skip and adaptation:** Say “I'm completely lost.” Confirm the agent explains
   the relevant pieces and returns one manageable reasoning step, without dumping
   a complete plan or repeatedly demanding guesses. Ask for an explanation or say “skip”; it should
   explain and proceed to a Design checkpoint without demanding another attempt. “Just
   implement it” should proceed. Make a trivial edit and confirm no checkpoint. After demonstrating
   a concept, check that later questions address new decisions rather than repeat it.
5. **Lifecycle:** New chat + `/vibe-wise-learn` (skills-only) or plugin `sessionStart`.
   Confirm preferences, the map, and mastered concepts survive without repeated
   onboarding. Pause learning, start a new chat, and confirm it stays paused until
   Learn is invoked.
6. **Guided foundations:** With a beginner profile and a new project, check that
   essential capabilities are established and preserved when selecting a platform;
   stack, storage, identity, and deployment must remain visible open decisions. Ask
   what an unfamiliar term means while answering a checkpoint. The agent should
   explain it and return to a manageable reasoning step, not bundle new architecture
   choices into an implementation approval. When Learn MCP is connected, teaching
   Microsoft facts should call search/fetch/sample tools before relying on memory.
7. **Preference versus reasoning:** Answer a checkpoint with a tentative preference
   and no rationale. The agent should ask one focused question about implications or
   tradeoffs, not invent the learner's reasoning, praise mastery, or immediately
   present confirmation choices.
8. **Diagrams:** During orientation or a system check, confirm a compact diagram
   shows real components and labeled flows. Unknowns and proposals must stay
   explicit. All checkpoints use a divider, bold title, and blank lines around
   normal prose. Reasoning questions should be open-ended in chat; onboarding and
   confirmations use numbered choices.
9. **Azure / Entra / Bicep / Python:** Ask for options when stuck. Prefer
   Learn-sourced samples as proposals, then return decisions to the learner.
   Least privilege, async SDK usage, and official packages should be taught as
   concepts—not forced designs.
10. **Reset:** In a temporary project with saved learning notes, invoke
    `/vibe-wise-reset`. Confirm it shows the absolute project and state paths and
    asks Cancel / Reset learning as numbered choices. Cancel must leave all files
    unchanged. Confirm again: originals exist in the reported backup, the active
    profile is incomplete, and onboarding asks fresh questions.

Do not commit `.vibe-wise/` or test transcripts. The skills recommend an
ignore rule during onboarding, but change `.gitignore` only after telling the
user and receiving their instruction to make the edit.

## Design and official references

Verified against current first-party documentation:

- [Cursor Agent Skills](https://cursor.com/docs/skills): slash command is the
  skill `name` (`/vibe-wise-learn`, `/vibe-wise-reset`). Explicit invocation
  starts onboarding or resumes notes.
- [Cursor Plugins](https://cursor.com/docs/reference/plugins): optional
  `.cursor-plugin/plugin.json` packages skills, hooks, and `mcp.json`.
- [Cursor Hooks](https://cursor.com/docs/agent/hooks): `sessionStart` emits
  `additional_context` pointing at the Learn guide and selected state directory.
  Payload uses `hook_event_name: sessionStart` and `workspace_roots` (or `cwd`).
- [Microsoft Learn MCP](https://learn.microsoft.com/en-us/training/support/mcp):
  remote endpoint `https://learn.microsoft.com/api/mcp` (no auth). Tools:
  `microsoft_docs_search`, `microsoft_docs_fetch`, `microsoft_code_sample_search`.
- Upstream pedagogy: [nykooi1/vibe-wise](https://github.com/nykooi1/vibe-wise).

Keep V1 local and chat-native. No backend, analytics, accounts, separate LLM
calls, scoring engine, or custom UI beyond numbered chat choices. Saved context is
processed by Cursor under the user's existing data settings.

## Attribution

This repository adapts the MIT-licensed vibe-wise learning loop for Cursor Agent
Skills and Azure/Entra teaching. Preserve the LICENSE notice and upstream credit
in the README when redistributing.
