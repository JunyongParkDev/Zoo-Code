# [BUG] Diagnostics download creates invalid `.json`

## Problem (one or two sentences)

The error diagnostics generator writes a file with a `.json` extension but prepends `//` comments before the JSON payload. The resulting file is not valid JSON.

## Context (who is affected and when)

Users clicking the diagnostics/download action on an error get a file that opens with JSON syntax errors and cannot be consumed by standard JSON tooling without manually removing the header.

## Reproduction steps

1. Trigger an error row with diagnostics available.
2. Click the diagnostics download/action button.
3. Open the generated `zoo-diagnostics-*.json` file in VS Code or run it through `JSON.parse`.

## Expected result

The generated `.json` file parses as valid JSON while still carrying sharing guidance.

## Actual result

The file starts with `//` comments, so it is invalid JSON.

## Code locations

- `src/core/webview/diagnosticsHandler.ts:66` prepends comment lines before the JSON payload.
- `src/core/webview/diagnosticsHandler.ts:76` writes the file with a `zoo-diagnostics-*.json` filename.
- `src/core/webview/__tests__/diagnosticsHandler.spec.ts:82` currently asserts that the comment header is present.

## Suggested fix direction

Put the guidance inside the JSON object, for example a top-level `instructions` or `supportNote` field, or change the extension to `.jsonc` if comments are intentional.

## Recommended tests

Update `src/core/webview/__tests__/diagnosticsHandler.spec.ts` to:

- Parse the written content with `JSON.parse`.
- Assert the guidance is present as structured data.
- Keep the existing assertions for error metadata and API conversation history.

## App Version

3.66.0

## Assumptions / uncertainty

Assumption: support tooling and users expect the diagnostics artifact to be valid JSON because the generated filename uses the `.json` extension.
