---
name: how
description: Explain how a subsystem, feature, or runtime flow works in the current codebase using source evidence. Also use when the user asks to critique that architecture after explaining it.
---

# How

Build a working mental model for an engineer onboarding onto a subsystem. Trace the actual implementation, explain its boundaries and data flow, and cite the files and symbols that support the explanation.

Use **Explain** by default. Add **Critique** only when the user asks for architectural issues, tradeoffs, or improvements.

## Host and delegation

This workflow uses the tools available in the current host. In Codex, use the exposed subagent tools (for example, `spawn_agent` and its wait tools, or the desktop `collaboration` namespace) with their actual schemas. Omit model overrides by default so agents use the host's configured model. If the user requests a model, use it only when the host supports it; report an unavailable requested model instead of silently substituting one.

Delegated exploration and critique are read-only. Put the repository path, question, assigned angle, and this boundary in every agent prompt: inspect source, return findings, make no changes, and do not delegate further. Use a read-only agent configuration when the host offers one; otherwise enforce the boundary through the task instructions. Choose a fresh context when supported and include the relevant prompt template contents so the agent has a self-contained assignment.

Respect the host's available concurrency. Queue remaining angles when slots are limited, wait for every assigned result, and close completed agents when the host supports closing them. If an agent fails, finish that angle yourself or retry within the available tools, and identify any unresolved gap.

If subagent tools are unavailable or delegation is prohibited by higher-priority instructions, perform the same exploration and critique passes yourself. Describe the actual execution accurately: sequential passes in one agent are not independent reviews. Enabling agents or changing host configuration is outside this workflow.

## Explain

### 1. Scope the question

Identify the subsystem, feature, or runtime path the user wants to understand. If the scope is ambiguous, state a reasonable interpretation and explore; ask only when the ambiguity blocks useful work.

- **Simple:** one module, a small utility, or a narrow call path. Explore and explain directly in the current agent.
- **Complex:** multiple services or files, a cross-cutting feature, or a broad architectural overview. Split into 2–4 distinct exploration angles and delegate within the host's limits. For example: data model, runtime orchestration, and rendering or external integrations.

When in doubt, start with the simple path and expand only if the evidence requires it.

### 2. Explore

For complex questions, read [references/explorer-prompt.md](references/explorer-prompt.md) and give each explorer the filled template plus its assigned angle. For simple questions or the single-agent fallback, use the same evidence criteria yourself.

Use the host's source search and file-reading tools. With shell access, start with `rg --files` and `rg` for entry points and symbols. Trace callers, callees, state changes, and boundary inputs/outputs until the relevant trigger-to-result path is accounted for. Read implementations rather than inferring behavior from names. Mark any untraceable link as an open question.

Exploration is complete when each angle provides the path it traced, code references, boundaries, and unresolved questions. Wait for all delegated findings before synthesis.

### 3. Explain and present

Read [references/explainer-prompt.md](references/explainer-prompt.md) for the communication style and output format. The current agent synthesizes the findings; for simple questions, it writes from its own exploration. Resolve conflicting claims by checking the source. Distinguish code evidence, inference, and behavior that would require runtime verification.

Use these sections when they help the reader:

- **Overview:** what the subsystem does and why it exists.
- **Key Concepts:** the types, services, and abstractions needed to follow it.
- **How It Works:** the trigger, execution flow, data transformations, and decision points.
- **Where Things Live:** the files a newcomer should start reading.
- **Gotchas:** non-obvious behavior, sharp edges, and evidence gaps.

Follow the user's language and requested level of detail. Reference concrete files and symbols. Include a diagram only when it makes the flow easier to understand.

## Critique

### 1. Explain first

Complete the Explain flow so the architectural assessment is grounded in the actual implementation.

### 2. Gather critiques

Read [references/critic-prompt.md](references/critic-prompt.md) and [references/critique-rubric.md](references/critique-rubric.md). When delegation is available, launch 2–3 independent critics with distinct primary lenses, such as:

- abstraction fit and boundary discipline;
- data model and state transitions;
- evolution readiness and complexity versus value.

Each critic gets the explanation, relevant file paths, filled prompt template, rubric, and its assigned lens. Critics must verify their claims against source and return concrete evidence and impact. Keep their reports separate until all have returned. Different lenses and fresh contexts provide independent judgments; this does not imply different models were used.

With the single-agent fallback, run the relevant lenses sequentially and disclose that no independent critics ran. An empty critique is valid when the evidence supports the design.

### 3. Lead judgment

Check the evidence, merge overlapping findings, and distinguish intentional tradeoffs from demonstrated problems. Categorize findings:

- **Act on:** architectural problems worth addressing now.
- **Consider:** real concerns with an unclear cost/benefit.
- **Noted:** valid, lower-priority observations.
- **Dismissed:** unsupported claims, missing context, or style preferences.

Present the explanation before the critique verdict. Retain evidence and impact for actionable findings, and report incomplete coverage. This skill produces an explanation and assessment; implement fixes only when the user separately authorizes them.
