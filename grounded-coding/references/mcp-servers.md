# MCP Servers

Use these sources first when the task touches the matching ecosystem. Prefer the MCP server over generic web search because it keeps retrieval scoped to official vendor documentation.

## AWS MCP

- Source: https://github.com/awslabs/mcp
- Coverage: AWS documentation, AWS CDK guides and examples, reference material, and AWS task automation, depending on which AWS MCP server is installed
- Tool names: the AWS MCP repository publishes multiple servers, so tool names are not fixed across every client. In Codex-style integrations, common AWS MCP tools include `search_documentation`, `read_documentation`, `recommend`, `call_aws`, `suggest_aws_commands`, `retrieve_agent_sop`, `get_regional_availability`, and `list_regions`.
- Use for: AWS service docs, AWS CDK construct and synthesis behavior, CLI and API lookups, current feature availability, AWS architecture guidance, and managed AWS operations

## Microsoft Learn MCP

- Source: https://github.com/MicrosoftDocs/mcp
- Agent skills: https://github.com/MicrosoftDocs/mcp?tab=readme-ov-file#-agent-skills
- Hosted endpoint: `https://learn.microsoft.com/api/mcp`
- Tool names: `microsoft_docs_search`, `microsoft_docs_fetch`, `microsoft_code_sample_search`
- Companion skills from the same repo:
  - `$microsoft-docs` for concepts, tutorials, configuration guidance, limits, and best practices:
    `https://github.com/MicrosoftDocs/mcp/tree/main/skills/microsoft-docs`
  - `$microsoft-code-reference` for Microsoft API references, SDK verification, code samples, and troubleshooting:
    `https://github.com/MicrosoftDocs/mcp/tree/main/skills/microsoft-code-reference`
- Best integration with `grounded-coding`:
  - use the Microsoft companion skill as the retrieval layer when it is installed
  - keep `grounded-coding` responsible for cross-stack source routing, decision recording, contract-drift checks, and evidence preserved in close-out notes, commit bodies, and PR descriptions
- Use for: Azure, .NET, Microsoft 365, Power Platform, Windows, Microsoft APIs, and official Microsoft code samples
- Useful docs for this skill:
  `https://learn.microsoft.com/en-us/azure/ai-services/content-safety/concepts/groundedness`
  `https://learn.microsoft.com/en-us/azure/ai-services/content-safety/quickstart-groundedness?tabs=curl&pivots=programming-language-foundry-portal`

## OpenAI Docs MCP

- Source: https://developers.openai.com/resources/docs-mcp/
- Hosted endpoint: `https://developers.openai.com/mcp`
- Coverage: read-only access to official OpenAI documentation from `developers.openai.com` and `platform.openai.com`
- Tool names: client integrations commonly expose `search_openai_docs`, `fetch_openai_doc`, `list_openai_docs`, `get_openapi_spec`, and `list_api_endpoints`
- Use for: OpenAI API usage, endpoint schemas, Responses API behavior, SDK guidance, ChatGPT Apps SDK, and Codex documentation
- Useful docs for this skill:
  `https://developers.openai.com/blog/eval-skills/`
