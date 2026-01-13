---
name: agent-browser
description: Automate browser interactions using agent-browser CLI. Use for taking screenshots, testing deployed sites, checking mobile views, form testing, or browser automation tasks.
allowed-tools: Bash, Read
user-invocable: true
---

# agent-browser

Automate browser interactions for testing, screenshots, and web scraping using the [agent-browser](https://github.com/vercel-labs/agent-browser) CLI tool from Vercel Labs.

## Installation

```bash
npm install -g agent-browser
agent-browser install
```

## Core Workflow (AI-Optimized)

The recommended pattern uses reference-based selection:

```bash
# 1. Navigate to page
agent-browser open "https://example.com"

# 2. Get interactive elements with refs
agent-browser snapshot -i

# 3. Interact using refs (@e1, @e2, etc.)
agent-browser click @e2
agent-browser fill @e3 "text"
```

## Commands Reference

### Navigation
```bash
agent-browser open <url>           # Navigate to URL
agent-browser back                 # Go back
agent-browser forward              # Go forward
agent-browser reload               # Reload page
```

### Snapshot (Get Element Refs)
```bash
agent-browser snapshot             # Full accessibility tree
agent-browser snapshot -i          # Interactive elements only
agent-browser snapshot -c          # Compact (remove empty elements)
agent-browser snapshot -d 3        # Limit depth to 3
agent-browser snapshot --json      # JSON output for parsing
```

### Interaction
```bash
agent-browser click @e1            # Click by ref
agent-browser click "#submit"      # Click by CSS selector
agent-browser dblclick @e1         # Double-click
agent-browser fill @e3 "text"      # Clear and fill input
agent-browser type @e3 "text"      # Type into element
agent-browser hover @e1            # Hover element
agent-browser focus @e1            # Focus element
agent-browser check @e1            # Check checkbox
agent-browser uncheck @e1          # Uncheck checkbox
agent-browser select @e1 "value"   # Select dropdown option
agent-browser drag @e1 @e2         # Drag and drop
agent-browser upload @e1 file.pdf  # Upload file
```

### Keyboard
```bash
agent-browser press Enter          # Press key
agent-browser press Control+a      # Key combination
```

### Scrolling
```bash
agent-browser scroll down 500      # Scroll down 500px
agent-browser scroll up 500        # Scroll up 500px
agent-browser scrollintoview @e1   # Scroll element into view
```

### Screenshots & PDF
```bash
agent-browser screenshot /tmp/shot.png         # Screenshot
agent-browser screenshot --full /tmp/full.png  # Full page
agent-browser pdf /tmp/page.pdf                # Save as PDF
```

### Get Information
```bash
agent-browser get text @e1         # Get element text
agent-browser get html @e1         # Get element HTML
agent-browser get value @e1        # Get input value
agent-browser get attr href @e1    # Get attribute
agent-browser get title            # Get page title
agent-browser get url              # Get current URL
```

### Check State
```bash
agent-browser is visible @e1       # Check visibility
agent-browser is enabled @e1       # Check if enabled
agent-browser is checked @e1       # Check if checked
```

### Find Elements (Semantic)
```bash
agent-browser find role button click --name "Submit"
agent-browser find text "Click me" click
agent-browser find label "Email" fill "user@test.com"
agent-browser find placeholder "Search..." type "query"
```

### Wait
```bash
agent-browser wait @e1             # Wait for element
agent-browser wait 3000            # Wait 3 seconds
```

### Browser Settings
```bash
agent-browser set viewport 1920 1080       # Set viewport size
agent-browser set device "iPhone 14"       # Emulate device
agent-browser set media dark               # Dark mode
agent-browser set media reduced-motion     # Reduced motion
agent-browser set offline on               # Offline mode
agent-browser set headers '{"Auth": "Bearer token"}'
```

### Session Management
```bash
agent-browser open url --session mytest    # Named session
agent-browser click @e1 --session mytest   # Use same session
agent-browser close --session mytest       # Close session
```

### Debug
```bash
agent-browser console               # View console logs
agent-browser errors                # View page errors
agent-browser highlight @e1         # Highlight element
agent-browser --headed open url     # Show browser window
```

## Workflow Examples

### Mobile Screenshot
```bash
agent-browser open "https://mysite.com" --session mobile
agent-browser set device "iPhone 14" --session mobile
sleep 2
agent-browser screenshot /tmp/mobile.png --session mobile
agent-browser close --session mobile
```

### Form Testing
```bash
agent-browser open "https://mysite.com/login" --session test
agent-browser snapshot -i --session test
# Output: textbox "Email" [ref=e1], textbox "Password" [ref=e2], button "Login" [ref=e3]
agent-browser fill @e1 "user@example.com" --session test
agent-browser fill @e2 "password123" --session test
agent-browser click @e3 --session test
agent-browser screenshot /tmp/after-login.png --session test
```

### Full Page Scroll Test
```bash
agent-browser open "https://mysite.com" --session scroll
sleep 2
agent-browser scroll down 1000 --session scroll
sleep 1
agent-browser screenshot /tmp/scrolled.png --session scroll
```

## Available Devices

- iPhone 14 / iPhone 14 Pro Max
- iPad Pro
- Pixel 7
- Galaxy S23

## Requirements

- Node.js 18+
- Chromium (auto-installed via `agent-browser install`)

## Notes

- Screenshots are saved as PNG by default
- Default viewport is desktop (1280x720)
- Browser runs in headless mode by default (use `--headed` to show)
- Sessions are isolated (separate cookies, storage, history)
- Use `--json` flag for machine-readable output
- Refs (@e1, @e2) are deterministic within a page state
