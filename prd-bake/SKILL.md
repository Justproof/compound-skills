---
name: prd-bake
description: Considering a task the way a field-leading expert would. Bake a feature idea into an implementation-ready PRD. Researches the codebase, then asks rounds of multiple-choice questions (answered like "1A, 2C") until every decision is made, then writes tasks/prd-[feature].md with a decisions table, small verifiable stories and an ordered plan. Use whenever the user wants to plan, scope or spec a feature, tool, page or skill before building it, even without saying "PRD": "bake this", "write a prd", "plan this feature", "spec out", "question me on", "compound engineering". Rather than operating at the task’s layer, we go one level deeper and touch the underlying data, tools, and problems.
---

# PRD Bake

Turn a rough idea into a PRD that someone (or an agent) can build from without coming back to ask questions. The value is in the baking: research, then relentless questioning, then a document that records every decision and why it was made.

Two principles drive everything below:

1. **Decisions belong to the user; facts belong to the codebase.** Never spend a question on something a file read can answer.
2. **Compound engineering.** Every unit of planning should make the next piece of work easier, not harder. Reuse what exists, and write down the patterns and lessons this feature creates so the next PRD starts further ahead.

Do not start implementing. The deliverable is the PRD.

## Workflow

### Step 1: Research before asking

Before the first question, spend a few minutes in the project:

- Read the project instructions (CLAUDE.md and similar) and note conventions the PRD must respect: stack, testing rules, naming, voice.
- Find the code this feature touches or resembles: existing components, models, routes, commands, skills.
- Skim `tasks/` for earlier PRDs on related features, and recent git history for the area.
- Check saved memories for relevant decisions or feedback.

This is what makes the questions sharp. "Should cards reuse the existing `Panel` component or get their own?" is a better question than "What should cards look like?", and you can only ask it after looking. Keep the findings; they go into the PRD's Research Findings section.

If there is no codebase yet (a new project), research is the user's prior notes, sibling projects and any reference material they point to.

### Step 2: Restate and frame

- Restate the feature in two or three sentences in your own words, including what you found in research that shapes it.
- Name the branches you intend to question (see the list below) so the user can strike any that do not apply.

Then go straight into round one in the same message. Do not wait for permission to start asking.

### Step 3: Question relentlessly, concisely

Ask 3 to 6 multiple-choice questions per round. Walk every branch of the decision tree that applies:

- Problem and goal: what hurts today, what "done" changes
- Users: who it is for, who it is not for
- Scope and non-goals: the smallest version worth shipping, what is explicitly out
- Core behaviour: the main flow, then each step's alternatives
- Edge cases and failure states: empty, error, offline, permission denied, partial success
- Data and integrations: what is stored, where it comes from, what else it touches
- UI and UX, if the feature has an interface: layout, navigation, touch, states
- Verification: how each piece will be tested and seen working
- Rollout: migration, backfill, flags, what happens to existing data
- Success metrics and risks

Question format:

```text
**1. Where should a finished run be saved?**

A. File download to a fixed folder (recommended: no server needed, matches how the ingest skill reads files)
B. POST to the Laravel app
C. Clipboard only
D. Other: [specify]

**2. Who starts a run?**

A. The user, manually (recommended: keeps v1 small)
B. A schedule
C. Both
D. Other: [specify]

**Reply:** `1A, 2C`, or `2D: your explanation`, or `rec` to take every recommendation.
```

Rules that make this work:

- **Number questions continuously across rounds** (round two starts at 7, not 1). The numbers become the IDs in the PRD's decisions table, so `5D` must mean one thing for the whole session.
- **Mark one option as recommended and say why in a few words.** You did the research; share the opinion up front so the user can accept with a letter instead of composing an argument. If you truly have no basis to prefer one, say so rather than inventing a preference.
- **Options must be real alternatives**, mutually exclusive and specific to this feature. Always include an "Other" escape hatch.
- **Keep each question to one decision.** If an option needs a paragraph to explain, it is two questions.

After each reply:

1. Record the answers in one compact list (`1A`, `2C`, ...).
2. Where the user departed from your recommendation, accept it and adjust; push back only if it conflicts with an earlier answer or with something found in research, and say exactly what conflicts.
3. Flag new dependencies, risks or assumptions the answers created.
4. Ask the next round, going deeper into whatever the answers opened up.

**When to stop.** Stop when another question would not change a user story, a requirement or the implementation order. Small features take one or two rounds; large ones may take five. If the user says to wrap up, stop, and put whatever is unresolved in Open Questions with your recommended default for each. Do not keep asking to be thorough; unanswered trivia is cheaper than a tired user.

### Step 4: Write the PRD

Save to `tasks/prd-[kebab-case-feature-name].md` in the project the feature belongs to (create `tasks/` if needed). If the feature will live in a different or new project, say so in the header and ask where the file should go only if it is truly ambiguous.

Follow the project's writing conventions for prose (voice rules, banned phrases, and so on). Use this structure. Sections marked "if applicable" are dropped, not left empty.

```markdown
# PRD: [Feature Name]

**Project:** [path or name, and where the feature lives]
**Date:** [today]
**Status:** Draft

## Introduction / Overview
The problem, who has it, current versus desired behaviour. End with the one
question or outcome this feature exists to deliver.

## Goals
- G1. Specific and measurable.

## Decisions (from questioning)
| #  | Decision |
| -- | -------- |
| 1A | Runs are saved as a file download to `~/Downloads/...` |
One row per question, ID = question number + chosen letter, written as the
decision itself (not the question). Add a short "why" where the reason is not
obvious or where the user overrode the recommendation.

## Research Findings
What the codebase, git history, docs and earlier PRDs showed: patterns to
reuse, constraints, prior art, surprises. Cite file paths.

## User Stories
### US-001: [Title]
**As a** [user], **I want** [capability] **so that** [benefit].

**Acceptance criteria:**
- [ ] Verifiable statement
- [ ] Automated test covers [behaviour], per the project's testing convention
- [ ] (Website UI stories) The mandatory mobile-first criteria from references/mobile-first-ui.md
- [ ] (UI stories) Verified in a real browser

## Functional Requirements
- FR-1. Explicit and unambiguous. Reference the decision it comes from where useful (see 5D).

## Non-Goals
What this deliberately does not do, so scope creep has something to bounce off.

## Design Considerations (required for any feature with an interface)
For website code: target devices and breakpoints, the Flexbox or Grid layout
per screen, the mobile-first rules this feature exercises. For other
interfaces: the platform conventions that apply. See "Website UI" below.

## Technical Considerations
Existing patterns to follow, integration points, dependencies, data model
changes, performance constraints of the target hardware.

## Implementation Plan
Ordered steps, each mapping to one or more stories, sequenced so every step
leaves the project working. Note what can be verified after each step.

## Success Metrics
How we will know it worked, tied back to the goals.

## Risks & Mitigations
| Risk | Mitigation |
Pulled from the questioning and research.

## Compounding Opportunities
Patterns, components, docs, tests or skills this work should leave behind so
the next feature is cheaper. Be concrete: "extract X into a reusable
component", "record Y in CLAUDE.md", "add Z to the test helpers".

## Open Questions
Anything unresolved, each with a recommended default so work is not blocked.
```

**What makes stories good.** Each story should be small enough to build and verify in one focused session; if a story needs "and" in its title, split it. Acceptance criteria must be checkable by someone who was not in the conversation: "shows a red inline error under the email field when the address is invalid" passes, "handles errors well" does not. Vague criteria are where implementations drift, and an agent working from the PRD has nothing else to go on.

**Browser verification.** UI stories carry a real-browser check because tests passing is not the same as the feature looking right. Word the criterion for whatever the project actually has: a browser automation skill or tool if one is available in the session (for example Claude in Chrome), the project's browser test suite (for example Pest browser tests), or a manual check as a last resort. Do not reference a tool that does not exist in the project.

**Website UI: the mobile-first rules are mandatory.** The rules apply to website coding only: pages and web apps built with HTML and CSS that people open in a browser, including a browser extension's own pages (popup, options, full-tab pages). They do not apply to other kinds of interface (a terminal tool, a native app, an email, generated images or video); for those, write Design Considerations from the questioning and the platform's own conventions, and leave the mobile-first criteria out unless the user asks for them. When the feature does touch website code, read `references/mobile-first-ui.md` before the UI round of questioning and again before writing Design Considerations and the UI stories. It holds the mobile-first, touch-friendly rules (R1 to R13), the Flexbox and Grid recipes, the bottom navigation pattern, the banned patterns and the acceptance criteria every UI story must carry. These are requirements, not suggestions: the project's design system supplies colours, fonts and component names but cannot relax a rule, and only an explicit user decision recorded in the decisions table can waive one. Do not offer a banned pattern (a hamburger menu, a desktop-first layout, a hover-only action) as a question option. When the feature has no interface at all (a skill, a background job, a content rule), skip the Design Considerations section entirely.

### Step 5: Self-check, then hand over

Before presenting, reread the PRD once as the implementer who never saw this conversation:

- Does every decision in the table show up in a story or requirement, and does nothing contradict it?
- Could each acceptance criterion be marked pass or fail without asking anyone?
- Are file paths, component names and commands real (checked against the codebase), not guessed?
- Is anything in Non-Goals accidentally required by a story?
- For a website feature: does every UI story carry the mandatory mobile-first criteria, and does nothing in the PRD specify a banned pattern?
- Would the implementation order work if steps were done one per session?

Fix what fails, then reply with: the file path, a five-line-or-less summary of what will be built, the open questions (if any) and the first step of the implementation plan. Leave the full document in the file rather than pasting it into the chat.

## After the PRD exists

PRDs are living documents. When a later review or the build itself changes a decision, do not silently rewrite history: add a dated section near the top (for example `## Review Amendments (2026-09-17)`) listing what changed and why, update the affected stories and requirements, and update the **Status** line (`Draft`, `Approved`, `In progress`, `Built [date]`). The decisions table plus the amendments are what let a future session understand why the code looks the way it does.
