---
name: grounded-coding
description: Ground non-trivial code changes and reviews in primary sources instead of memory. Use when work depends on vendor APIs, SDKs, frameworks, cloud services, CI/CD systems, or other version-sensitive tooling and the agent should verify behavior against official documentation before editing.
compatibility: Works best with AWS MCP, Microsoft Learn MCP, and OpenAI Docs MCP. Without them, fall back to the official docs listed in `references/`.
---

# Grounded Coding

This is a coordinator skill, not a leaf retrieval skill. Use it to route work to the right primary source, compose with narrower documentation skills, implement only supported behavior, and leave a traceable grounded close-out.

## When To Use This

Use this skill when:

- a non-trivial code change depends on vendor APIs, SDKs, framework behavior, config shape, hosted platform features, or managed service limits
- you want the agent to justify an implementation or review with official docs and links
- a review should focus on stale patterns, hallucinated methods, or unsupported claims
- the work should be easy to audit, share, and improve across repos

## When Not To Use This

Do not use this skill for:

- trivial edits that do not depend on external technology behavior
- purely local refactors where the repository itself is the only relevant source of truth
- speculative brainstorming where the goal is idea generation rather than grounded implementation

## Operating Contract

1. Read repository guidance first.
   Check `README.md`, `AGENTS.md`, `agents.md`, and any repo-specific docs for local workflow rules, validation order, deployment policy, and project conventions.
2. Route to the best primary source.
   Match the technology to the right official docs or vendor MCP server before broader browsing.
3. Read before editing.
   For non-trivial changes, fetch the exact API reference, configuration, migration, or troubleshooting pages needed before making edits.
4. Separate facts from inference.
   If the docs do not say something directly, label the extra reasoning as inference rather than presenting it as fact.
5. Record decisions, not just facts.
   When more than one documented path is valid, explain the tradeoff and explicitly record the path you chose.
6. Verify locally first.
   Prefer the smallest meaningful local verification available, such as tests, lint, typecheck, build, or a targeted command. If verification cannot be run, say so clearly.
7. Check contract drift.
   If the change affects a user-visible, API, infrastructure, deployment, or operational contract, check whether repo-owned docs, tests, comments, migration notes, or runbooks also need updates.
8. Preserve evidence.
   The final close-out should name the sources consulted, include the exact documentation URL for each source, state what each source confirmed, and say what verification ran and what remains uncertain. If a short verbatim quote from the doc materially strengthens the decision, include it and mark it clearly as a quote. If a commit or PR is created, preserve the same evidence there.

## Source Routing

- Repo guidance:
  treat local docs and repo conventions as first-party context for how this repository works
- AWS, AWS CDK, Microsoft, OpenAI:
  read [references/mcp-servers.md](references/mcp-servers.md) and prefer documentation retrieval from the relevant MCP server before broader browsing
- Action vs retrieval:
  prefer documentation and reference tools first; use operational or action-taking tools only when the task explicitly requires execution
- React, Next.js, TypeScript, Jest, Testing Library, Python stdlib, boto3, Gemini API, GitHub CLI, GitHub Actions:
  use the official documentation URLs in [references/doc-sources.md](references/doc-sources.md)
- Local repository context:
  use the repo's code, lockfiles, config, tests, and README guidance together with vendor docs, not instead of vendor docs

## Primary Sources

Use these sources directly when routing the task. Keep [references/mcp-servers.md](references/mcp-servers.md) and [references/doc-sources.md](references/doc-sources.md) as the fuller routing tables.

| Technology | Primary source |
| --- | --- |
| AWS, AWS CDK | AWS MCP: https://github.com/awslabs/mcp |
| Microsoft | Microsoft Learn MCP: https://learn.microsoft.com/api/mcp |
| OpenAI | OpenAI Docs MCP: https://developers.openai.com/resources/docs-mcp/ |
| React | https://react.dev/ |
| Next.js | https://nextjs.org/docs |
| TypeScript | https://www.typescriptlang.org/docs/ |
| Jest | https://jestjs.io/docs/getting-started |
| Testing Library | https://testing-library.com/docs/ |
| Python standard library | https://docs.python.org/3/library/ |
| boto3 | https://boto3.amazonaws.com/v1/documentation/api/latest/index.html |
| Gemini API | https://ai.google.dev/gemini-api/docs |
| GitHub CLI | https://cli.github.com/manual/ |
| GitHub Actions | https://docs.github.com/en/actions |

## Skill Composition

This skill is intentionally broad. When a narrower documentation skill is installed for part of the stack, compose with it rather than duplicating it.

- For Microsoft-only concepts, tutorials, configuration guidance, limits, and best practices, prefer `$microsoft-docs` as the retrieval layer when it is available.
- For Microsoft API lookups, SDK method verification, code samples, and troubleshooting, prefer `$microsoft-code-reference` when it is available.
- Keep `grounded-coding` responsible for cross-stack source routing, decision recording, contract-drift checks, and evidence preserved in close-out notes and repo workflow artifacts.
- Do not copy detailed retrieval instructions from narrower skills into this skill unless the overlap is truly core and stable.

## Groundedness Modes

- Fast pass:
  confirm the claim against the source, avoid speculation, and keep the explanation brief. Use this for routine implementation and low-risk edits.
- Reasoning pass:
  explain why the source supports the change, call out ambiguity, and surface unsupported assumptions. Use this for architecture changes, debugging, reviews, and higher-risk edits.

If speed matters, prefer the fast pass. If the task is ambiguous or the risk of drift is high, prefer the reasoning pass.

## Review Mode

When reviewing an existing change, focus on:

- undocumented or hallucinated APIs
- deprecated or stale patterns
- config that contradicts official docs
- behavior claims that are unsupported by the cited source
- documented options where the chosen path is still the wrong engineering or product decision for this repo
- missing decision records when several valid documented paths existed
- missing evidence in commit, PR, or handoff artifacts for grounded choices
- contract changes that were not reflected in repo-owned docs, tests, comments, migration notes, or runbooks
- tests that validate the wrong contract

If a change appears correct but was not grounded, call that out. "Probably right" is weaker than "verified against the source."

## Grounded Close-Out

Use this structure for final chat summaries and for any commit or PR body created for non-trivial grounded work.

Required:

- what changed
- what sources were used, with the exact documentation URL for each source, and what each source confirmed
- what verification ran
- what remains uncertain, if anything

Add when relevant:

- decision record
- contract drift follow-up
- a short verbatim quote from the source when it materially supports the chosen decision

If a relevant primary source exists and was not consulted, the task is not done yet.

## Definition Of Done

This skill has done its job when:

- the task was routed to the right primary source before non-trivial edits
- the implementation or review note is traceable to official documentation
- unsupported claims were removed, sourced, or labeled as inference
- the decision between multiple valid documented paths was recorded when relevant
- contract drift was checked and handled or explicitly deferred
- at least one meaningful verification step ran, or the absence of verification was made explicit
- the same evidence was preserved in commit or PR artifacts when those artifacts exist

## Improvement Loop

1. Try the skill on real prompts.
2. Save failure cases where the skill did not trigger, triggered too eagerly, or used the wrong source.
3. Refine the `name`, `description`, and trigger sections before adding more body text.
4. Turn repeated misses into a small eval set and prefer measurable checks over vague claims that the skill "feels better."
