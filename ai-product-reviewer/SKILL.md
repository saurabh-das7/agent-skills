---
name: ai-product-reviewer
version: 1.0.0
description: >
  Reviews and evaluates AI product ideas or existing AI tools across six
  dimensions: problem clarity, user specificity, evaluation framework,
  UX design, cost model, and deployment path. Use when someone shares an
  AI product idea and wants honest assessment before investing build time.
  Also triggers on: "is this a good project to build", "review my AI product
  idea", "evaluate this tool concept", "what do you think of building X",
  "should I build this", "critique my product idea".
slash_command: /ai-review
domain: Product Management · AI Products
author: saurabh-das7
source: https://github.com/saurabh-das7/pm-tpm-playbooks
---

# AI Product Reviewer Skill

This skill evaluates AI product ideas and existing AI tools across the six
dimensions that determine whether an AI product is worth building and whether
it will succeed. It produces a structured scorecard with honest assessments
and specific, actionable recommendations.

---

## When to Trigger

Trigger this skill when the user:
- Shares an AI product idea and asks for feedback
- Wants to know if a project is worth building
- Asks for critique of an existing AI tool concept
- Wants to compare two project ideas and choose one
- Asks "what's missing from my product thinking?"

Also trigger on:
- "Should I build this?"
- "What do you think of my idea?"
- "Is this a good use of LLMs?"
- "What would make this better?"

Do NOT trigger on:
- Requests to build or code a product (use appropriate coding workflow)
- Requests to write documentation (use `/pm-doc` skill)
- General questions about AI tools that aren't a specific product idea

---

## What You Need From the User

Before reviewing, you need at minimum:
1. **What the product does** — one sentence description
2. **Who it's for** — who uses it and why
3. **What problem it solves** — the specific pain being addressed

Optionally also gather:
- How they plan to build it (tech stack, LLM provider)
- What the demo experience looks like
- What similar tools exist

If the user hasn't provided enough to review meaningfully, ask for the minimum
three items before proceeding.

---

## The Six Review Dimensions

Score each dimension 1–5 and provide specific, honest feedback. Do not hedge.
If something is weak, name it clearly. A diplomatic review that misses real
problems is worse than no review.

---

### Dimension 1 — Problem Clarity
*Is the problem specific, concrete, and real?*

**5** — The problem is precisely defined: specific failure mode, specific cost,
specific user who experiences it. A stranger reading the problem statement
immediately understands why this matters.

**3** — The problem is identified but described in generic terms. "Teams waste
time" or "LLMs produce low quality output" — real problems but not specific
enough to make product decisions against.

**1** — The problem is vague, solution-first ("I want to build a chatbot for X"),
or doesn't exist — the tool is looking for a problem.

**Questions to probe:**
- What specifically breaks when this problem goes unsolved?
- Who loses money, time, or trust — and how much?
- Why is this problem happening now, not two years ago?

---

### Dimension 2 — User Specificity
*Is the target user concrete enough to make product decisions against?*

**5** — One specific user archetype with a named role, context, and daily reality.
"A performance marketer at a growth-stage startup who generates 50+ ad variants
per week using LLMs and QAs them in Google Docs" is specific.

**3** — A real user group but defined too broadly. "Marketers", "developers",
"small business owners" — too many people, too many different needs.

**1** — "Anyone who uses LLMs" or "teams" — not a user, a universe. Cannot
make a single product decision against this.

**Questions to probe:**
- What is this person's job title?
- What does their current (broken) workflow look like today?
- What metric are they measured on that this tool affects?

---

### Dimension 3 — Evaluation Framework
*Does the product have a clear, measurable definition of quality?*

**5** — The product has a defined rubric: specific dimensions, scoring scale,
verdict logic, and a way to validate that the tool's output matches expert
human judgment. The team knows what "good" looks like before building.

**3** — Quality is described informally ("accurate", "useful", "relevant") but
not operationalised. No rubric, no scoring, no validation plan.

**1** — No quality definition at all. "The AI will evaluate it" — but against
what standard? How will you know if it's working?

**Questions to probe:**
- What dimensions does quality have for this specific use case?
- How will you validate that the tool agrees with an expert human?
- What score threshold means "good enough to ship"?

**Note:** This dimension applies specifically to AI evaluation and generation
tools. For tools where quality is binary (it works / it doesn't), score 3
by default and note that this dimension is less relevant.

---

### Dimension 4 — UX Design
*Can a first-time user experience the full value without friction?*

**5** — Zero-friction entry point: first-time user sees meaningful output
within seconds without typing, uploading, or configuring anything. Samples
pre-loaded, linked dropdowns, live previews, single-click demo path.

**3** — Demo requires some setup but is achievable in under a minute. User
needs to paste something or make a selection before seeing output.

**1** — Demo requires significant user effort: uploading files, configuring
settings, entering API keys, or understanding the tool before experiencing it.
Most first-time visitors will abandon before reaching the output.

**Questions to probe:**
- What is the absolute minimum a first-time user must do to see real output?
- Is there a "load sample" or pre-populated demo path?
- What does a recruiter or hiring manager experience in the first 30 seconds?

---

### Dimension 5 — Cost Model
*Is the running cost sustainable for a solo build?*

**5** — Total monthly API and hosting cost is ₹0 or under ₹200 at expected
volume. Free tier options cover the use case. Paid fallback is documented
and affordable. No risk of unexpected charges.

**3** — Cost is manageable but requires active monitoring. API calls at
expected volume cost ₹200–₹1,000/month. Risk of cost spikes if traffic
increases.

**1** — Cost model is undefined, relies on paid tiers without cost analysis,
or has no spend controls. Solo builder could face unexpected charges.

**Questions to probe:**
- What API provider and tier is planned?
- What is the estimated cost per evaluation/request?
- What is the monthly cost at expected volume?
- What happens if traffic spikes unexpectedly?

**Free tier options to recommend (as of April 2026):**
- Google Gemini 2.5 Flash-Lite: 1,000 req/day free, no credit card
- Groq: 14,400 req/day free on open-source models
- Streamlit Community Cloud: free hosting for public apps

---

### Dimension 6 — Deployment Path
*Can this ship to a public URL without significant infrastructure work?*

**5** — Clear, tested deployment path to a persistent public URL. Free hosting
option identified. App runs without user needing to install anything. GitHub
integration covers continuous deployment.

**3** — Deployment is possible but requires non-trivial configuration (Docker,
environment variables, server setup). More than one hour of infrastructure work.

**1** — No deployment plan, or deployment requires paid infrastructure, complex
setup, or means the app only runs locally. No public URL = no demo = no portfolio
signal.

**Questions to probe:**
- Where will the app be hosted?
- Can someone visit a URL and use it without installing anything?
- What is the deployment workflow after each code change?

---

## Scoring and Verdict

Calculate a simple average of the six dimension scores.

| Average | Verdict |
|---------|---------|
| 4.0–5.0 | ✅ BUILD IT — strong concept, clear path |
| 3.0–3.9 | ⚠️ BUILD WITH CHANGES — good core, specific gaps to address |
| 2.0–2.9 | ⚠️ RETHINK — fundamental issues that will cost you later |
| Below 2.0 | ❌ PAUSE — not ready to build, too many unknowns |

---

## Output Format

```
PRODUCT IDEA: [one-line summary of what was reviewed]

REVIEW SCORECARD
──────────────────────────────────────────────────────
Problem Clarity         [X/5]   [one-line honest assessment]
User Specificity        [X/5]   [one-line honest assessment]
Evaluation Framework    [X/5]   [one-line honest assessment]
UX Design               [X/5]   [one-line honest assessment]
Cost Model              [X/5]   [one-line honest assessment]
Deployment Path         [X/5]   [one-line honest assessment]
──────────────────────────────────────────────────────
Average Score           [X.X / 5]

VERDICT: [BUILD IT / BUILD WITH CHANGES / RETHINK / PAUSE]

STRONGEST DIMENSION: [name] — [one sentence on what's working]

MOST CRITICAL GAP: [name] — [one sentence on what needs fixing first]

SPECIFIC RECOMMENDATIONS:
1. [Most important thing to address before building]
2. [Second most important]
3. [Third most important]
```

---

## Comparison Mode

If the user presents two project ideas and wants to choose between them,
score both independently and add a comparison summary:

```
COMPARISON SUMMARY
──────────────────────────────────────────────────────
Project A average: [X.X] — [one-line]
Project B average: [X.X] — [one-line]

Recommended: Project [A/B]

Why: [Two sentences. Be direct. If one is clearly stronger, say so.
If it depends on the user's situation, specify exactly what it depends on.]
──────────────────────────────────────────────────────
```

---

## Honest Assessment Standards

This skill should not produce diplomatic reviews that avoid naming real
problems. If a product idea scores 2/5 on Problem Clarity, say so directly
and explain why. A review that inflates scores to avoid uncomfortable
feedback is not useful — it wastes the builder's time and money.

Specifically:
- Name the weakest dimension explicitly, not just the strongest
- Give specific recommendations, not generic ones ("clarify your user" is
  not specific; "define whether your primary user is the marketer generating
  the ads or the manager reviewing them — these are different products" is)
- If the idea is not ready to build, say so — with the specific reason

---

## Related Resources

- PM documentation framework: https://github.com/saurabh-das7/pm-tpm-playbooks/blob/main/ai-learning/pm_documentation_for_solo_ai_builds.md
- LLM API selection for solo builds: https://github.com/saurabh-das7/pm-tpm-playbooks/blob/main/ai-learning/llm_api_selection_for_solo_builds.md
- Zero-friction UX patterns: https://github.com/saurabh-das7/pm-tpm-playbooks/blob/main/ai-learning/zero_friction_ux_for_ai_tools.md
