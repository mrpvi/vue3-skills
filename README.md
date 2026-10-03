# Vue Contribution Guidelines

A portable Claude Code skill for consistent Vue development: project structure,
naming, component contracts, state ownership, BEM, and pragmatic Clean Code.
It defaults to Pinia and Tailwind when setting up a project without an existing
state manager or styling method, while preserving established tools and keeping
AI changes strictly within the requested scope.

## Install in Claude Code CLI

### Personal installation — available across your projects

Run from the directory containing this skill folder (for example, your Desktop):

```sh
mkdir -p ~/.claude/skills
cp -R ./vue-contribution-guidelines ~/.claude/skills/
```

The installed entry point should be:

```text
~/.claude/skills/vue-contribution-guidelines/SKILL.md
```

Copy the entire folder, including `references/`, not only `SKILL.md`. If the
destination already exists, review or back it up before replacing it.

### Project-only installation

Instead of the personal installation, run from your project's root:

```sh
mkdir -p .claude/skills
cp -R ~/Desktop/vue-contribution-guidelines .claude/skills/
```

Commit the project-level skill if you want to share it with your team.

## Use

Start a new Claude Code session in your Vue project:

```sh
claude
```

Invoke the skill explicitly in the Claude prompt:

```text
/vue-contribution-guidelines Fix this Vue form's validation. Do not refactor unrelated code.
```

Claude can also load the skill automatically when your request matches its
description. Installing this skill does **not** install Pinia, Tailwind, or any
application dependencies.
