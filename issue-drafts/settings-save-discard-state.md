# [BUG] Settings controls persist changes before Save

## Problem (one or two sentences)

Some controls inside `SettingsView` bypass the local `cachedState` / Save flow and send `updateSettings` immediately. This makes Discard/Cancel misleading because the setting may already be persisted before the user clicks Save.

## Context (who is affected and when)

Users editing Settings tabs for Auto Approve, MCP, Context Management profile thresholds, or prompt enhancement history can have changes applied even if they leave the Settings view and choose to discard unsaved changes.

## Reproduction steps

1. Open Settings.
2. Go to Auto Approve.
3. Enable execute controls and add an allowed command.
4. Do not click Save.
5. Click Done and choose to discard changes.
6. Reopen Settings or inspect extension settings/state.

## Expected result

Discarding unsaved changes leaves `allowedCommands`, `deniedCommands`, `mcpEnabled`, `profileThresholds`, and `includeTaskHistoryInEnhance` unchanged unless Save was clicked.

## Actual result

Several child controls already send `updateSettings`, so the extension host may persist changes before Save.

## Code locations

- `webview-ui/src/components/settings/SettingsView.tsx:377` includes these values in the Save payload.
- `webview-ui/src/components/settings/AutoApproveSettings.tsx:84` sends command-list changes immediately.
- `webview-ui/src/components/settings/ContextManagementSettings.tsx:131` sends `profileThresholds` immediately.
- `webview-ui/src/components/settings/PromptsSettings.tsx:200` sends `includeTaskHistoryInEnhance` immediately.
- `webview-ui/src/components/mcp/McpEnabledToggle.tsx:8` binds to live extension state and persists immediately.
- `src/core/webview/webviewMessageHandler.ts:677` persists `updateSettings` as soon as it receives the message.

## Suggested fix direction

Route Save-managed settings exclusively through `cachedState` and `setCachedStateField` while `SettingsView` is open. Remove immediate `updateSettings` calls for these controls, or clearly split truly immediate actions out of the Save/Discard settings form.

## Recommended tests

Add or update `webview-ui` SettingsView tests asserting:

- No `updateSettings` is posted before Save for Save-managed settings.
- Save posts the new values once.
- Discard does not persist child-control edits.
- Existing tests around allowed commands currently expecting immediate persistence are updated to match the Save/Discard contract.

## App Version

3.66.0

## Assumptions / uncertainty

Assumption: these controls are intended to follow the same Save/Discard contract as the rest of `SettingsView` because their values are included in `SettingsView`'s Save payload and the repository guidance says SettingsView inputs should bind to local `cachedState`, not live extension state.
