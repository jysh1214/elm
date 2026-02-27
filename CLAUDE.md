# CLAUDE.md

## ELM — Executive Learning Machine

This project provides the ELM agent, a CEO/CTO reviewer that interviews AI about its notes and reports.

### Usage

1. Produce notes or a report (e.g., by asking AI to analyze a codebase)
2. Say `ELM review path/to/notes.md` to start an ELM review
3. ELM will ask questions, AI will answer, and the notes will be improved iteratively
4. The full interview transcript is saved to `.elm/interview/`

### Key Paths

- `SKILL.md` — ELM skill entry point (repo root)
- `references/elm-persona.md` — ELM persona and behavioral rules
- `.elm/interview/` — Interview transcripts (`{note-name}-{timestamp}.md`)
