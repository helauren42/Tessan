# Tessan Telemedicine MVP

## What this is

A single, self-contained prompt designed to instruct an AI agent to produce — in one execution — a complete technical and product proposal for a telemedicine mobile MVP. The deliverable is the prompt, not the application.

```
tessan_prompt.md
```

## How to use

Paste the full contents into an AI agent and let it run, preferably with "thinking" option or other options for higher quality outputs activated

## Key design decisions

**Structure** — vision -> functional specs -> architecture -> methods -> modeling. Each section builds on the last rather than existing independently. Markdown usage helps the agent clearly identify the different sections.

**Precision enforcement** — every technology choice must be named and justified. The prompt explicitly bans phrases like "ensure proper input validation" and shows the model exactly what acceptable specificity looks like.

**Assumptions are made visible** — the `[HYPOTHESIS]` tagging system forces silent assumptions into the open. Section 7 collects them into a register with confidence levels and validation paths.

**Minimum counts force depth** — minimum requirements have been defined to push the model past the obvious cases into real operational complexity.

**Communication style** - The tone of the AI is defined to be direct to limit speech that does not directly contribute to the task, repetition is also being suppressed.
