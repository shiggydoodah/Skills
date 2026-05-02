# LL Skills

Practical coding-agent skills for Claude Code and Codex.

This is my public collection of agent skills: skills I write myself, skills I
adapt, and useful skills I find along the way. The goal is to keep the
collection practical, readable, and easy to install across Claude Code and
Codex-style workflows.

## Skills

- [`tdd`](./skills/tdd/SKILL.md) - Test-driven development with a red-green-refactor loop.
- [`diagnose`](./skills/diagnose/SKILL.md) - Disciplined debugging for bugs and performance regressions.
- [`grill-me`](./skills/grill-me/SKILL.md) - Interview and stress-test a plan until the decisions are clear.
- [`caveman`](./skills/caveman/SKILL.md) - Ultra-compressed communication mode.
- [`write-a-skill`](./skills/write-a-skill/SKILL.md) - Create new agent skills with the right structure.

## Install In Claude Code

From your terminal:

```bash
claude plugin marketplace add shiggydoodah/Skills
claude plugin install ll-skills@ll-skills
```

Or inside Claude Code:

```text
/plugin marketplace add shiggydoodah/Skills
/plugin install ll-skills@ll-skills
/reload-plugins
```

Claude plugin skills are namespaced by the plugin name, so invoke them as:

```text
/ll-skills:tdd
/ll-skills:diagnose
/ll-skills:grill-me
/ll-skills:caveman
/ll-skills:write-a-skill
```

## Local Development

Test the plugin directly from this checkout:

```bash
claude --plugin-dir .
```

Validate the Claude manifests:

```bash
claude plugin validate .claude-plugin/plugin.json
claude plugin validate .claude-plugin/marketplace.json
```

Check skill discovery with `skills.sh`:

```bash
npx skills@latest add . --list
```

## Codex

This repo includes `.codex-plugin/plugin.json` so the same skills are ready to
be packaged for Codex plugin workflows. Claude Code distribution is the primary
install path for now.

## Attribution

The initial skills are copied from Matt Pocock's
[`mattpocock/skills`](https://github.com/mattpocock/skills)
