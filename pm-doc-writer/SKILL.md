---
name: pm-doc-writer
version: 1.0.0
description: >
  Writes PM-quality documentation for AI products and solo builds following
  a structured stage-by-stage framework. Use when someone wants to document
  a product or project — problem statement, PRD, UX flows, tech stack
  decisions, risk register, roadmap, build log, or retrospective. Also
  triggers on: "write a problem statement for", "help me create a PRD",
  "document this project like a PM", "write the requirements for",
  "create product docs for my project".
slash_command: /pm-doc
domain: Product Management · AI Products · Solo Builds
author: saurabh-das7
source: https://github.com/saurabh-das7/pm-tpm-playbooks
---

# PM Doc Writer Skill

This skill produces PM-quality documentation for AI products and solo builds,
following the stage-by-stage framework developed across the LLM Eval Toolkit
project. It covers all eight documentation stages — from problem statement
through retrospective — with the correct structure, voice, and depth for each.

---

## When to Trigger

Trigger this skill when the user wants to:
- Write a problem statement for a product or project
- Create a PRD (Product Requirements Document)
- Document UX flows or wireframe decisions
- Write a tech stack decision log
- Create a risk register or cost plan
- Build a project roadmap
- Start or update a build log
- Write a launch checklist or retrospective

Also trigger on:
- "Document this like a PM"
- "Help me write the docs for my project"
- "What should my PRD include?"
- "How do I frame this problem?"

Do NOT trigger on general writing requests unrelated to product documentation,
or on requests to write code, marketing copy, or business strategy.

---

## Core Principles — Apply to Every Document

Before writing anything, internalise these principles:

**1. Document decisions, not just outcomes.**
Every document should capture why, not just what. "We chose Streamlit" is
weak. "We chose Streamlit because it's Python-native, deploys with one
command, and the tab structure maps directly to our three-flow design" is
what a PM doc looks like.

**2. Two audiences simultaneously.**
Primary: future self (picks this up after two weeks away). Secondary: anyone
who lands on the GitHub repo. Write with the authenticity of the first and
the clarity of the second.

**3. Non-goals have rationale.**
Any out-of-scope item must be accompanied by a reason. "We are not building
X because Y" — never just a list of exclusions.

**4. Voice: first-person professional.**
Not casual ("I just figured out..."), not dry corporate ("The system shall...").
Natural, specific, confident. "I chose X over Y because Z" is the right register.

**5. Specificity over completeness.**
A problem statement with one specific user archetype is stronger than one
with five generic personas. A PRD with three measurable success metrics is
stronger than one with ten vague ones.

---

## The Eight Documentation Stages

### Stage 1 — Problem Statement

**Purpose:** Answer "why does this exist and who is it for?"

**Required sections:**
- The problem in concrete, specific terms (not "LLMs produce low quality output"
  but the specific failure mode that causes real harm)
- Who feels this problem acutely — one user archetype, specific enough to make
  product decisions against
- The cost of the problem — money wasted, time lost, decisions delayed
- Why now — what changed recently that makes this more urgent

**Quality checks:**
- Does the problem statement contain the word "tool", "app", or "platform"?
  If yes, you've drifted into solution space — rewrite.
- Is the user archetype specific enough? "Marketers" is not specific.
  "Performance marketers at growth-stage startups who use LLMs to generate
  ad copy at scale" is specific.
- Is the cost section concrete? "Inefficiency" is not concrete.
  "₹50,000/month in wasted ad spend" is concrete.

---

### Stage 2a — PRD (Product Requirements Document)

**Purpose:** The contract between the problem and the solution.

**Required sections:**
- Product overview (one sentence: the core job to be done)
- Primary user (one archetype)
- Goals — what v1 accomplishes
- Non-goals — what v1 explicitly does not do, with rationale for each
- Feature specification — written as user flows, not engineering tasks
- Evaluation rubric or scoring design (for AI products)
- Success metrics — specific and measurable
- Design principles — values that govern trade-off decisions
- Open questions — decisions not yet made, with resolution timelines

**Quality checks:**
- Are there non-goals with rationale? A PRD with no non-goals is a feature list.
- Are success metrics measurable? "Users find it useful" is not measurable.
  "≥75% agreement with expert reviewer verdict" is measurable.
- Are there open questions? A PRD with no open questions is either very
  late-stage or dishonest.

---

### Stage 2b — Sample / Test Data Bank

**Purpose:** For AI evaluation or demo tools — curated inputs that power
the experience and anchor the rubric.

**Required elements:**
- Samples across multiple scenarios (at least 3 products/contexts)
- Deliberate quality variation per scenario (strong / mixed / weak)
- Expected output for each sample
- Rationale connecting each sample to specific rubric dimensions
- Contribution guide — format for adding new samples

**Key principle:** Variant B (mixed quality) must be non-obvious. If gut feel
catches it without the rubric, it's not a useful test case.

---

### Stage 3 — UX Flow and Wireframe

**Purpose:** Define how users move through the product.

**Required sections:**
- Interface structure overview
- One flow per major user journey — written as numbered steps
- Zero-friction entry point — how does a first-time user experience the full
  output without prior knowledge?
- Edge case handling — what happens on empty input, invalid format, ambiguous data
- Design decisions with rationale
- Wireframe descriptions or ASCII diagrams of key screens

**Quality checks:**
- Does every flow have a zero-friction path? If a first-time user must type
  something to see output, the UX is not zero-friction.
- Are edge cases documented? Empty inputs, over-limit content, invalid formats.

---

### Stage 4 — Tech Stack Decisions

**Purpose:** Record what was built with and why.

**Required format per decision:**
```
## Decision N — [What the decision is about]
**The question:** [One sentence]

### Options evaluated
[Each option with honest pros/cons]

### Decision: [Chosen option]
[Rationale table: Factor | Rationale]

**Why not [alternative]:** [One sentence per alternative rejected]
```

**Required decisions to cover (minimum):**
- UI/Frontend framework
- AI/LLM evaluation engine
- Hosting/deployment
- Python environment and dependencies
- Version control workflow
- Data storage (if applicable)

**Quality check:** Does every decision have a business/product rationale,
not just a technical one? "Cheaper" is not a rationale. "Fits the ₹0 API
budget constraint while providing sufficient reasoning quality for rubric
scoring" is a rationale.

---

### Stage 5 — Risk and Cost Plan

**Purpose:** Name what could go wrong and what it costs to run.

**Risk register format per risk:**
```
### R[N] — [Risk name]
**Category:** [API / Product / Execution / Security]
**Description:** [What could happen]
**Probability:** Low / Medium / High
**Impact:** Low / Medium / High
**Mitigation:** [What prevents or reduces this]
**Escalation trigger:** [What event means this risk has materialised]
```

**Cost plan required sections:**
- Cost philosophy (what is the target — ₹0, <₹500/month, etc.)
- Cost breakdown table (every line item, even ₹0 ones)
- Cost scenarios (expected / degraded / worst case) with calculations
- Spend controls (what prevents runaway costs)

**Quality check:** Are risks specific? "API issues" is not a risk.
"Google reduces Gemini free tier daily limit below 100 req/day" is a risk.

---

### Stage 6 — Roadmap

**Purpose:** The path from zero to launched.

**Required sections:**
- Constraints (time budget, skill level, solo/team, definition of done)
- Milestone plan — outcomes not activities, with "done when" criteria
- Timeline summary table
- Critical path — the minimum dependency chain to launch
- Post-MVP backlog — deferred features in priority order

**Milestone format:**
```
### Milestone N — [Name]
**Target: [Week X, Days Y-Z] | ~[N] hours**

[One paragraph describing what this milestone produces]

Deliverables:
- [Specific, verifiable output]
- [...]

**Done when:** [Single unambiguous completion criterion]
```

---

### Stage 7 — Build Log

**Purpose:** A running journal of the build — honest, not polished.

**Entry format:**
```
### Entry [N] — [Short title]
**Date:** [Month Year]
**Milestone:** [M0–M6]
**Hours this session:** [X hours]

**What was accomplished:**
**What was harder than expected:**
**What changed from the plan:**
**What's next:**
```

**Key principle:** The build log is not a highlight reel. Entries must include
what was harder than expected and what changed from the plan. A log that only
records wins is a press release.

---

### Stage 8 — Launch and Retrospective

**Purpose:** Quality gate before launch + honest reflection after.

**Launch checklist structure:**
- Functional verification (one checkbox per feature, per tab/flow)
- Sample/test data validation (every sample must return expected output)
- Consistency validation (same input → same output across multiple runs)
- Edge case validation
- Security and configuration checks
- Documentation and repo checks
- Final smoke test sequence (exact steps, must all pass before sharing URL)

**Retrospective sections (written post-launch):**
- What worked well
- What was harder than expected
- What would be done differently (specific, not generic)
- Quality assessment (re-run sample validation after one week live)
- Next bets (prioritised from post-MVP backlog based on what was learned)
- What this project taught me (one paragraph, honest)

---

## How to Use This Skill

**If the user wants a specific document:**
Ask for the project context needed for that stage, then write it following
the stage specification above.

**If the user wants to document a whole project:**
Ask for:
1. What the product does (one sentence)
2. Who it's for (one user archetype)
3. What problem it solves
4. Key constraints (cost, time, skill level, deployment target)

Then work through the stages in order, confirming scope before writing each.

**If the user asks "what should I document?":**
Recommend starting with Stage 1 (Problem Statement) and Stage 2a (PRD)
before any other stage. These two documents force the most important
decisions and make every subsequent stage faster to write.

---

## Voice and Formatting Standards

- **First-person professional:** "I chose X because Y" not "The system shall X"
- **No bullet points for prose sections** — write in sentences and paragraphs
- **Tables for comparisons** — decisions, options, trade-offs
- **Checklists for verification steps** — launch checklist, edge cases
- **No hedging** — if a decision was made, state it directly
- **Rationale over description** — explain why, not just what

---

## Related Resources

- Full documentation example: https://github.com/saurabh-das7/llm-eval-toolkit/tree/main/docs
- PM documentation guide: https://github.com/saurabh-das7/pm-tpm-playbooks/blob/main/ai-learning/pm_documentation_for_solo_ai_builds.md
- Rubric design guide: https://github.com/saurabh-das7/pm-tpm-playbooks/blob/main/ai-learning/rubric_based_llm_evaluation_design.md
