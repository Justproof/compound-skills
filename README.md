# compound-skills

Two Claude Code skills for the part of a feature that happens before the first line of code. `prd-bake` questions you until every decision in a feature is made and written down. `compound-engineering` runs the loop around the build: plan, work, review, then write down what the work taught you so the next feature starts ahead.

Both come from the same habit. A plan that skips the questions gets them back later as rework, usually at the worst possible moment, in the middle of the build.

**Owner line:** a feature that gets planned this way arrives with its decisions, tests and lessons on paper, so the second one like it costs less than the first.

## What's in here

| Path                                     | What it does                                                                                                                                                   |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `prd-bake/SKILL.md`                      | Researches the codebase, asks numbered multiple-choice rounds (reply `1A, 2C`, or `rec` to take every recommendation), then writes `tasks/prd-[feature].md`    |
| `prd-bake/references/mobile-first-ui.md` | The mandatory website UI rules (R1 to R13), Flexbox and Grid recipes, a bottom-nav pattern, banned patterns and the acceptance criteria every UI story carries |
| `compound-engineering/SKILL.md`          | The Plan, Work, Review, Compound loop: a plan template, a review checklist, and templates for recording patterns, decisions and lessons in `AGENTS.md`         |

## Install

Clone once, then link each skill into your global skills folder:

```sh
git clone https://github.com/Justproof/compound-skills.git ~/src/compound-skills
ln -s ~/src/compound-skills/prd-bake ~/.claude/skills/prd-bake
ln -s ~/src/compound-skills/compound-engineering ~/.claude/skills/compound-engineering
```

That makes both available in every project Claude Code opens, and `git -C ~/src/compound-skills pull` updates them in place. For one project only, link into that project's `.claude/skills/` instead.

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

The PRD is saved to `tasks/`. From there, `compound-engineering` takes over the build and, at the end, asks what the work taught you and where that belongs: `AGENTS.md`, a regression test, or a comment the code could not do without.

## What each skill refuses to do

`prd-bake` never spends a question on something a file read can answer, and it does not start implementing. Its UI rules apply to website code only (pages, web apps, browser extension pages), and it will not offer a hamburger menu, a hover-only action or a desktop-first layout as an option. A user decision recorded in the decisions table is the only way to waive one.

`compound-engineering` is deliberately generic. Its quality checks use `npm` commands as placeholders, so swap in your project's own test and lint commands.

## See also

A PRD is prose too, and prose can read like a list even when every fact in it is right. [rechain](https://github.com/Justproof/rechain) checks that each paragraph follows from or reverses the one before it, never "and then", and rewrites the joints that fail.

## Credits

The Flexbox and Grid recipes in `mobile-first-ui.md` are distilled from Shruti Balasa's _Complete Guide to CSS Flex & Grid (Vanilla CSS)_. The compound engineering loop is inspired by the approach Every describes at [every.to](https://every.to); this is an independent write-up, not their plugin.

## License

MIT. See `LICENSE`.
