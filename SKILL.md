---
name: elm
description: ELM is a CEO/CTO reviewer agent that interviews AI about its notes and reports using first-principles thinking. Use this to stress-test notes before they reach decision-makers.
license: MIT
---

# ELM — Executive Learning Machine

ELM is a simulated CEO/CTO who reviews notes and reports through an interview process. ELM thinks in first principles and uses the Feynman Learning Technique to surface gaps, jargon, and unclear reasoning.

## How It Works

When the user says `ELM review {notes}`, you will run an interview loop:

1. ELM reads the notes and asks questions
2. You (AI) answer the questions using the codebase and notes as context, and update the notes
3. Repeat until ELM is satisfied

The full conversation is recorded as a transcript.

## Workflow

When this skill is invoked, follow these steps precisely:

### Step 1: Identify the Notes

- The user may provide a file path as an argument (e.g., `ELM review path/to/notes.md`)
- If no argument is provided, ask the user which notes or report to review
- Read the notes file using the Read tool
- If the file is empty or does not exist, inform the user and stop

### Step 2: Create Transcript File

- Derive the note name from the notes file path: take the filename without extension, in kebab-case (e.g., `path/to/My Notes.md` → `my-notes`)
- Generate a timestamp: `YYYY-MM-DDTHH-MM-SS`
- Create the transcript file at `.elm/interview/{note-name}-{timestamp}.md`
- Ensure the `.elm/interview/` directory exists (create it if needed using Bash `mkdir -p`)

### Step 3: Run the Interview Loop

Repeat the following until ELM outputs `[SATISFIED]`:

**3a. Spawn ELM sub-agent**

Use the Task tool to spawn a sub-agent with `subagent_type: "general-purpose"`. The prompt must include:
- The full ELM persona from [elm-persona.md](./references/elm-persona.md)
- The current contents of the notes being reviewed
- The interview transcript so far (so ELM has context of previous rounds)
- Clear instruction to output questions in the format `ELM: ...` and to output `[SATISFIED]` when done

**3b. Process ELM's response**

- If ELM's response contains `[SATISFIED]`:
  - Append ELM's final message (including the verdict) to the transcript
  - Write the final transcript to the file
  - Update the notes file with any final improvements
  - Stop the loop and show the user a summary
- If ELM asks questions:
  - Read the relevant parts of the codebase to formulate thorough answers
  - Write your answers, keeping them clear and jargon-free (remember, ELM is a CEO/CTO)
  - Update the notes file to incorporate improvements surfaced by ELM's questions
  - Append the round to the transcript

**3c. Append to transcript**

Each round in the transcript follows this format:

```
ELM: [ELM's questions]

AI: [Your answers]

---

```

### Step 4: Finalize

After the loop ends:
- Ensure the transcript file is fully written to `.elm/interview/{note-name}-{timestamp}.md`
- Ensure the notes file has been updated with all improvements
- Show the user:
  - How many interview rounds occurred
  - The path to the transcript file
  - ELM's final verdict (from the `[SATISFIED]` response)

## Disciplines

- Never fabricate answers. If you don't know something, say so and investigate the codebase.
- ELM is a CEO/CTO, not an engineer — but he is smart and will understand details when explained clearly. Do not dumb things down. Instead, explain technical details thoroughly using clear language and first-principles reasoning. If ELM asks about a technical concept, give a real explanation, not a hand-wavy summary.
- Each round, re-read the notes to ensure your answers reflect the latest state.
- The transcript is the source of truth for the interview — always keep it up to date.
