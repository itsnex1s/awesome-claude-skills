---
name: apple-docs
description: Look up Apple Developer Documentation (Swift, SwiftUI, HealthKit, UIKit, etc.) and WWDC session transcripts using the sosumi CLI tool. Use when working with Swift/iOS code and need to check API signatures, find documentation, or understand Apple frameworks.
allowed-tools: Bash
user-invocable: true
argument-hint: <search-query or /documentation/path>
---

# Apple Documentation Lookup

Fetch Apple Developer Documentation and WWDC session transcripts as clean markdown using the `sosumi` CLI tool.

## Installation

```bash
npm install -g @nshipster/sosumi
# or use directly with npx (no install needed)
```

## Commands

### Search documentation
When the user needs to find an API, framework, or concept:
```bash
npx @nshipster/sosumi search "$ARGUMENTS" 2>/dev/null
```

### Fetch specific documentation page
When the user provides a documentation path or URL:
```bash
npx @nshipster/sosumi fetch "$ARGUMENTS" 2>/dev/null
```

### Fetch WWDC session transcript
When the user asks about a WWDC session:
```bash
npx @nshipster/sosumi fetch "/videos/play/wwdc{year}/{session-id}" 2>/dev/null
```

## How to decide which command to use

1. If `$ARGUMENTS` starts with `/documentation` or `/videos` or `https://developer.apple.com` → use `fetch`
2. If `$ARGUMENTS` is a search query (e.g. "SwiftData", "HealthKit sleep") → use `search`
3. If no arguments provided → ask the user what they want to look up

## Workflow

1. Run the appropriate sosumi command
2. Read the output (markdown-formatted Apple documentation)
3. Present the relevant information to the user in a concise summary
4. If the search returned multiple results, pick the most relevant and offer to fetch the full docs
5. When fetching API docs, highlight: **signature**, **availability**, **key parameters**, and **usage notes**

## Common documentation paths

```
/documentation/swiftui
/documentation/swiftdata
/documentation/observation
/documentation/charts
/documentation/healthkit
/documentation/widgetkit
/documentation/backgroundtasks
/documentation/foundation
/documentation/uikit
/documentation/combine
/documentation/concurrency
/documentation/corelocation
/documentation/mapkit
/documentation/avfoundation
/documentation/coreml
/documentation/createml
/documentation/vision
/documentation/naturallanguage
/documentation/appintents
```

## Example usage

User: `/apple-docs HKCategoryValueSleepAnalysis`
→ Run: `npx @nshipster/sosumi search "HKCategoryValueSleepAnalysis"`
→ Then fetch the top result if needed

User: `/apple-docs /documentation/swiftui/view`
→ Run: `npx @nshipster/sosumi fetch "/documentation/swiftui/view"`

User: `/apple-docs SwiftData model macro`
→ Run: `npx @nshipster/sosumi search "SwiftData model macro"`

User: `/apple-docs WWDC23 SwiftData`
→ Run: `npx @nshipster/sosumi search "WWDC 2023 SwiftData"`
→ Then fetch the session path from the results

## Requirements

- Node.js 18+
- Internet connection (fetches from developer.apple.com)

## Notes

- Output is clean markdown, ideal for reading in terminal
- Search returns up to 50+ results — pick the most relevant
- Documentation paths are case-insensitive
- WWDC transcripts include full session text with timestamps
- Works with all Apple frameworks: SwiftUI, UIKit, HealthKit, CoreML, etc.
