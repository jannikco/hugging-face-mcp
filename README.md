# Hugging Face model workflows for AI assistants

Inspect model branches and tags, then read a selected revision’s commit history before pinning it in downstream inference tooling. Dataset references can be inspected separately.

[Start locally](#get-started) · [Connect your assistant](#connect-your-assistant) · [Available tools](#available-tools) · [Hosted PipMCP setup](#use-pipmcp-for-production)

## Built for useful workflows

- **Build a Hugging Face brief.** Inspect model branches and tags before selecting a reproducible revision. Make that provider response available to your assistant before it proposes a next action.
- **Investigate a chosen Hugging Face result.** Inspect model commit metadata for a specified branch, tag or commit. Use an account or record returned by your provider rather than guessing an identifier.
- **Keep the workflow read-only.** The shipped operations expose Hugging Face reads. Combine their returned context in your assistant without adding a provider write operation.

## Why use this package?

Get a focused Hugging Face MCP integration without assembling request mappings and a stdio transport yourself. The package provides documented input schemas, provider authentication handling and an example CLI, with the same tools available to your assistant.

The selected tools keep this Hugging Face workflow read-only.

## Get started

**Requirements:** Node.js 22 or newer, npm, and access to the provider account you intend to use.

```sh
git clone https://github.com/jannikco/hugging-face-mcp.git
cd hugging-face-mcp
npm ci --ignore-scripts
npm test
npm start
```

`npm start` shows the tool inventory and workflow offline. It does not authenticate or call the provider. The tests use provider fixtures and stdio transport; they do not modify your account.

### Configure your provider account

The current release connects directly to the provider with **your own credentials**. It does not require a PipMCP key for local execution. Configure the environment of the process or MCP client that runs the server; `.env` files are not loaded automatically.

| Environment variable | What to supply |
| --- | --- |
| `HF_TOKEN` | Your own provider API key or access token. |

[Provider credential/reference documentation](https://huggingface.co/.well-known/openapi.json). Keep credentials out of commits, issue reports and shared client configuration.

### Run your first provider read

After configuring the environment, run `list_model_refs`:

```sh
npm start -- --call list_model_refs '{"namespace":"openai","repo":"whisper-tiny"}'
```

Replace sample account, record, file and project IDs with values from your authorized account. This command makes a real provider request and returns its result for you to inspect.

## Available tools

| Capability | MCP tool | Effect | Required inputs |
| --- | --- | --- | --- |
| Inspect model branches and tags before selecting a reproducible revision. | `list_model_refs` | Read | `namespace`, `repo` |
| Inspect model commit metadata for a specified branch, tag or commit. | `list_model_commits` | Read | `namespace`, `repo`, `rev` |
| Inspect dataset branches and tags without downloading dataset contents. | `list_dataset_refs` | Read | `namespace`, `repo` |

Tool schemas and bindings are in [connector.json](./connector.json). These are the selected operations this package ships.

### A practical tool sequence

1. Use `list_model_refs` — Inspect model branches and tags before selecting a reproducible revision.
2. Use `list_model_commits` — Inspect model commit metadata for a specified branch, tag or commit.

## Connect your assistant

Add this stdio server to an MCP-compatible client. Replace the absolute path and credential placeholders. Use your client’s secret/environment controls instead of committing this configuration.

```json
{
  "mcpServers": {
    "hugging-face-mcp": {
      "command": "node",
      "args": [
        "/absolute/path/hugging-face-mcp/server.mjs"
      ],
      "env": {
        "HF_TOKEN": "YOUR_HF_TOKEN"
      }
    }
  }
}
```

`node server.mjs` starts the MCP server; `npm start` runs the example CLI. The server reserves stdout for MCP messages. If the client does not inherit your shell environment, pass the required variables explicitly.

## Read-only scope

The shipped Hugging Face tools are read-only. This package does not provide a write command or silently expand the provider permissions.

## Use PipMCP for production

The local package is useful when you want to run these selected Hugging Face operations in your own process. PipMCP provides a hosted place to configure provider connections, publish tools, issue endpoint access tokens, apply server-side policy and approvals, and inspect execution/usage records. Provider credentials and hosted execution are configured in that service.

**[Import Hugging Face into PipMCP](https://pipmcp.com/github/start?integration=openapi-to-mcp&utm_source=github&utm_campaign=github-hugging-face-mcp&utm_content=readme)**

This provider is not listed as a ready managed template in the current availability snapshot (2026-11-08). The link opens the OpenAPI importer; it does not activate the local tools automatically.

Use [selected-openapi.json](./selected-openapi.json) as a starting point for manual import. Configure the account hostname, authentication and provider-specific requirements, then review and publish the resulting tools in PipMCP.

Local credentials are not uploaded by this repository. A hosted endpoint uses its own PipMCP endpoint access token and published tool inventory; the local and hosted credentials are separate.

## Scope and validation

- The installed package has HTTP request fixtures and MCP stdio `tools/call` tests.
- Fixture success checks client behavior; it does not establish provider authentication, paid-plan access or live account execution.
- No live provider execution is claimed for this package by these tests.
- Hosted tool names, schemas, permissions and availability can differ from this local package. A queued or accepted job/message is not proof of completion or delivery. Provider API plans, account rules and limits apply.
- See [integration.json](./integration.json) for operation-scoped evidence and the provider-specific limits above.

## FAQ

**Do I need a Hugging Face account?**

You need the provider account, token and permissions required by these operations. Provider charges and API limits remain with that account.

**Does this version require a PipMCP API key?**

This package uses your provider credentials for local execution. To use the hosted service, configure the provider connection in PipMCP and use the endpoint access token issued there.

**Why is a tool missing or a request rejected?**

This repository exposes the selected tools above. Check the required environment variables, provider scopes, account/region requirements and input IDs first. Hosted endpoints have their own published inventory and may exclude tools by role or policy. Do not retry a failed write without checking its outcome.

**Is this an official provider SDK or a hosted-service guarantee?**

This is an independently maintained MCP integration. Its fixtures and source references do not imply provider endorsement, unrestricted API access, or identical hosted behavior.

## Guides and related workflows

- [Hugging Face integration guide](https://jannikco.github.io/pipmcp-connectors/integrations/hugging-face/)
- [Hugging Face workflow and tool sequence](https://jannikco.github.io/pipmcp-connectors/workflows/hugging-face/)
- [More integration workflows](https://jannikco.github.io/pipmcp-connectors/workflows/)
- [Typed LeadAdapter API client](https://github.com/jannikco/linkedin-typescript-sdk)
- [LeadAdapter Python API client](https://github.com/jannikco/linkedin-python-sdk)
- [LinkedIn API quickstart](https://github.com/jannikco/linkedin-api)
- [LinkedIn people and post search](https://github.com/jannikco/linkedin-search-api)
- [LinkedIn post engagers](https://github.com/jannikco/linkedin-post-engagers)
- [LeadAdapter LinkedIn MCP server](https://github.com/jannikco/linkedin-mcp)
- [OpenAPI to MCP import starter](https://github.com/jannikco/openapi-to-mcp)
- [Swagger to MCP import starter](https://github.com/jannikco/swagger-to-mcp)
- [Hugging Face API and credential reference](https://huggingface.co/.well-known/openapi.json)

Maintained by [jannikco](https://github.com/jannikco). MIT for this repository’s code; dependencies retain their licenses. [Report a reproducible issue](https://github.com/jannikco/hugging-face-mcp/issues) with redacted input and error details. For credentials or security issues, use [private contact](https://pipmcp.com/contact).
