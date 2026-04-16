English | [한국어](README.ko.md)

# show-me-the-prd

> **Interview-driven PRD generator for vibe coders — from one sentence to 4 design documents.**

Turn a single idea into a complete product plan. Answer 5–6 structured questions and get four design documents ready to hand off to any AI coding tool.

[Quick Start](#quick-start) • [Why show-me-the-prd](#why-show-me-the-prd) • [How it works](#how-it-works) • [Features](#features) • [Commands](#commands) • [Requirements](#requirements)

---

## Quick Start

### 1. Add the marketplace

```
/plugin marketplace add https://github.com/fivetaku/gptaku_plugins.git
```

### 2. Install the plugin

```
/plugin install show-me-the-prd
```

### 3. Restart Claude Code

The plugin loads on session start — restart once after installing.

### 4. Start planning

```
/show-me-the-prd I want to build a photo organization app
```

Or just say it naturally:

```
Make me a PRD for a task manager
```

---

## Why show-me-the-prd?

- **No planning experience required** — Every question comes with plain-language explanations and tradeoffs, not jargon
- **AI leads, you decide** — AI researches options in real time and presents them as choices; you never have to Google anything
- **Research-backed, not guesswork** — Live web searches power the feature list, stack comparison, and complexity ratings
- **Four documents, not one** — PRD, data model, phase plan, and AI project spec are generated together as a coherent set
- **Production-oriented** — Each phase targets a real deployment with real auth and a real database, not a local mock
- **Existing plans welcome** — Drop in an existing spec; the plugin identifies gaps and fills them in

---

## How it works

```
One-sentence idea
       |
       v
  [ Interview ]  ←── live web research runs between each question
       |
   Turn 1: clarify the idea (1–3 questions)
   Turn 2: pick core features + MVP scope
   Turn 3: confirm data model
   Turn 4: confirm phase breakdown
   Turn 5: choose tech stack + auth method
       |
       v
  [ 4 Documents ]
       |
       +── PRD/01_PRD.md            What you're building and who it's for
       +── PRD/02_DATA_MODEL.md     Core data structure (conceptual ERD)
       +── PRD/03_PHASES.md         Phase plan with start prompts
       +── PRD/04_PROJECT_SPEC.md   AI rules + "never do this" list
       +── PRD/README.md            Navigation guide
```

Every option in the interview includes a plain-language description, pros, cons, and a complexity rating (Simple / Moderate / Complex). If you are unsure, pick the one marked **(recommended)**.

---

## Features

### Interview-based planning

No developer vocabulary required. Instead of "choose your auth strategy," it asks "how should users log in?" with concrete options like "social login (Google/Kakao)" and "email + password."

### Real-time web research

Between each interview turn, the plugin runs live searches to find relevant features, common pitfalls, and current best-practice stacks. What you see in the options is grounded in actual results, not hardcoded suggestions.

### Complexity guidance

Each feature in the feature-selection step is labeled:

| Label | Meaning |
|-------|---------|
| Simple | Works out of the box with most frameworks |
| Moderate | Requires an external service; some cost likely |
| Complex | Recommended to skip in the first version |

### Four output documents

| Document | Contents | When to use |
|----------|----------|-------------|
| `01_PRD.md` | Product goals, user stories, feature list | Share at project start |
| `02_DATA_MODEL.md` | Core entities and relationships | Share when designing the database |
| `03_PHASES.md` | Phase-by-phase plan with start prompts | Reference for build order |
| `04_PROJECT_SPEC.md` | AI behavior rules + "never do this" list | Share with AI on every session |

### Enhancement mode

If you already have a spec or notes, share them before running the command. The plugin reads them, identifies what is missing against the four-document standard, and runs only the questions needed to fill the gaps.

---

## Commands

| Command | Description |
|---------|-------------|
| `/show-me-the-prd [idea]` | Start an interview with your idea inline |
| `/show-me-the-prd` | Start with an interactive opening question |

### Natural-language triggers

The plugin also activates when you say:

- "Make me a PRD"
- "Create a planning document"
- "Show me the PRD"
- "Plan this app for me"
- "Plan [idea] for me"

---

## Requirements

- [Claude Code](https://docs.anthropic.com/claude-code) CLI

### Optional plugins (recommended)

Installing these alongside show-me-the-prd improves research quality:

| Plugin | What it adds |
|--------|-------------|
| `docs-guide` | Official documentation lookups during tech stack research |
| `deep-research` | Deeper market and trend analysis |

The plugin works without them — it falls back to WebSearch automatically.

---

## License

MIT

---

<div align="center">

**One sentence in. Four documents out.**

</div>
