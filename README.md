# How — explain codebase architecture in Codex

A Codex-ready fork of [poteto/how](https://github.com/poteto/how), originally created by Lauren Tan for Cursor. The `how` skill traces a subsystem, feature, or runtime flow and gives an engineer a working mental model backed by source references.

## What it does

- **Explain** (default): explore the implementation and explain the concepts, execution flow, boundaries, relevant files, and gotchas.
- **Critique** (on request): explain first, then assess architectural issues through independent review lenses and summarize the evidence and tradeoffs.

Simple questions run directly in the current agent. Complex questions split into 2–4 exploration angles when the host exposes subagent tools. Critique uses 2–3 critics within the host's concurrency limits. Agents use the host's configured model by default. If delegation is unavailable, the skill performs the passes in the current agent and states that no independent reviews ran. All exploration and critique are read-only.

## Install as a Codex plugin

On a Codex CLI with `codex plugin` support:

```sh
codex plugin marketplace add nishino-tsukasa/how --ref main
codex plugin add how@how-plugins
codex plugin list --marketplace how-plugins
```

In the desktop plugin browser, select the **How Plugins** marketplace and install **How** after adding the source. Start a new chat after installation so it discovers the skill. The exact browser UI depends on the client version; the commands above provide the CLI route.

Invoke the plugin skill with `$how:how` (or select **how:how** from the skill picker):

```text
$how:how How does the auth middleware check permissions?
$how:how Walk me through what happens when a user sends a message.
$how:how Explain how the auth service works, then critique its architecture.
```

For updates:

```sh
codex plugin marketplace upgrade how-plugins
codex plugin add how@how-plugins
```

Check `codex plugin list --marketplace how-plugins` and start a new chat to pick up the updated skill.

## Use as a standalone skill

Copy the complete `skills/how/` directory, including `references/`, into `~/.agents/skills/how/`, or into `.agents/skills/how/` in a target repository. Choose either the standalone skill or the plugin to avoid duplicate discovery. This source repository already links `.agents/skills` to `skills`, so Codex can discover the skill while working in this checkout.

The standalone skill is invoked with `$how`; the plugin installation uses the `how:how` namespace.

## Codex adaptation

- Adds a portable root `plugin.json`, a `.codex-plugin/plugin.json` compatibility manifest, and a repository marketplace at `.agents/plugins/marketplace.json`.
- Replaces Cursor-specific Task parameters and fixed Claude/Composer/GPT model IDs with the host's exposed agent tools and configured model.
- Keeps the original explanation and critique prompts, with host-neutral source reading, bounded concurrency, read-only assignments, and a single-agent fallback.
- Uses distinct critique lenses instead of promising a review across different model vendors.

The `.cursor-plugin/plugin.json` manifest remains for Cursor. Its host is not tested by this adaptation.

## Structure

```text
how/
├── plugin.json
├── .codex-plugin/plugin.json
├── .cursor-plugin/plugin.json
├── .agents/
│   ├── plugins/marketplace.json
│   └── skills -> ../skills
└── skills/how/
    ├── SKILL.md
    └── references/
        ├── explainer-prompt.md
        ├── explorer-prompt.md
        ├── critic-prompt.md
        └── critique-rubric.md
```

The plugin has no MCP servers, external services, or credentials to configure. Subagents are optional; the skill does not change your Codex settings.

Packaging follows the [OpenAI plugin documentation](https://developers.openai.com/plugins/build/plugins). For host settings, see [Codex subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents).

## License

MIT. The original copyright and license are retained in [LICENSE](LICENSE).
