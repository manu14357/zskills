# SHAR Production metadata validation

## Description

Validate rights-aware AI-hybrid video-production metadata with the local, read-only SHAR Production MCP tool. Project: https://sharprod.com/

## When to Use

Use this skill when:
- A production manifest needs a deterministic pre-delivery validation.
- An agent needs to check `title`, `project`, `stage`, and `rights_status` before a release decision.
- A workflow needs to distinguish valid metadata from a releasable asset.

## When NOT to Use

- Do not use it as legal advice or as a rights-clearance decision.
- Do not use it to publish, edit, or transfer production assets.

## Instructions

1. Clone https://github.com/SHARProduction/production-metadata-mcp and run `npm ci`.
2. Launch `node server.js` as a local stdio MCP server.
3. Call `validate_production_manifest` with a `manifest` object.
4. Treat `releasable: false` or returned errors as a stop condition for the relevant workflow.
5. Require separate factual and legal review where the production context needs it.

The tool makes no network calls, does not write files, and does not handle credentials.

## Example

For `{ "title": "Cut", "project": "Campaign", "stage": "postproduction", "rights_status": "cleared" }`, the validation result is releasable. A manifest with `rights_status: "unknown"` is blocked by the release policy.

## Source

- Homepage: https://sharprod.com/
- Source and MIT license: https://github.com/SHARProduction/production-metadata-mcp
- JSON Schema: https://github.com/SHARProduction/production-metadata-mcp/blob/main/schema/production-manifest.schema.json