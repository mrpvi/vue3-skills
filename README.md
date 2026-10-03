# Vue Contribution Guidelines

A portable Claude Code skill for consistent Vue development: project structure,
naming, component contracts, state ownership, BEM, and pragmatic Clean Code.
It defaults to Pinia and Tailwind when setting up a project without an existing
state manager or styling method, while preserving established tools and keeping
AI changes strictly within the requested scope.

## Install in Claude Code CLI

### Personal installation — available across your projects

Once the plugin files are on the repository's default branch, run in your terminal:

```sh
claude plugin marketplace add mrpvi/vue3-skills
claude plugin install vue3-skills@vue3-skills-marketplace
```

This registers this repository's plugin catalog and installs the plugin at user
scope. Start a new Claude Code session after installation, or run
`/reload-plugins` in an existing session.

### Project-only installation

Add the marketplace as above, then run this from your project's root instead of
the personal installation command:

```sh
claude plugin install vue3-skills@vue3-skills-marketplace --scope project
```

Commit the resulting project settings if you want to share the configuration
with your team. Each collaborator still needs to install the plugin locally.

### Try a local checkout without installing

From your Vue project's directory, start a session with the plugin loaded:

```sh
claude --plugin-dir /absolute/path/to/vue3-skills
```

Replace the path with this repository's local checkout. The plugin is loaded
only for that session, without a permanent installation.

**Plugin command:** use `/vue3-skills:vue-contribution-guidelines` instead of the
standalone `/vue-contribution-guidelines` command shown below. If you previously
installed a standalone copy, review and back up any customizations before removing
it manually to avoid loading both copies.

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
