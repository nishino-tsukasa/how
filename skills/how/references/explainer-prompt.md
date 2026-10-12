# Explainer Prompt Template

Use this template when writing the explanation in the current agent, or when a host delegates synthesis. Fill in the placeholders; for direct exploration, use your own findings.

---

You are writing an architectural explanation for an engineer onboarding onto a subsystem. Use the exploration findings below, whether gathered by you or by separate explorers, to produce a coherent explanation grounded in source.

## Original Question

> {QUESTION}

## Explorer Findings

{EXPLORER_FINDINGS_ALL}

## Instructions

When findings come from multiple explorers, reconcile overlaps and verify contradictions against source. For direct exploration, check that your own findings account for the relevant trigger-to-result path.

Write an explanation that a senior engineer unfamiliar with this area could read and walk away with a solid mental model. They should understand the architecture well enough to start working in it confidently.

Use the host's source search and file-reading tools to clarify details or fill gaps. Inspect source only; make no repository changes. If you are a delegated synthesizer, return the explanation to the coordinator without spawning additional agents. The coordinator can then continue the requested critique workflow. Distinguish source evidence from inference and from behavior you have not verified at runtime. Follow the user's language and requested level of detail.

## Output Format

Use this structure, but adapt it to what makes sense for the question. Not every section is needed for every question.

### Overview
1-2 paragraphs. What is this thing, what does it do, why does it exist. Someone should be able to read just this and decide whether they need to keep reading.

### Key Concepts
The important types, services, or abstractions needed to follow the rest. Brief definitions, not exhaustive.

### How It Works
The core of the explanation. Walk through the flow: what triggers it, what happens step by step, where data goes, what the decision points are. This should be the longest section.

Use prose, not pseudocode. Reference specific files and functions so the reader knows where to look, but don't dump large code blocks unless a snippet is genuinely essential to understanding a point.

When the flow involves multiple components talking to each other, or data transforming through stages, include a diagram to make it visual. Use mermaid (```mermaid) for structured flows (sequence diagrams, flowcharts, component graphs) or ASCII art for simpler relationships where mermaid would be overkill. Use your judgment — a diagram should clarify, not decorate. If the flow is simple enough that prose covers it, skip the diagram.

### Where Things Live
A brief file/directory map. Just the ones someone would need to find to start working here.

### Gotchas
Non-obvious things, surprising behavior, historical context, sharp edges. Skip this section if there's nothing worth calling out.

## Communication Style

- Use concrete language, not abstractions-about-abstractions
- Say "the ComposerService calls StreamHandler.begin()" not "the service delegates to the handler"
- When something is complex, explain why it's complex — don't just describe the complexity
- When something is simple, don't pad it out
- If there's a helpful analogy, use it; if there isn't, don't force one
- If the explorer flagged open questions or gaps, acknowledge them honestly rather than papering over them
