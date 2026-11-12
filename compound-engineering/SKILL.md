---
name: compound-engineering
description: "Compound Engineering workflow for AI-assisted development: Plan, Work, Review, Compound, where each unit of work leaves the project easier to change. Use when planning features, executing a plan or PRD, reviewing code, or recording what a piece of work taught: a bug's root cause, a correction from the user, a rule that turned out to be wrong. Triggers on: plan this feature, implement this, build this prd, review this code, compound learnings, what did we learn, record this lesson, retro, create implementation plan, systematic development."
---

# Compound Engineering

Each unit of work should make the next one easier. The loop is Plan, Work, Review, Compound. Inspired by the approach Every describes at every.to.

The first three steps are ordinary good practice. The fourth is the one that gets skipped, and it is the only one that compounds, so this skill spends most of its words there.

## The one idea: a lesson is only as durable as the place it is stored

A lesson written as a sentence depends on someone reading the sentence. A lesson written as a failing test depends on nobody. Before recording anything, pick the highest rung of this ladder that fits:

| Rung                              | Form                                                                                    | Survives because                                     |
| --------------------------------- | --------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| 1. Remove the choice              | One shared helper, component, config row or generator that every caller uses            | The wrong way no longer exists to be chosen          |
| 2. Make it fail                   | A regression test, lint rule, type, architecture test, ratchet or pre-commit/agent hook | The mistake stops the build                          |
| 3. Put it where the work happens  | A skill, a subdirectory instruction file, a comment on the trap itself                  | It is read at the moment it applies                  |
| 4. Put it in the instruction file | One line in CLAUDE.md, AGENTS.md or whatever the agent loads                            | It is read every session, and paid for every session |
| 5. Leave it in the chat           | Nothing                                                                                 | It does not                                          |

Rules of the ladder:

- **Rung 4 is the default habit and the weakest real option.** Every line there costs context in every future session and can drift from the code without anything noticing. Use it for conventions that cannot be checked mechanically (naming, voice, which of two valid patterns this project prefers), and for pointers to rungs 1 to 3.
- **Pair a low rung with a high one when you can.** A rule line that names the test enforcing it tells the reader both what and where the proof lives.
- **If the same hand-copied code caused the bug twice, the lesson is rung 1.** Extract it, move the callers, then write the line that says "use X, never copy it".
- **Rung 5 is a decision too.** Most observations from a session are not lessons. Drop them on purpose.

## Step 1: Plan

If a PRD or plan already exists (for example `tasks/prd-*.md` from the `prd-bake` skill), read it and build from it. Do not re-plan. Read its decisions table, its implementation order and its "Compounding Opportunities" section; that last one is a list of rung 1 to 4 work the planner already spotted.

If the feature is undecided (open questions about scope, behaviour, data or UI), the plan is a questioning job: use `prd-bake` when it is installed, or ask the questions yourself before writing code.

If there is no plan and the work is small, write a short one in the reply before starting:

- **Context:** current behaviour, desired behaviour, who is affected.
- **Research:** the files this touches or resembles, how the last similar change was made (`git log` on those paths), and what the instruction files say about the area. Cite paths.
- **Acceptance criteria:** each one checkable as pass or fail by someone who was not in the conversation.
- **Order:** steps that each leave the project working.
- **Compounding:** what this work should leave behind, by rung.

Research has one extra duty: **treat instruction files as claims, not facts.** They were true when written. Where a rule names a file, version, command or count, check it against the code before relying on it. A rule that turns out to be wrong is the most valuable finding of the session (see "Fix the rule").

## Step 2: Work

1. **Find the project's real commands first.** Test, lint, format and typecheck commands come from the instruction file, then `composer.json` / `package.json` / `Makefile` / CI config. Never assume `npm test`.
2. **Record the baseline.** Run the narrowest relevant tests before changing anything and note what already fails. A failure you did not cause is still worth one line in the final report, and it is impossible to attribute later without the baseline. If you forgot, stash the changes and rerun.
3. **Work on a branch or worktree** unless the project commits to its main branch by convention; check recent history rather than guessing.
4. **One step at a time, test after each.** When something fails, find the cause before moving on. A failure you do not understand is a lesson you have not collected yet.
5. **Stay in scope.** Things you notice outside the task go in the report as findings, not into the diff.
6. **Keep a running list of surprises.** Anything that made you stop, look something up twice, or change approach. This list is the raw material for Step 4 and it does not survive memory.

## Step 3: Review

Review by measuring, not by rereading. For each acceptance criterion, name the command, test or observation that proves it, and run it.

- **Behaviour:** tests cover the acceptance criteria, including the empty, error and permission-denied cases.
- **In the running thing:** for anything with an interface, check it in a real browser or device at the size users have. Tests passing is not the same as the feature working.
- **Fit:** follows the patterns found in research; no new abstraction with one caller; no debug output left behind.
- **Safety:** no secrets in the diff, input validated at the boundary, queries not multiplied per row.
- **Guards bite:** for every new test or check that exists to prevent a mistake, undo the fix, watch it fail with a message that names the problem, then restore the fix. A guard that has never failed has not been tested.
- **Docs agree:** search the instruction files, skills and docs for the terms this change touched. If any of them now says something false, that is part of this change.
- **Prose chains:** when the change adds or rewrites prose people read in order (a README, a guide, a PRD, a post), each paragraph should follow from or reverse the one before it, never "and then". Use [`rechain`](https://github.com/Justproof/rechain) when it is installed to find and fix the joints that fail, or read the paragraphs in pairs and ask whether "so" or "but" fits between them.

Then read the diff once as the maintainer who opens it in six months with no context: is the reason for each non-obvious line findable?

## Step 4: Compound

### 4a. Harvest

Go through the session and collect candidates from five sources:

1. **Corrections.** Anything the user had to tell you. If they said it once, a future session should not need to hear it.
2. **Root causes.** Each bug fixed: what made it possible, not what fixed it.
3. **Wrong instructions.** A rule, skill or doc that contradicted the code, or two that contradicted each other.
4. **Repeats.** Anything you looked up, derived or hand-wrote that an earlier session clearly also did.
5. **The surprise list** from Step 2.

### 4b. Sort

For each candidate ask, in order:

1. Would this change what a future session does? If not, drop it.
2. Is it already recorded (code, test, git history, instruction file)? If so, drop it or fix the existing record.
3. What is the highest rung it fits? Build that.

### 4c. Build the rung

**Rung 1, remove the choice.** Extract the helper, component or config table; move existing callers if that is in scope, or leave them and add a ratchet so the count can only fall.

**Rung 2, make it fail.** Write the test so that its failure message is the lesson: say what was found, where, and what to do instead. Three forms cover most cases:

- _Regression test:_ reproduces the bug, named for the behaviour.
- _Rule test:_ scans the source for a banned pattern (a string, a class name, a call) and fails with the file and line. Use the project's linter or architecture-test tool when it has one.
- _Ratchet:_ for a rule the existing code already breaks too often to fix today. Count the violations now, store the number as a ceiling, fail when the count rises, and also fail when it falls with a message telling the reader to lower the ceiling. New code is held to the rule, old code is paid down in passing, and the number in the file is an always-current measure of the debt.

If the project runs agent or git hooks and the mistake is an action rather than code (deploying with uncommitted changes, reading a secrets file), a hook is the rung 2 form.

**Rung 3, where the work happens.** Prefer the narrowest place: the skill that governs the task, an instruction file in the subdirectory, a comment on the line that looks wrong but is right. When the lesson belongs to a shared skill or reference that other projects also use, fix it there, not in a local copy, and keep project-specific paths and names out of anything shared.

**Rung 4, the instruction file.** Write lines that a reader can audit later:

```markdown
- **[Rule, as an instruction].** [Why, with the incident: what happened, when,
  or the ticket/commit]. [Where the proof lives: test path, helper name.]
```

The reason is what lets a later session decide whether the rule still holds. A rule with no reason can never be safely deleted, so it never is, and the file grows until nobody reads it.

Before editing, **find the file's source.** Some instruction files are generated from another directory by a tool, and edits to the output are overwritten on the next run. Look for a generator in the dependency list or a header naming the source, and edit the source.

### 4d. Fix the rule

When an instruction turned out to be wrong:

1. Correct it at its source, in place. Do not add a second rule that contradicts the first.
2. State the measured fact that replaced it (a count, a version, a command's output), so the next reader can recheck it the same way.
3. Search for every other place that repeats the old claim: other docs, skills, comments, templates.
4. Name one source as the winner when two files cover the same ground, and say so in the loser.

### 4e. Prune

Compounding includes subtraction. While in the instruction file, delete or shorten:

- a rule that a test, linter or helper now enforces (keep a pointer if the rule is not obvious from the tool);
- a rule about code that no longer exists;
- a rule whose reason no longer holds.

### 4f. Close out

- If the work came from a PRD, update its status line and add a dated amendment for any decision the build changed. Do not rewrite the original decisions.
- Run the tests for everything added in this step, plus the formatter.
- Report in this shape, so the compounding is visible and not buried in the summary:

```text
Built: [what, in one or two lines]
Verified: [commands run and their results; anything not verified, said plainly]
Compounded:
  - [rung] [what was added or changed] ([path])
Dropped: [candidates considered and not recorded, one line]
Found, not fixed: [pre-existing failures, out-of-scope problems, decisions for the user]
```

Follow the project's rules on creating documentation files, committing and pushing. This skill never overrides them: if the project says to ask before adding a doc or a dependency, ask.

## Using one step on its own

- **"Plan this":** Step 1, then stop with the plan.
- **"Build this plan / PRD":** Steps 2 to 4.
- **"Review this":** Step 3 against the diff or PR, then 4a and 4b as a list of proposed compounding, not applied.
- **"What did we learn" / "compound this":** Step 4 only, over the current session or the named work.

## Signs it is working

- A mistake made once is caught by a check the second time, not by a person.
- Instruction files stay about the same length while the test suite's rule checks grow.
- Ratchet ceilings go down.
- A new session asks fewer questions that an old session already answered.
