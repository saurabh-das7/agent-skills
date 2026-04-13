---
name: ad-copy-evaluator
version: 1.0.0
description: >
  Evaluates search ad copy (headline + description) against a structured,
  intent-aware rubric across 5 dimensions. Use when someone provides a
  product description, a search keyword, and an ad copy to evaluate — or
  asks whether an ad is ready to serve, needs revision, or should be rejected.
  Also triggers on: "evaluate this ad", "score this ad copy", "is this ad good",
  "check my search ad", "review this headline and description".
slash_command: /ad-copy-eval
domain: Ad Tech · LLM Evaluation
author: saurabh-das7
source: https://github.com/saurabh-das7/llm-eval-toolkit
---

# Ad Copy Evaluator Skill

This skill evaluates search ad copy against a structured rubric and returns a
dimensional scorecard with a clear verdict. It mirrors the evaluation logic
built into the LLM Eval Toolkit app — same rubric, same weights, same verdicts.

---

## When to Trigger

Trigger this skill when the user provides:
- A search ad headline and description to evaluate
- A product description + keyword + ad copy combination
- A request to compare two ad copies

Also trigger on phrases like:
- "Is this ad ready to serve?"
- "Evaluate this search ad"
- "Score my ad copy"
- "Which ad is better?"
- "Would this pass a quality check?"

Do NOT trigger on general copywriting requests ("write me an ad") — this skill
evaluates, it does not generate.

---

## What You Need From the User

Before evaluating, confirm you have:

1. **Product description** — what the product is, key features, pricing if relevant
2. **Target search keyword** — the exact keyword the ad is targeting
3. **Ad headline** — should be ≤30 characters (flag if over)
4. **Ad description** — should be ≤90 characters (flag if over)

If any of these are missing, ask for them before proceeding. Do not guess.

If the product description is fewer than 5 words or the ad copy contains
placeholder text (e.g. "Insert USP here"), return NOT EVALUABLE and explain
what is missing.

---

## Step 1 — Infer Search Intent

Before scoring, infer the searcher's intent from the keyword:

**Purchase intent** — keyword contains: "buy", "order", "book", "shop", "deal",
"offer", price terms (₹, $, "price"), "online", "get"
Examples: "buy nike running shoes online", "book flights to Goa", "order pizza now"

**Consideration intent** — keyword contains: "best", "compare", "vs", "review",
"top", "which", "alternative", "recommend"
Examples: "best running shoes for marathon", "compare project management tools"

**Awareness intent** — keyword contains: "what is", "how to", "guide", "tips",
"explained", "learn"
Examples: "what is a CRM", "how to improve ad CTR"

**Default:** If the intent is ambiguous, use Consideration.

State the inferred intent clearly before scoring:
`Inferred intent: Purchase / Consideration / Awareness`

---

## Step 2 — Apply the Weight Profile

Use the weight profile that matches the inferred intent:

| Dimension          | Purchase | Consideration | Awareness |
|--------------------|---------|--------------|----------|
| Relevance          | 15%     | 20%          | 25%      |
| Intent Alignment   | 30%     | 20%          | 15%      |
| Differentiation    | 20%     | 30%          | 20%      |
| CTA Strength       | 25%     | 15%          | 10%      |
| Character Efficiency | 10%   | 15%          | 30%      |

---

## Step 3 — Score Each Dimension

Score each dimension 1–5 (integers only). For each score, provide one sentence
of reasoning that references specific words from the ad copy — not generic
commentary.

### Dimension 1 — Relevance
Does the ad accurately reflect the product described?

- **5** — Ad precisely matches the product offer, features, and positioning
- **3** — Ad is on-topic but misses key product details or benefits
- **1** — Ad is misleading, inaccurate, or disconnected from the product

### Dimension 2 — Intent Alignment
Does the CTA match where the searcher is in their decision journey?

- **5** — CTA perfectly matches the inferred keyword intent
- **3** — CTA is present but partially mismatched to intent
- **1** — CTA is completely mismatched to keyword intent, or absent entirely

### Dimension 3 — Differentiation
Is there a specific, compelling reason to click this ad over a competitor?

- **5** — Headline contains a clear, specific USP that competitors cannot trivially claim
- **3** — Some differentiation present but vague or easily replicated
- **1** — Generic — any competitor in this category could run this exact ad

### Dimension 4 — CTA Strength
Is the call to action specific, urgent, and action-oriented?

- **5** — CTA is specific, creates appropriate urgency, matches funnel stage
- **3** — CTA exists but is weak, generic, or only partially appropriate
- **1** — CTA is absent, generic ("Learn more"), or actively wrong for the intent

### Dimension 5 — Character Efficiency
Is every character earning its place?

- **5** — Tight, punchy copy — no filler, strong information density
- **3** — Some filler or wasted characters, but within limits
- **1** — Significant filler/repetition, or copy exceeds platform character limits

**Character limits:**
- Headline: ≤30 characters. If over limit → score 1 on this dimension automatically.
- Description: ≤90 characters. If over limit → score 1 on this dimension automatically.

---

## Step 4 — Calculate Overall Score

Overall score = weighted average of dimension scores using the intent-appropriate
weight profile. Round to one decimal place.

**Downgrade rule:** If ANY single dimension scores 1/5, the overall verdict cannot
be higher than NEEDS REVISION — regardless of the weighted average.

---

## Step 5 — Assign Verdict

| Overall Score | Verdict |
|---------------|---------|
| 4.0 – 5.0 | ✅ READY TO SERVE |
| 2.5 – 3.9 | ⚠️ NEEDS REVISION |
| Below 2.5 | ❌ REJECT |
| Insufficient input | 🟡 NOT EVALUABLE |

---

## Output Format

Return the scorecard in this exact structure:

```
PRODUCT: [one-line product summary]
KEYWORD: [keyword]
INFERRED INTENT: [Purchase / Consideration / Awareness]
WEIGHT PROFILE: [e.g. "Purchase — CTA Strength 25%, Intent Alignment 30%..."]

AD EVALUATED:
Headline: [headline]
Description: [description]

SCORECARD
──────────────────────────────────────────────
Relevance             [X/5]   [one-line reasoning]
Intent Alignment      [X/5]   [one-line reasoning]
Differentiation       [X/5]   [one-line reasoning]
CTA Strength          [X/5]   [one-line reasoning]
Character Efficiency  [X/5]   [one-line reasoning]
──────────────────────────────────────────────
Overall Score         [X.X / 5]

VERDICT: [READY TO SERVE / NEEDS REVISION / REJECT / NOT EVALUABLE]

EVALUATOR NOTE: [One sentence naming the single most important issue or
confirming the primary strength. Must be specific to this ad copy.]
```

---

## Side-by-Side Comparison Mode

If the user provides two ad copies for the same product and keyword, evaluate
both independently using the same weight profile, then add a head-to-head
summary:

```
HEAD-TO-HEAD SUMMARY
──────────────────────────────────────────────
Ad A wins on: [dimension(s)]
Ad B wins on: [dimension(s)]
Overall winner: Ad [A/B] — [one sentence reason]
──────────────────────────────────────────────
```

---

## Example Evaluation

**Input:**
- Product: Nike Running Shoes — engineered for performance. React foam and Air Zoom cushioning. Free delivery over ₹3,000.
- Keyword: "buy nike running shoes online"
- Headline: "Nike Running Shoes – Shop Now" (29 chars)
- Description: "Free delivery over ₹3,000. React foam cushioning for speed. New arrivals in stock. Order today." (93 chars ⚠️ over limit)

**Output:**
```
PRODUCT: Nike Running Shoes — performance footwear with free delivery
KEYWORD: buy nike running shoes online
INFERRED INTENT: Purchase
WEIGHT PROFILE: Purchase — Intent Alignment 30%, CTA Strength 25%, Differentiation 20%, Relevance 15%, Char Efficiency 10%

AD EVALUATED:
Headline: Nike Running Shoes – Shop Now
Description: Free delivery over ₹3,000. React foam cushioning for speed. New arrivals in stock. Order today.

SCORECARD
──────────────────────────────────────────────
Relevance             4/5   Accurately reflects the product and free delivery offer
Intent Alignment      5/5   "Shop Now" CTA perfectly matches purchase intent
Differentiation       4/5   Free delivery threshold is a specific, concrete USP
CTA Strength          5/5   "Shop Now" is direct, urgent, and funnel-appropriate
Character Efficiency  1/5   Description is 93 characters — exceeds the 90-char limit
──────────────────────────────────────────────
Overall Score         3.8 / 5

VERDICT: ⚠️ NEEDS REVISION

EVALUATOR NOTE: Strong intent alignment and CTA, but description exceeds
the 90-character limit by 3 characters — trim "in stock" or "New arrivals"
before serving.
```

---

## Related Resources

- Full app: https://github.com/saurabh-das7/llm-eval-toolkit
- Rubric design rationale: https://github.com/saurabh-das7/pm-tpm-playbooks/blob/main/ai-learning/rubric_based_llm_evaluation_design.md
- System prompt design: https://github.com/saurabh-das7/pm-tpm-playbooks/blob/main/ai-learning/system_prompt_design_for_llm_evaluation.md
