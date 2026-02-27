# Subtasks of *Implement ELM Agent*

## Scaffold

- [x] Create `.elm/` directory structure (`.elm/interview/` for transcripts)
- [x] Create the Claude Code skill entry point (`.claude/skills/elm/SKILL.md`) with skill metadata and trigger

## ELM Persona

- [x] Write the ELM system prompt defining the persona: first-principles thinker, Feynman technique, CEO/CTO lens, high-level → detailed questioning pattern
- [x] Include the `[SATISFIED]` marker convention so the interview loop knows when ELM is done
- [x] Include behavioral examples showing the progression from broad to specific questions

## Interview Loop

- [x] Implement the skill logic that reads the user-provided notes/report
- [x] Implement the loop: spawn ELM sub-agent → collect questions → answer questions using codebase + notes → update notes → repeat
- [x] Detect the `[SATISFIED]` marker to terminate the loop
- [x] Handle edge cases: empty notes, ELM satisfied on first round, user interruption

## Transcript Recording

- [x] Save each interview round (ELM question + Claude answer) to the transcript file in `.elm/interview/`
- [x] Use the specified format (`ELM: xxx\nClaude: xxx\n\n---\n`)
- [x] Name transcript files with timestamps (e.g., `2026-02-28T01-49-00.md`)

## Integration

- [x] Register the skill in project-level `.claude/settings.json`
- [x] Add a `CLAUDE.md` entry documenting the `/elm` command for discoverability

## Final Verdict

- [x] ELM produces a final summary/verdict at the end of the interview when satisfied

## Notes

- The ELM persona prompt is the most critical artifact — it determines the quality of the entire review process.
- The interview loop relies on the Task tool to spawn sub-agents, keeping context fresh per round.
- No fixed iteration cap — ELM uses its own judgment to converge. Users can invoke `/elm` again for deeper review.
