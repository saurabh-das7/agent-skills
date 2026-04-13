# agent-skills

Reusable AI skills for Claude and other agents — packaged as `SKILL.md` files following the [Agent Skills open standard](https://agentskills.io).

Each skill in this repo teaches an AI agent a specific workflow: when to trigger, what to do, and how to structure the output. No code required to install or use.

---

## What is a skill?

A skill is a folder containing a `SKILL.md` file with YAML frontmatter and plain-English instructions. When installed, the agent reads the metadata to decide when the skill is relevant, then loads the full instructions only when needed.

**Skills vs. prompts:** A saved prompt needs to be pasted every session. A skill loads itself automatically when the task matches — or can be called explicitly with a `/slash-command`.

**Skills vs. MCP:** MCP gives agents new tools (APIs, databases, external services). Skills teach agents *how to use those tools* — the workflow, the rubric, the output format.

---

## Skills in this repo

| Skill | Domain | What it does | Status |
|-------|--------|--------------|--------|
| [`ad-copy-evaluator`](./ad-copy-evaluator/) | Ad Tech · LLM Eval | Evaluates search ad copy against a structured, intent-aware rubric across 5 dimensions | ✅ Ready |

More skills planned as I work through problems worth packaging. Each one starts as a repeated workflow — something I find myself re-explaining to an agent more than twice.

---

## How to install

### Option A — Claude.ai (chat interface)

1. Download the skill folder you want
2. Zip it (e.g. `ad-copy-evaluator.zip`)
3. Go to **Settings → Capabilities → Skills → Upload**
4. Upload the zip

Once installed, the skill is available in **any chat on your account** — not project-specific. It triggers automatically when your message matches the skill description, or you can invoke it explicitly with `/skill-name`.

### Option B — Claude Code (terminal)

1. Copy the skill folder into `~/.claude/skills/` for global access across all projects
   ```
   cp -r ad-copy-evaluator ~/.claude/skills/
   ```
2. Or into `.claude/skills/` inside a specific repo for project-only access
3. Start a new Claude Code session — skills load at session start
4. Type `/ad-copy-eval` or describe the task and Claude will trigger it automatically

### Option C — Other agents

These skills follow the [Agent Skills open standard](https://agentskills.io). The same `SKILL.md` format works with Cursor, Gemini CLI, and other tools that have adopted the standard. Check your tool's docs for the install path.

---

## Skill design principles

Every skill in this repo is built around three questions before writing a single line:

1. **What's the repeated workflow?** A skill earns its place by eliminating re-prompting, not by adding complexity.
2. **What's the trigger condition?** If the description is vague, the skill never fires. Specificity is not optional.
3. **What does good output look like?** The skill defines the output format — not just the task.

These principles come from building the [LLM Eval Toolkit](https://github.com/saurabh-das7/llm-eval-toolkit), where rubric design and output consistency were the hardest problems, not the evaluation logic itself.

---

## Related repos

| Repo | What it contains |
|------|-----------------|
| [llm-eval-toolkit](https://github.com/saurabh-das7/llm-eval-toolkit) | Search Ad Copy Evaluator — a Streamlit app that evaluates LLM-generated ad copy end-to-end |
| [pm-tpm-playbooks](https://github.com/saurabh-das7/pm-tpm-playbooks) | Frameworks and playbooks for AI product development and measurement |

The `ad-copy-evaluator` skill in this repo is the packaged workflow version of the evaluation rubric built into the LLM Eval Toolkit app. Same rubric, different surface — one is a deployable product, one is an installable workflow.

---

## About

I'm Saurabh — Senior TPM and Designated PM at Microsoft AI, working on Ads Marketplace and LLM-powered diagnostic tooling. This repo is part of a public AI learning journey: building real things, documenting what works, and packaging reusable workflows for others.

**[linkedin.com/in/saurabhdas7](https://linkedin.com/in/saurabhdas7)**
