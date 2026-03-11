# grounded-coding

Grounded Agentic Coding is a docs-first coordinator skill for AI coding agents. It teaches the agent to route each task to the right primary source before making non-trivial code changes, especially when working with frameworks, SDKs, cloud platforms, CI/CD tooling, and AWS CDK surfaces that can drift over time.

This is an opinionated starter skill built around the stacks I work in most often, especially AWS, Azure, React, and LLM APIs. The method is portable, but the source list is intentionally curated rather than universal.

It works best with the AWS MCP server, Microsoft Learn MCP, and the OpenAI Docs MCP server, then falls back to official documentation URLs for the rest of the covered stack.

Background article:
https://medium.com/@billcockerill/how-i-made-my-coding-agent-even-more-of-an-expert-grounded-agentic-coding-b8f79f834fea

## Why Docs-First Matters

Agents are fast, but speed is not the same thing as grounding. Official documentation reduces stale assumptions, hallucinated APIs, and copy-pasted folklore from generic web results. A docs-first workflow also makes reviews easier because implementation choices can be traced back to a source instead of memory.

This version of the skill is not only about research behavior. It is also about preserving grounded evidence in the repo workflow itself, especially in commit messages, PR bodies, and other decision-bearing artifacts, with exact documentation URLs and, when useful, a short confirming quote from the docs.

## Why A Skill

Per-repo README guidance is a great place to start. It is visible to humans, easy to refine, and close to the project. A skill adds the next layer: it makes the workflow discoverable by agents, shareable across repositories, and improvable over time with clearer routing, reusable references, and lightweight evals.

This repo packages the same kind of docs-first rules you might keep in a project README, such as primary-source tables, local-first validation, and grounded PR summaries, into a reusable skill that can travel between projects and models.

It is best understood as a coordinator skill rather than a tiny leaf skill. The point is not just to retrieve docs. The point is to route across sources, separate facts from inference, verify locally, and leave evidence behind in a way a human can inspect.

It should be read as a reference implementation, not a universal list of sources for every team. If your world is GCP, Kubernetes, Stripe, Terraform, Rust, or something else, the intended move is to fork this and tailor the primary-source references to your own stack.

## What This Skill Covers

- A Docs-First Agent Workflow for implementation, debugging, refactoring, and review work
- Primary-source routing for AWS, AWS CDK, Microsoft, and OpenAI via MCP servers where available
- Composition with narrower vendor skills when that gives better routing, especially Microsoft's `microsoft-docs` and `microsoft-code-reference` skills on top of the Microsoft Learn MCP server
- Official documentation fallbacks for React, Next.js, TypeScript, Jest, Testing Library, Python stdlib, boto3, Gemini API, GitHub CLI, and GitHub Actions
- A groundedness standard for final summaries, commit bodies, and PR descriptions
- Exact documentation URLs in grounded evidence, and short verbatim quotes when they genuinely help support a decision
- Decision recording when more than one documented path is valid
- Contract-drift checks for user-visible, API, infrastructure, deployment, and operational changes
- A small-scope improvement loop so the skill can get better through real failures and evals

## Example Prompts

These are the kinds of prompts this skill should improve. The official docs say natural-language matching is supported:

- Codex quote: "Codex can choose a skill when your task matches the skill `description`."
- Claude Code quote: "Claude uses skills when relevant, or you can invoke one directly with `/skill-name`."

For grounded work, I recommend explicit invocation so the docs-first workflow is intentional rather than inferred. The examples below therefore show only the explicit form.

Recommended explicit invocation prompts:

- "Codex: $grounded-coding Use the AWS docs to check whether API Gateway or Lambda response streaming is the better fit for this chatbot, then explain the tradeoff with links."
- "Claude Code: /grounded-coding Use the AWS docs to check whether API Gateway or Lambda response streaming is the better fit for this chatbot, then explain the tradeoff with links."
- "Codex: $grounded-coding Review this OpenAI API change against the official docs and call out anything unsupported, inferred, or stale."
- "Claude Code: /grounded-coding Review this OpenAI API change against the official docs and call out anything unsupported, inferred, or stale."

## How It Works

1. Identify the technologies, versions, and risk areas involved in the task.
2. Route each question to the best primary source.
3. If a narrower vendor skill is installed, compose with it instead of duplicating it. For Microsoft work, that usually means `microsoft-docs` for concepts, tutorials, configuration guidance, and limits, or `microsoft-code-reference` for API and SDK verification.
4. Read the relevant official docs before editing code.
5. If more than one documented path is valid, explain the tradeoff and record the choice explicitly.
6. Implement only what the source supports, label anything beyond that as inference, then verify locally.
7. Check for contract drift and update repo-owned docs, tests, comments, migration notes, or runbooks when needed.
8. Close with grounded evidence that survives in chat, commits, and PRs when those artifacts exist.

## Composing With Microsoft Learn Skills

This skill should not try to outgrow every narrower vendor skill. The better pattern is composition.

- Use the Microsoft Learn MCP server as the official documentation source for Microsoft technologies:
  https://github.com/MicrosoftDocs/mcp
- Microsoft also ships companion agent skills:
  https://github.com/MicrosoftDocs/mcp?tab=readme-ov-file#-agent-skills
- Direct skill references:
  `microsoft-docs`: https://github.com/MicrosoftDocs/mcp/tree/main/skills/microsoft-docs
  `microsoft-code-reference`: https://github.com/MicrosoftDocs/mcp/tree/main/skills/microsoft-code-reference
- In practice, `grounded-coding` should stay responsible for the broad workflow:
  routing to primary sources, recording decisions, checking contract drift, and preserving evidence in summaries, commits, and PRs
- Let the narrower Microsoft skills do the focused retrieval work:
  - `microsoft-docs` for concepts, tutorials, configuration guidance, limits, and best practices
  - `microsoft-code-reference` for API references, SDK verification, code samples, and troubleshooting

## Fixed Grounded Evidence Format

The skill uses the same compact evidence structure across close-out notes, commit bodies, and PR descriptions:

- What changed
- Sources used, with the exact documentation URL for each source and what it confirmed
- Verification run
- Remaining uncertainty or inference

When relevant, it also records:

- the decision between multiple valid documented paths
- any contract-drift follow-up or explicitly deferred repo updates
- a short verbatim quote from the source when it materially supports the chosen decision

## Principles Behind The Skill

- Azure groundedness concept:
  https://learn.microsoft.com/en-us/azure/ai-services/content-safety/concepts/groundedness
- Azure groundedness quickstart:
  https://learn.microsoft.com/en-us/azure/ai-services/content-safety/quickstart-groundedness?tabs=curl&pivots=programming-language-foundry-portal
- OpenAI on evaluating skills:
  https://developers.openai.com/blog/eval-skills/
- Claude skill best practices:
  https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices

These sources shape the repo in a practical way:

- groundedness means tying outputs back to supplied source material and treating unsupported claims as risk
- skill quality improves when success is explicit and checkable
- discoverability depends on clear routing in the skill name, description, and usage sections
- the skill body should stay concise, with detail pushed into references

## Install And Trigger

The most explicit way to test this skill today is to clone the repo and copy the nested `grounded-coding/` skill folder into the location your agent reads from.

Quick cheat sheet:

| Tool | Install location | Explicit invocation | Docs |
| --- | --- | --- | --- |
| Codex | `.agents/skills/` or `~/.agents/skills/` | `$grounded-coding` or `/skills` | https://developers.openai.com/codex/skills/ |
| Claude Code | `.claude/skills/` or `~/.claude/skills/` | `/grounded-coding` | https://code.claude.com/docs/en/skills |

### Codex

OpenAI's Codex docs explain that skills live in repository or user locations under `.agents/skills/`:
https://developers.openai.com/codex/skills/#where-to-save-skills

User-level install:

```bash
git clone https://github.com/billster45/grounded-coding.git
mkdir -p ~/.agents/skills
cp -R -n grounded-coding/grounded-coding ~/.agents/skills/
```

Project-level install:

```bash
git clone https://github.com/billster45/grounded-coding.git
mkdir -p .agents/skills
cp -R -n grounded-coding/grounded-coding .agents/skills/
```

Trigger it in Codex:

- Explicitly: type `$grounded-coding` in your prompt
- Or use the skill picker: `/skills`
- Or let Codex auto-select it when your request matches the description

Official Codex usage docs:
https://developers.openai.com/codex/skills/#how-codex-uses-skills

### Claude Code

Claude Code uses filesystem-based custom skills in `.claude/skills/` or `~/.claude/skills/`:
https://code.claude.com/docs/en/skills
https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview

User-level install:

```bash
git clone https://github.com/billster45/grounded-coding.git
mkdir -p ~/.claude/skills
cp -R -n grounded-coding/grounded-coding ~/.claude/skills/
```

Project-level install:

```bash
git clone https://github.com/billster45/grounded-coding.git
mkdir -p .claude/skills
cp -R -n grounded-coding/grounded-coding .claude/skills/
```

Trigger it in Claude Code:

- Explicitly: `/grounded-coding`
- Or ask a matching docs-first coding question and let Claude discover it automatically

### Optional Cross-Tool Installer

If you use the open-standard `skills` installer, the GitHub install command for this repo is:

```bash
npx skills add billster45/grounded-coding --full-depth
```

The `--full-depth` flag matters here because the repo contains a nested skill directory rather than putting `SKILL.md` at the repo root.

In the manual copy commands above, `mkdir -p` creates the directory only if it does not already exist, and `cp -R -n` avoids overwriting files that are already present.

## Repository Layout

- `grounded-coding/SKILL.md`
- `grounded-coding/references/mcp-servers.md`
- `grounded-coding/references/doc-sources.md`
