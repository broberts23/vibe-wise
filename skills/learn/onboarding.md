# Onboarding

Guide one step at a time. Reuse answers already given; don't dump a questionnaire.
If the profile says `Onboarding reset: pending`, reuse only answers given after
that reset. Keep this marker while onboarding is incomplete; remove it on completion.
Don't restore previous preferences or understanding from conversation or backups.
For onboarding choices, ask exactly one question at a time with 2–4 short numbered
options and brief descriptions in chat. Wait for an explicit reply before the next
question. Do not use AskUserQuestion or native pickers. Open-ended answers belong
in ordinary chat without a numbered menu.

Briefly explain: learning comes first. Ask for their approach, then give feedback,
explain unfamiliar concepts, and ask follow-ups where needed. Their reasoning shapes
the design; the agent writes the agreed implementation. Suggestions aren't an
automatic next step. This skill is tuned for Azure, Entra ID, Bicep, and Python
Microsoft SDK work, but the learner still chooses the stack and design.
Notes live in .vibe-wise/. Recommend ignoring that directory in Git. Don't
change .gitignore unless requested; announce the edit first.

## Project

Unless already answered, first ask “What are we doing?” with numbered choices:
1. New project  
2. Existing repo  
3. Known project  

Don't infer the answer from an empty folder. Wait for each answer before the next
question.

- **New:** Ask what they're building if unknown. Mark proposed architecture as
  proposed; don't invent a stack or existing components. If they describe Azure,
  identity, or automation work, keep Bicep, Entra, and SDK choices open until they
  reason about them.
- **Existing:** Inspect project guidance, entry points, dependencies, storage,
  identity configuration, Bicep/ARM templates, integrations, and deployment
  configuration. Avoid secrets and generated files.
  Save a small evidence-based map and show a concise flow with unknowns. Then ask
  codebase familiarity (1. New / 2. A little experience / 3. Know it well), followed by
  learning scope (1. Whole system / 2. Parts we touch / 3. A mix), as separate
  numbered questions.
- **Known:** Ask architecture familiarity if unknown. Inspect enough to maintain
  the map, without unnecessary introductory teaching.

## Learner

Ask only what's unknown, one question at a time:

- Programming experience: 1. Beginner / 2. Intermediate / 3. Advanced.
- Stack familiarity: 1. Beginner / 2. Intermediate / 3. Advanced. Defer if
  there is no chosen stack; accept per-technology details in free text
  (for example Azure, Entra ID, Bicep, Python SDKs).
  Existing levels remain valid: New means Beginner; Some experience or Comfortable
  mean Intermediate. Don't repeat onboarding just to update a label.
- Goal: for a beginner starting a new project, default to understanding the project
  end to end unless they already gave another goal. Say “I'll guide you through
  how this project works end to end as we build it.” Record this as a default;
  don't ask them to define a learning or capability goal. They can change it later.
  For other learners, ask about their learning focus only if it isn't already clear.
- Preferences, as a numbered question:
  1. Use defaults — Reason through each meaningful decision first; AI writes code.
  2. Customize — Adjust frequency, question style, or who writes the code.

Defaults means Normal checkpoints and open-ended reasoning; finish setup immediately.
Customize asks frequency (1. Light / 2. Normal / 3. Frequent), reasoning style
(1. Open-ended / 2. Multiple choice / 3. Mixed), and coding preference
(1. AI writes / 2. A mix / 3. More hands-on), each as a separate numbered question.
Reasoning style doesn't change setup choice menus. A longer-term capability goal is
optional; don't add a separate question if their goal already covers it.

If they want to skip setup, use defaults, mark unknown answers Not specified, and
proceed. Save known answers with state-templates.md and `Onboarding: incomplete`
plus a short Remaining onboarding list between turns. Mark complete when ready.
Self-reported experience isn't demonstrated understanding. Summarize preferences
in one sentence, then begin the build task with the learning loop in behavior.md.
