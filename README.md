# Personal Agent Skills

A small, public collection of portable agent skills. This repository is the
single editable source for the skills I choose to use with Codex, Claude Code,
and compatible agents.

## Included skills

- `skills/productivity/handoff` — create a compact, redacted handoff document
  for a fresh agent session.
- `skills/productivity/writing-great-skills` — reference material for writing
  and maintaining predictable skills.

Both skills are currently preserved from
[mattpocock/skills](https://github.com/mattpocock/skills), under its MIT
license. Their source is retained verbatim so that upstream provenance and
behaviour are clear.

## Repository layout

```text
skills/
  productivity/
    handoff/
    writing-great-skills/
```

Each skill lives in its own directory and is defined by `SKILL.md`. Supporting
files required by a skill, such as `GLOSSARY.md`, stay beside it.

## Editing and syncing

Edit skills in this repository, commit the change, then install or sync the
chosen skill into each agent's own skill directory. Do not treat an installed
copy such as `~/.codex/skills/<name>` as the canonical source.

Before changing an upstream-derived skill, record the local reason in the
commit message and keep this README's attribution current. Upstream updates
should be reviewed and applied deliberately instead of overwriting local
changes.
