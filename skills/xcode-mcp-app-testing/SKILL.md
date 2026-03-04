---
name: xcode-mcp-app-testing
description: MCP-first testing workflow for Xcode apps (iOS, iPadOS, macOS). Use when the user asks to build, test, run, or verify an Xcode project — including UI checks, smoke tests, regression tests, or simulator launches. Prefers Xcode MCP tools via mcpbridge; falls back to xcodebuild/simctl CLI when MCP is unavailable.
allowed-tools: Bash, Read
user-invocable: true
argument-hint: <test scope, e.g. "smoke test" or "run UI tests">
---

# Xcode MCP App Testing

Build, test, and validate Xcode apps using the native Xcode MCP server (Xcode 26.3+). Falls back to `xcodebuild`/`simctl` CLI when MCP is not connected.

## Installation

### Enable Xcode MCP server

1. Open Xcode → Settings (`⌘,`) → Intelligence
2. Under Model Context Protocol, toggle **Xcode Tools** to ON

### Register with Claude Code

```bash
claude mcp add --transport stdio xcode -- xcrun mcpbridge
claude mcp list  # verify "xcode" appears
```

If `xcode` does not appear: re-run the add command, confirm the Xcode Tools toggle is enabled, and restart Claude Code.

## Xcode MCP Tools Reference

### Build & Testing

| Tool | Purpose |
|------|---------|
| `BuildProject` | Compile the project for a scheme and destination |
| `GetBuildLog` | Retrieve build output (errors, warnings, notes) |
| `RunAllTests` | Execute the full test suite |
| `RunSomeTests` | Run specific test targets or test methods |
| `GetTestList` | Enumerate all available test cases |

### Diagnostics

| Tool | Purpose |
|------|---------|
| `XcodeListNavigatorIssues` | List all current warnings and errors |
| `XcodeRefreshCodeIssuesInFile` | Re-scan a specific file for issues |

### File Operations

| Tool | Purpose |
|------|---------|
| `XcodeRead` | Read file contents |
| `XcodeWrite` | Create a new file |
| `XcodeUpdate` | Modify an existing file |
| `XcodeGlob` | Find files by pattern |
| `XcodeGrep` | Search text across the project |
| `XcodeLS` | List directory contents |
| `XcodeMakeDir` | Create directories |
| `XcodeRM` | Delete files or directories |
| `XcodeMV` | Move or rename items |

### Intelligence & Preview

| Tool | Purpose |
|------|---------|
| `ExecuteSnippet` | Run Swift code in a REPL environment |
| `RenderPreview` | Capture SwiftUI preview screenshots |
| `DocumentationSearch` | Search Apple docs and WWDC transcripts |

### Workspace

| Tool | Purpose |
|------|---------|
| `XcodeListWindows` | List open Xcode project windows |

## Execution Workflow

### 1. Detect project context

**MCP:** `XcodeListWindows` → `XcodeLS` → `XcodeGlob` for `.xcodeproj`, `.xcworkspace`, `Package.swift`

**CLI fallback:**
```bash
find . -maxdepth 2 \( -name "*.xcodeproj" -o -name "*.xcworkspace" -o -name "Package.swift" \) | head -10
xcodebuild -list
```

### 2. Build

**MCP:** `BuildProject` with scheme and destination → on failure, `GetBuildLog` for diagnostics

**CLI fallback:**
```bash
xcodebuild -scheme <SCHEME> -destination 'platform=iOS Simulator,name=<DEVICE>' build 2>&1
```

Stop and report immediately on build failure with the shortest actionable error summary.

### 3. Run tests

**MCP:** `GetTestList` to discover tests → `RunAllTests` or `RunSomeTests` for targeted runs

**CLI fallback:**
```bash
xcodebuild -scheme <SCHEME> -destination 'platform=iOS Simulator,name=<DEVICE>' test 2>&1
```

Capture failing test names, file references, and failure messages.

### 4. UI / functional validation

**MCP:** `RenderPreview` for SwiftUI screenshot capture. For runtime validation, combine with simulator launch.

**CLI fallback:**
```bash
xcrun simctl boot <UDID>
xcrun simctl bootstatus <UDID> -b
xcrun simctl install <UDID> <APP_PATH>
xcrun simctl launch <UDID> <BUNDLE_ID>
xcrun simctl io <UDID> screenshot /tmp/screen.png
```

### 5. Diagnostics

**MCP:** `XcodeListNavigatorIssues` → `XcodeRefreshCodeIssuesInFile` for specific files → `GetBuildLog` for crash details

**CLI fallback:**
```bash
xcrun simctl diagnose <UDID>
```

### 6. Quick code verification

**MCP:** `ExecuteSnippet` — run Swift snippets in REPL to verify logic without a full build cycle

### 7. Documentation lookup

**MCP:** `DocumentationSearch` — search Apple docs and WWDC transcripts with semantic search

## Minimum Smoke Checklist

When no explicit test plan is provided, verify:

1. App launches without crash
2. Main navigation loads and is interactive
3. Primary user flow completes (create / save / send / main CTA)
4. Permission-gated paths handled (granted + denied states)
5. Error and empty states render without UI breakage

## Reporting Format

Structure the final output as:

1. **Environment** — scheme, destination, build config, MCP status (connected / fallback)
2. **Executed** — build result, test results, UI flows, artifacts collected
3. **Findings** — ordered by severity (P0 critical → P3 cosmetic) with exact repro steps
4. **Evidence** — screenshot paths, log excerpts
5. **Next actions** — minimal fix plan, prioritized

## Common Destinations

```bash
# List available simulators
xcrun simctl list devices available

# Common destinations for xcodebuild
'platform=iOS Simulator,name=iPhone 16 Pro'
'platform=iOS Simulator,name=iPhone SE (3rd generation)'
'platform=iOS Simulator,name=iPad Pro 13-inch (M4)'
'platform=macOS'
```

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `xcode` not in `claude mcp list` | Re-run `claude mcp add`, confirm Xcode Tools toggle, restart Claude Code |
| MCP connects but cannot access project | Open the project in Xcode first; mcpbridge auto-detects the Xcode process |
| Build succeeds in Xcode but fails via CLI | Check scheme sharing: Xcode → Manage Schemes → mark scheme as Shared |
| `simctl` cannot find device | Run `xcrun simctl list devices available` to get correct UDID/name |
| Tests time out | Add `-test-timeouts-enabled YES -default-test-execution-time-allowance 300` |

## Notes

- Xcode must be running with the project open for MCP tools to work
- `mcpbridge` communicates with Xcode via XPC — no network required
- `RenderPreview` returns actual screenshot images of SwiftUI previews
- `ExecuteSnippet` runs in an isolated Swift REPL, useful for quick logic checks
- When the project builds a simulator executable instead of `.app`, use `simctl spawn` for process-level checks
