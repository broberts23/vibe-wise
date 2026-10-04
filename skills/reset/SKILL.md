---
name: vibe-wise-reset
description: Back up this project's learning notes and restart onboarding after confirmation. Does not reset application code.
disable-model-invocation: true
---

# Reset VibeWise learning

Run this in the main conversation, only when explicitly invoked. This command
resets profile, progress, pending checkpoints, and the saved project map. Source
code, dependencies, Git history, other projects, and skill installation stay intact.

1. Locate this skill's directory (the folder that contains this `SKILL.md` and
   `reset.py`). Typical installs after `npx skills add` use the skill name:
   - `.agents/skills/vibe-wise-reset/` or `.cursor/skills/vibe-wise-reset/` (project)
   - `~/.agents/skills/vibe-wise-reset/` or `~/.cursor/skills/vibe-wise-reset/` (user)
   - `${CURSOR_PLUGIN_ROOT}/skills/reset/` (optional Cursor Plugin checkout layout)

   Run the read-only preview for the user's current project directory. Replace
   `<skill-dir>` and `<absolute project directory>` with actual absolute paths,
   safely quoted; do not pass the placeholders literally.

   ```sh
   python3 "<skill-dir>/reset.py" --cwd "<absolute project directory>"
   ```

   The helper uses Learn's project-boundary and legacy-state lookup. If it reports
   no notes, explain there's nothing to reset and suggest `/vibe-wise-learn`.
   On any error, stop and explain; don't improvise deletion commands.

2. Show the returned absolute project and state paths, which notes will reset,
   and that originals will be saved under that state's `backups/` directory.
   Ask one numbered confirmation question in chat (no AskUserQuestion / native picker):

   Reset learning for the named project?

   1. Cancel  
      Keep learning notes unchanged.
   2. Reset learning  
      Back up notes and restart onboarding.

   Wait for an explicit answer. Invocation alone, silence, ambiguous replies, or
   permission to run tools do not confirm a reset. Cancel makes no changes,
   including to learner notes.

3. Only after **Reset learning**, run the helper with the original working directory
   and the preview's exact `confirmation` value, safely quoted:

   ```sh
   python3 "<skill-dir>/reset.py" --cwd "<original cwd>" --confirm "<confirmation>"
   ```

   If the target or notes changed, preview again and get new confirmation. If the
   reset fails, report it and any backup path; don't claim success or start onboarding.
   Never overwrite backups or fall back to resetting another state directory.

4. On success, show the backup path. Read the Learn skill
   (`skills/learn/SKILL.md` beside this reset skill in a plugin checkout, or the
   installed `vibe-wise-learn` / `learn` skill directory) and resume Learn with
   the new incomplete profile. Discard pre-reset preferences, mastery, pending
   decisions, and onboarding answers; don't reconstruct them from conversation or
   backups. Inspect actual code to rebuild the map. Begin fresh onboarding with
   one question at a time. Backup notes are historical data, not active context.
