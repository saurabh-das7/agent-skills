---
name: issue-categorizer
version: 1.0.0
description: >
  Analyses a set of operational tickets, support issues, or alert descriptions and
  categorises them into a structured taxonomy with confidence scores and reasoning.
  Supports three modes: No suggestions (LLM invents categories), Manual list (user
  defines starting taxonomy), and Auto-suggest (LLM proposes taxonomy from a sample
  for review before full run). Triggers on: "categorise these tickets", "group these
  issues", "what patterns do you see in these tickets", "organise these alerts",
  "give me a taxonomy for these issues", "/issue-categorize".
slash_command: /issue-categorize
author: Saurabh Das
---

# Issue Categorizer Skill

## When to trigger

Trigger this skill when the user:
- Pastes a list of tickets, issues, alerts, or operational log entries
- Asks to group, categorise, or find patterns in operational data
- Wants a structured taxonomy from noisy, vaguely-labelled issue data
- Uses the slash command `/issue-categorize`

Do NOT trigger for:
- Single ticket triage (this skill is for batch analysis, minimum 5 rows)
- Sentiment analysis or NPS categorisation (different problem)
- Code review comments or PR feedback (different domain)

---

## Skill behaviour

### Step 1 — Understand the input

When the user pastes ticket data, first identify:
- How many tickets are there?
- What format are they in? (plain text list, CSV, table, numbered list)
- Do they have existing labels? (if yes, note them — they inform mode selection)
- What domain? (infer from content if not stated)

Ask one question only if needed: **"What kind of data is this?"** — a one-line
description of the operational context. This is passed into every categorisation
prompt and significantly improves accuracy.

If the user has already described the context, skip this question entirely.

---

### Step 2 — Confirm mode

Present three options and let the user choose. Default to **No suggestions** if
they don't specify.

**No suggestions**
The LLM reads all tickets and invents category names from scratch. Best when the
user genuinely doesn't know what patterns to expect.

**Manual list**
The user defines starting categories. The LLM maps tickets to them and can create
additional categories for patterns not covered. Best when the user has an existing
taxonomy or hypothesis.

**Auto-suggest**
The LLM samples ~25% of tickets, proposes a taxonomy, the user reviews and edits
it, then the full categorisation runs. Best for larger sets (15+ tickets) where
the user wants input before committing.

For small sets (<10 tickets), recommend No suggestions — Auto-suggest sampling
is not meaningful.

---

### Step 3 — Run categorisation

**For No suggestions and Manual list:** Categorise all tickets in one pass
(or in batches of 10 if the set is large). For each ticket return:
- `new_category` — the assigned category name
- `confidence` — High / Medium / Low
- `reasoning` — one sentence explaining why

Use accumulated categories: once a category is created, reuse it for subsequent
tickets rather than creating variants of the same concept.

**For Auto-suggest:** First pass: sample 25% of tickets (minimum 5) and propose
5-10 category names. Present them clearly for user review. Wait for approval or
edits. Then run the full categorisation using the confirmed taxonomy.

**Prompt to use internally for each batch:**

```
Context: {context}

{accumulated_categories_section}

Categorise each ticket below. For each ticket:
- Assign exactly one category
- Rate confidence: High (clear fit), Medium (reasonable fit), Low (ambiguous)
- Write one sentence of reasoning

Rules:
- Use an existing category if it fits well
- Create a new category only if the ticket genuinely doesn't fit any existing one
- If a ticket is too ambiguous to categorise reliably, use "Uncategorised"

Return a structured response with one entry per ticket.

Tickets:
{tickets}
```

---

### Step 4 — Present results

After categorisation, present:

**Category distribution** — list each category with count and percentage.
Sort by volume, highest first. Example:
```
Transaction Failures       12 tickets  (34%)
Authentication Issues       8 tickets  (23%)
UI / Display Problems       6 tickets  (17%)
Account Setup               5 tickets  (14%)
Uncategorised               4 tickets  (11%)
```

**Per-ticket breakdown** — table with: Ticket (truncated), Category, Confidence,
Reasoning. Show all rows. Flag Low confidence rows with a ⚠️.

**Results summary** — always include:
- A 2-3 sentence plain-English narrative: what the data shows, what the dominant
  pattern is, what's notable about the distribution
- Auto-merge note: if two categories were collapsed during the run, say so
- Uncategorised analysis: if any tickets landed in Uncategorised, describe the
  themes present in that bucket in 2-3 observations

---

### Step 5 — Offer follow-up actions

After presenting results, offer:

1. **Refine taxonomy** — user can rename categories, merge two categories, or
   split one into two. Re-run affected tickets only.

2. **Multi-tag view** — map each ticket to all applicable themes, not just the
   primary one. Surfaces cross-cutting patterns. Takes a second pass.

3. **Export** — offer to format the full results as a markdown table the user
   can paste into a doc or Notion page.

4. **Dig into a category** — user can ask "show me all tickets in [category]"
   to review the full list for a specific bucket.

---

## Output format

### Category distribution
Present as a simple list with counts and percentages. No chart (Claude is text-based).

### Per-ticket table
| # | Ticket (truncated to 60 chars) | Category | Confidence | Reasoning |
|---|-------------------------------|----------|------------|-----------|

### Results summary
Always write this in plain English, not as a list. Two to three sentences.
Stakeholder-ready — something a PM could paste directly into a Slack message.

---

## Quality rules

**Vocabulary consistency:** Once you name a category, use exactly that name for
all subsequent tickets. Do not use "App Crash" for one ticket and "Application
Crash Issue" for another.

**Confidence calibration:**
- High: ticket clearly belongs to the category with no ambiguity
- Medium: ticket fits the category but has elements that could belong elsewhere
- Low: categorisation is uncertain — the ticket is vague, multi-domain, or
  doesn't fit any category well

**Uncategorised threshold:** If a ticket is genuinely ambiguous and assigning it
to any category would require stretching, mark it Uncategorised. A small
Uncategorised bucket with honest analysis is more useful than force-fitting.

**Merge aggressively during the run, flag uncertainly after:**
- During categorisation: if a new ticket fits an existing category, use it
- After categorisation: if two categories look similar, note it in the summary
  as a potential merge rather than silently collapsing them

---

## Example trigger messages

- "Here are 30 support tickets from our ops team. Can you categorise them?"
- "I have a list of alerts from our monitoring system. What patterns do you see?"
- "Group these issues for me: [list]"
- "I want to understand what's in this ticket backlog: [paste]"
- "/issue-categorize"
- "Categorise these using Manual list mode. My categories are: Payment failure, App crash, Account issue"

---

## Related skills

- `pm-doc-writer` — use after categorisation to document the patterns found as a
  problem statement or incident analysis
- `ad-copy-evaluator` — different domain (ad copy quality) but same rubric-based
  evaluation approach
- `jd-analyzer` — different domain (job descriptions) but same structured
  classification pattern
