---
name: search-ad-copy-generator
version: 1.0.0
description: >
  Generates search ad copy variants (headline + description) for a given
  product and keyword. Produces three quality variants per combination —
  strong (A), mixed (B), weak (C) — designed to feed directly into the
  ad-copy-evaluator skill. Also triggers on: "generate search ads for",
  "write ad copy for this keyword", "create ad variants for", "give me
  search ad options for", "write a Google ad for".
slash_command: /ad-copy-gen
domain: Ad Tech · Search Advertising · Content Generation
author: saurabh-das7
source: https://github.com/saurabh-das7/llm-eval-toolkit
---

# Search Ad Copy Generator Skill

This skill generates search ad copy variants for a given product and keyword.
It produces three quality tiers — strong, mixed, and weak — following the
same rubric dimensions used by the `ad-copy-evaluator` skill.

Use this skill to generate candidates, then pipe the output into
`/ad-copy-eval` to score and select the best variant before serving.

---

## When to Trigger

Trigger this skill when the user wants to:
- Generate search ad copy for a product and keyword
- Create multiple variants to compare
- Produce test cases for the ad copy evaluator
- Draft a starting point for manual refinement

Also trigger on:
- "Write me a Google search ad for..."
- "Give me ad copy options for this product"
- "Create headline and description variants"
- "Generate ads targeting [keyword]"

Do NOT trigger on:
- Requests to evaluate existing ad copy (use `/ad-copy-eval`)
- Requests for display ads, banner ads, or social media ads — this skill
  is specific to search ad text format (headline + description)
- Requests for long-form copy, landing pages, or email copy

---

## What You Need From the User

**Required:**
1. **Product description** — what the product is, key features, what makes
   it different. At least 2–3 sentences.
2. **Target search keyword** — the exact keyword or phrase being targeted

**Optional (improves output quality):**
3. **Number of variants** — default is 3 (A/B/C quality tiers); user can
   request 1–5 variants
4. **Specific focus** — any dimension to prioritise (e.g. "focus on urgency",
   "emphasise the price", "lead with the USP")

If the product description is fewer than 10 words, ask for more detail
before generating. Vague input produces generic output.

---

## Step 1 — Infer Search Intent

Before generating, infer the searcher's intent from the keyword. This
determines what good ad copy looks like.

**Purchase intent** — "buy", "order", "book", "shop", "price", "deal",
"offer", "get", "online"
→ Lead with price, availability, or delivery. CTA must be direct and urgent.

**Consideration intent** — "best", "compare", "vs", "review", "top",
"alternative", "which"
→ Lead with a specific USP or differentiator. CTA should invite comparison,
not demand purchase.

**Awareness intent** — "what is", "how to", "guide", "tips", "explained"
→ Lead with the value proposition or a curiosity hook. CTA should be soft.

State the inferred intent before generating:
`Inferred intent: Purchase / Consideration / Awareness`

---

## Step 2 — Generate Three Variants

Generate exactly three variants per request unless the user specifies
otherwise. Each variant must:
- Respect platform character limits: headline ≤30 chars, description ≤90 chars
- Show the character count for each field
- Be genuinely differentiated in quality — not three slight variations
  of the same approach

### Variant A — Strong (target: READY TO SERVE)

Design principles:
- Headline: specific USP or concrete benefit in the first 5 words
- CTA matched precisely to the inferred intent
- Description: one concrete benefit + one action signal, no filler
- Every character earning its place
- Differentiated — a competitor cannot run this exact ad

### Variant B — Mixed (target: NEEDS REVISION)

Design principles:
- Intentionally non-obvious flaw — not obviously bad at first glance
- One dimension clearly weak while others are acceptable
- Common weak dimensions for Variant B:
  - CTA mismatched to intent (e.g. "Buy Now" on a consideration keyword)
  - Generic headline that any competitor could use
  - Accurate but uninspiring description with no specific benefit
- The rubric should catch it; gut feel should not

### Variant C — Weak (target: REJECT)

Design principles:
- Multiple dimensions clearly failing
- Common patterns:
  - Wrong-stage CTA (e.g. "Learn More" on a purchase keyword)
  - Zero differentiation — could be any brand in the category
  - Vague, filler-heavy description
  - Awareness-stage language on a purchase query

---

## Character Count Verification

Before returning any variant, verify character counts:
- Count headline characters including spaces and punctuation
- Count description characters including spaces and punctuation
- If either exceeds the limit, revise before returning — never return
  over-limit copy without flagging it

Format: `[X chars]` after each field so the user can verify.

---

## Output Format

```
PRODUCT: [one-line product summary]
KEYWORD: [keyword]
INFERRED INTENT: [Purchase / Consideration / Awareness]

─────────────────────────────────────────────────────
VARIANT A — Strong (target: READY TO SERVE)
─────────────────────────────────────────────────────
Headline:    [copy]                    [X chars]
Description: [copy]                    [X chars]

Why it should score well:
- Relevance: [one sentence]
- Intent Alignment: [one sentence]
- Differentiation: [one sentence — what specific USP is present]
- CTA Strength: [one sentence]
- Character Efficiency: [one sentence]

─────────────────────────────────────────────────────
VARIANT B — Mixed (target: NEEDS REVISION)
─────────────────────────────────────────────────────
Headline:    [copy]                    [X chars]
Description: [copy]                    [X chars]

Primary weakness: [name the specific dimension and exactly why it fails]
Note: This is intentionally non-obvious — the rubric should catch it,
not gut feel.

─────────────────────────────────────────────────────
VARIANT C — Weak (target: REJECT)
─────────────────────────────────────────────────────
Headline:    [copy]                    [X chars]
Description: [copy]                    [X chars]

Primary failures: [list the two or three dimensions that fail and why]

─────────────────────────────────────────────────────
NEXT STEP: Run /ad-copy-eval on any of these variants to get a full
dimensional scorecard and official verdict.
```

---

## Single Variant Mode

If the user asks for just one ad (no variants), generate Variant A quality
only — the strongest possible copy for the given product and keyword.

Output format for single variant:
```
PRODUCT: [summary]
KEYWORD: [keyword]
INFERRED INTENT: [intent]

Headline:    [copy]    [X chars]
Description: [copy]    [X chars]

Key decisions:
- [Why this headline vs alternatives]
- [Why this CTA for this intent]
- [What USP was chosen and why]
```

---

## Quality Standards for Variant A

Variant A should meet these standards before being returned:

**Headline:**
- Contains a specific USP, price signal, or intent match in the first 5 words
- Not a generic category description ("Best Running Shoes")
- CTA embedded naturally if character space allows

**Description:**
- Leads with the most compelling benefit — not a repetition of the headline
- Contains one concrete proof point (price, number, specific feature)
- Ends with a clear action signal matching the intent
- No filler phrases: "great for", "wide range of", "visit our website"

**Overall:**
- A competitor cannot run this ad without it being obviously wrong for
  their brand
- Character efficiency: every word is doing work

---

## Common Generation Mistakes to Avoid

| Mistake | Example | Better |
|---------|---------|--------|
| Generic headline | "Best Nike Shoes Online" | "Nike React Foam – Shop Now" |
| Wrong CTA for intent | "Buy Now" on consideration keyword | "Compare Models" |
| Repeating headline in description | Headline: "Shop Nike", Desc: "Shop Nike shoes online today" | Lead desc with a new benefit |
| Filler opening | "Discover the amazing range of..." | Lead with the USP directly |
| Missing specificity | "Great deals available" | "Free delivery over ₹3,000" |
| Over-limit copy | 32-char headline | Trim to ≤30 before returning |

---

## Example Output

**Input:**
- Product: Trello — visual project management using boards, lists, cards.
  2M+ teams. 100+ integrations. Free to start.
- Keyword: "project management tool for teams"

**Output:**
```
PRODUCT: Trello — visual project management for teams
KEYWORD: project management tool for teams
INFERRED INTENT: Consideration

─────────────────────────────────────────────────────
VARIANT A — Strong (target: READY TO SERVE)
─────────────────────────────────────────────────────
Headline:    Trello for Teams – See How           [26 chars]
Description: Visual boards, 100+ integrations. See why 2M+
             teams choose Trello. Free to start.            [88 chars]

Why it should score well:
- Relevance: Accurately reflects product — boards, integrations, teams
- Intent Alignment: "See How" CTA matches consideration intent precisely
- Differentiation: 2M+ teams is specific social proof; "Free to start"
  removes commitment friction
- CTA Strength: Soft CTA appropriate for comparison stage
- Character Efficiency: No filler — every element earns its place

─────────────────────────────────────────────────────
VARIANT B — Mixed (target: NEEDS REVISION)
─────────────────────────────────────────────────────
Headline:    Trello – Manage Team Projects        [29 chars]
Description: Trello helps teams manage projects with boards,
             lists, and cards to organise work better.      [89 chars]

Primary weakness: Zero differentiation — describes what Trello is
(boards, lists, cards) but not why it beats Asana or Monday.com.
The description is accurate but generic; the rubric catches the
missing USP that gut feel doesn't flag.

─────────────────────────────────────────────────────
VARIANT C — Weak (target: REJECT)
─────────────────────────────────────────────────────
Headline:    Project Management Software          [27 chars]
Description: Looking for a way to manage projects? Trello
             is a tool that can help your team.             [79 chars]

Primary failures:
- Differentiation: Headline doesn't mention Trello — could be any
  product in the category
- Intent Alignment: Description restates the question the user just
  answered by searching
- CTA Strength: No CTA present

─────────────────────────────────────────────────────
NEXT STEP: Run /ad-copy-eval on any of these variants to get a full
dimensional scorecard and official verdict.
```

---

## Integration with ad-copy-evaluator

This skill is designed as the upstream half of a two-step workflow:

```
/ad-copy-gen → generates variants A, B, C
     ↓
/ad-copy-eval → scores each variant across 5 dimensions
     ↓
User selects the best variant to serve
```

The rubric dimensions used to design variants A, B, C are identical to
the dimensions used to evaluate them — ensuring the generator and evaluator
are calibrated against each other.

---

## Related Resources

- Evaluate generated copy: use `/ad-copy-eval` skill
- Full evaluation app: https://github.com/saurabh-das7/llm-eval-toolkit
- Sample ad copy bank (18 examples): https://github.com/saurabh-das7/llm-eval-toolkit/blob/main/docs/02b_sample_ad_copy_bank.md
