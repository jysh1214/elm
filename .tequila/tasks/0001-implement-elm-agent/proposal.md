# Proposal of *Implement ELM Agent*

## Motivation

When AI produces notes or reports from codebase analysis, the output often contains gaps, jargon, or unclear reasoning that would not survive scrutiny from a non-technical executive. There is no systematic way to stress-test these artifacts before they reach decision-makers.

ELM is a simulated CEO/CTO reviewer agent. It thinks in first principles and applies the Feynman Learning Technique — if something can't be explained simply, it isn't understood well enough. By having ELM interview the AI about its own notes, we surface weak spots and iteratively improve clarity and completeness.

## Summary

- Add an ELM agent that acts as a first-principles reviewer of AI-generated notes and reports
- Implement an **interview loop**: ELM reads notes → asks questions → AI answers and updates notes → repeat until ELM is satisfied or a max iteration limit is reached
- ELM starts with high-level questions and gradually drills into details, mimicking how a real CEO/CTO would probe a technical briefing
- Record the full interview conversation in `.elm/interview/` with a clear, human-readable format:
  ```
  ELM: xxx
  Claude: xxx

  ---

  ELM: xxx
  Claude: xxx

  ---
  ```
- The interview process is user-initiated — users first ask AI to produce notes/reports, then invoke ELM to review them

## Impact

- Affected code: new module/script for the ELM agent (to be created at project root)
- New directory: `.elm/interview/` for persisting interview transcripts
- Integration point: reads notes/reports produced by AI, feeds questions back to AI for answers and note updates
