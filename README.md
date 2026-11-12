# compound-skills

Two Claude Code skills for the parts of a feature that happen around the code. `prd-bake` questions you until every decision in a feature is made and written down. `compound-engineering` runs the loop around the build: plan, work, review, then write down what the work taught you so the next feature starts ahead.

Both come from the same habit. A plan that skips the questions gets them back later as rework, usually at the worst possible moment, in the middle of the build.

**Owner line:** a feature that gets planned this way arrives with its decisions, tests and lessons on paper, so the second one like it costs less than the first.

## What's in here

| Path                                     | What it does                                                                                                                                                   |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `prd-bake/SKILL.md`                      | Researches the codebase, asks numbered multiple-choice rounds (reply `1A, 2C`, or `rec` to take every recommendation), then writes `tasks/prd-[feature].md`    |
| `prd-bake/references/mobile-first-ui.md` | The mandatory website UI rules (R1 to R13), Flexbox and Grid recipes, a bottom-nav pattern, banned patterns and the acceptance criteria every UI story carries |
| `compound-engineering/SKILL.md`          | The Plan, Work, Review, Compound loop, built around one ladder: a lesson goes in a shared helper or a failing test before it goes in a sentence                |

## Install

Clone once, then link each skill into your global skills folder:

```sh
git clone https://github.com/Justproof/compound-skills.git ~/.claude/compound-skills
ln -s ~/.claude/compound-skills/prd-bake ~/.claude/skills/prd-bake
ln -s ~/.claude/compound-skills/compound-engineering ~/.claude/skills/compound-engineering
```

That makes both available in every project Claude Code opens, and `git -C ~/.claude/compound-skills pull` updates them in place. For one project only, link into that project's `.claude/skills/` instead.

## Use it

Describe the feature and ask for a bake:

```text
/prd-bake a page where customers can book a 30-minute call
```

The first reply restates the idea, names the branches it plans to question, and asks round one in the same message. The output line that matters is the reply prompt:

```text
**Reply:** `1A, 2C`, or `2D: your explanation`, or `rec` to take every recommendation.
```

Question numbers keep counting across rounds, so `5D` means one thing for the whole session and becomes a row ID in the PRD's decisions table. That table is what lets someone open the code six months later and find out why it looks the way it does.

The PRD is saved to `tasks/`. From there, `compound-engineering` takes over the build and, at the end, sorts what the work taught you onto a ladder:

| Rung | Where the lesson goes                                 | Why it lasts                            |
| ---- | ----------------------------------------------------- | --------------------------------------- |
| 1    | One shared helper, component or config row            | The wrong way is no longer on the menu  |
| 2    | A regression test, lint rule, ratchet or hook         | The mistake stops the build             |
| 3    | The skill, subfolder file or comment next to the work | It is read at the moment it applies     |
| 4    | A line in `CLAUDE.md` or `AGENTS.md`, with its reason | It is read, and paid for, every session |
| 5    | The chat                                              | It does not                             |

The habit is to reach for rung 4, which is why instruction files grow until nobody reads them. So the skill asks for the highest rung that fits, and its closing report says which rung each lesson went on:

```text
Compounded:
  - [rung 2] ratchet on hand-written hover gates, ceiling 83 (tests/Feature/LayoutRatchetTest.php)
Dropped: two observations that would not change a future session
```

A ratchet is the rung for a rule your old code already breaks. It counts today's violations, fails when the count rises, and fails when it falls too, with a message telling you to lower the ceiling. That second failure is what keeps the number in the file true.

You do not have to run the whole loop. Say "build this prd" and it starts from the PRD, reading the decisions table and the Compounding Opportunities section instead of planning again. Say "what did we learn" at the end of any session and it runs only the compound step: harvest the corrections, root causes and wrong instructions, sort them, build the rungs.

## What each skill refuses to do

`prd-bake` never spends a question on something a file read can answer, and it does not start implementing. Its UI rules apply to website code only (pages, web apps, browser extension pages), and it will not offer a hamburger menu, a hover-only action or a desktop-first layout as an option. A user decision recorded in the decisions table is the only way to waive one.

`compound-engineering` does not trust your instruction files, which sounds rude until one of them is wrong. It treats each rule as a claim, checks the ones that name a file, version or count against the code, and corrects a wrong one at its source instead of stacking a second rule on top. It also will not call a guard finished until it has undone the fix and watched the guard fail. It never assumes `npm test`; it reads your project's own commands first.

## See also

A PRD is prose too, and prose can read like a list even when every fact in it is right. [rechain](https://github.com/Justproof/rechain) checks that each paragraph follows from or reverses the one before it, never "and then", and rewrites the joints that fail.

## Credits

The Flexbox and Grid recipes in `mobile-first-ui.md` are distilled from Shruti Balasa's _Complete Guide to CSS Flex & Grid (Vanilla CSS)_. The compound engineering loop is inspired by the approach Every describes at [every.to](https://every.to); this is an independent write-up, not their plugin.

## License

MIT. See `LICENSE`.
