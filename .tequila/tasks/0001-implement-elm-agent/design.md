# Design of *Implement ELM Agent*

## Context

ELM is a simulated CEO/CTO reviewer agent built as a Claude Code skill. Users first ask AI to analyze a codebase and produce notes or reports. Then they invoke ELM to review those artifacts through an interview loop. The goal is to surface gaps, jargon, and unclear reasoning so the final output is crisp enough for a non-technical executive.

Key stakeholders: the user (who produces notes and triggers ELM), and ELM itself (the AI persona that reviews).

## Goals / Non-Goals

- Goals:
  - Implement ELM as a Claude Code skill invoked via `/elm`
  - Define the ELM persona: first-principles thinker, Feynman technique, high-level → detailed questioning
  - Implement the interview loop where ELM asks and AI answers, continuing until ELM is satisfied
  - Persist the full interview transcript to `.elm/interview/`
  - Keep the architecture simple — a skill prompt + a transcript recorder

- Non-goals:
  - ELM does not modify the codebase directly
  - ELM does not replace human review — it supplements it
  - No fixed iteration cap — ELM uses first-principles judgment to decide when to stop; users can always ask for deeper review manually
  - No web UI or external service integration

## Decisions

- **Skill location**: The skill will live in the project repo at `.elm/` (skill definition, prompts, and interview outputs all co-located). The skill will be registered in the user's Claude Code settings or project-level `.claude/settings.json`.
  - *Alternative*: Put it under `~/.claude/skills/elm/`. Rejected because this is a project-specific agent, not a global tool.

- **Interview loop mechanism**: The skill prompt instructs Claude to alternate between two roles — ELM (questioner) and Claude (answerer) — within a single conversation turn by using the Task tool to spawn an ELM sub-agent. The sub-agent reads the notes, produces questions, and returns them. The main agent answers and updates notes. This repeats until the sub-agent returns no further questions.
  - *Alternative*: Manual back-and-forth where the user copies questions/answers. Rejected because it defeats the automation purpose.

- **Transcript format**: Each interview round is separated by `---`. Each speaker is prefixed with `ELM:` or `Claude:`. Transcripts are saved as timestamped markdown files in `.elm/interview/` (e.g., `2026-02-28T01-49-00.md`).
  - *Alternative*: JSON format. Rejected because human readability is a core requirement.

- **ELM stopping condition**: ELM declares it is satisfied when its first-principles review finds no further gaps. This is encoded in the ELM prompt — ELM must output a specific marker (e.g., `[SATISFIED]`) when done. The loop checks for this marker.

## Risks / Trade-Offs

- **Context window pressure**: Long notes + multiple interview rounds may approach context limits. Mitigation: the interview loop runs via sub-agents (Task tool), which get fresh context per round. Only the transcript accumulates.
- **ELM quality depends on prompt engineering**: The ELM persona prompt is the critical artifact. If poorly written, ELM asks shallow or repetitive questions. Mitigation: invest in the prompt with clear behavioral instructions and examples.
- **No hard iteration cap**: ELM could theoretically loop many times on complex notes. Mitigation: the ELM prompt includes guidance to converge (start broad, narrow down, stop when clear). Users can also interrupt.

## Open Questions

- Should ELM also produce a final summary/verdict at the end of the interview (e.g., "These notes are ready" or "These notes still have gaps in X, Y, Z")?
