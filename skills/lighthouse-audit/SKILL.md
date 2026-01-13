---
name: lighthouse-audit
description: Run Lighthouse performance audits on deployed websites. Checks Performance, Accessibility, Best Practices, and SEO scores.
allowed-tools: Bash, Read, WebFetch
user-invocable: true
---

# lighthouse-audit

Run Lighthouse performance audits on deployed websites. Checks Performance, Accessibility, Best Practices, and SEO scores.

## Installation

```bash
# Lighthouse CLI (optional, for local audits)
npm install -g lighthouse

# agent-browser for visual testing
npm install -g agent-browser
agent-browser install
```

## Quick Audit (CLI)

```bash
# Run Lighthouse audit
npx lighthouse https://example.com --output=json --output-path=/tmp/report.json

# Extract scores
cat /tmp/report.json | jq '{
  performance: (.categories.performance.score * 100 | round),
  accessibility: (.categories.accessibility.score * 100 | round),
  bestPractices: (.categories["best-practices"].score * 100 | round),
  seo: (.categories.seo.score * 100 | round)
}'
```

## Detailed Audit

```bash
# Full audit with all categories
npx lighthouse https://example.com \
  --output=json \
  --output-path=/tmp/lighthouse.json \
  --chrome-flags="--headless" \
  --only-categories=performance,accessibility,best-practices,seo

# Extract scores and metrics
cat /tmp/lighthouse.json | jq '{
  scores: {
    performance: (.categories.performance.score * 100 | round),
    accessibility: (.categories.accessibility.score * 100 | round),
    bestPractices: (.categories["best-practices"].score * 100 | round),
    seo: (.categories.seo.score * 100 | round)
  },
  metrics: {
    FCP: .audits["first-contentful-paint"].displayValue,
    LCP: .audits["largest-contentful-paint"].displayValue,
    TBT: .audits["total-blocking-time"].displayValue,
    CLS: .audits["cumulative-layout-shift"].displayValue,
    SpeedIndex: .audits["speed-index"].displayValue
  }
}'
```

## PageSpeed Insights (Visual)

```bash
# Open PageSpeed Insights
agent-browser open "https://pagespeed.web.dev/analysis?url=https://example.com" --session lighthouse

# Wait for analysis to complete
sleep 20

# Take screenshot of results
agent-browser screenshot /tmp/lighthouse-results.png --session lighthouse
agent-browser close --session lighthouse
```

## Output Format

### Score Ranges

| Score | Status | Meaning |
|-------|--------|---------|
| 90-100 | Green | Good |
| 50-89 | Orange | Needs Improvement |
| 0-49 | Red | Poor |

### Core Web Vitals

| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| FCP | < 1.8s | 1.8s - 3s | > 3s |
| LCP | < 2.5s | 2.5s - 4s | > 4s |
| TBT | < 200ms | 200ms - 600ms | > 600ms |
| CLS | < 0.1 | 0.1 - 0.25 | > 0.25 |

## Common Workflows

### Quick Performance Check
```bash
npx lighthouse https://mysite.com --output=json --quiet | jq '.categories.performance.score * 100'
```

### Mobile vs Desktop
```bash
# Mobile (default)
npx lighthouse https://mysite.com --output=json --output-path=/tmp/mobile.json

# Desktop
npx lighthouse https://mysite.com --output=json --output-path=/tmp/desktop.json --preset=desktop

# Compare
echo "Mobile: $(cat /tmp/mobile.json | jq '.categories.performance.score * 100')%"
echo "Desktop: $(cat /tmp/desktop.json | jq '.categories.performance.score * 100')%"
```

### Find Performance Issues
```bash
# Get failed audits
cat /tmp/lighthouse.json | jq '[.audits | to_entries[] | select(.value.score != null and .value.score < 0.9) | {id: .key, score: .value.score, title: .value.title}] | sort_by(.score) | .[0:10]'
```

### Check Unused JavaScript
```bash
cat /tmp/lighthouse.json | jq '.audits["unused-javascript"].details.items[:5]'
```

### Check LCP Element
```bash
cat /tmp/lighthouse.json | jq '.audits["largest-contentful-paint-element"].details'
```

## Report Options

```bash
# Generate HTML report
npx lighthouse https://example.com --output=html --output-path=./report.html

# Generate both JSON and HTML
npx lighthouse https://example.com --output=json --output=html --output-path=./report

# Specific categories only
npx lighthouse https://example.com --only-categories=performance,seo

# Quiet mode (less output)
npx lighthouse https://example.com --quiet
```

## Automation

### CI/CD Check
```bash
#!/bin/bash
SCORE=$(npx lighthouse https://mysite.com --output=json --quiet | jq '.categories.performance.score * 100')
if (( $(echo "$SCORE < 90" | bc -l) )); then
  echo "Performance score $SCORE is below 90"
  exit 1
fi
echo "Performance score: $SCORE"
```

### Compare Before/After
```bash
# Before changes
npx lighthouse https://mysite.com --output=json --output-path=/tmp/before.json

# After changes
npx lighthouse https://mysite.com --output=json --output-path=/tmp/after.json

# Compare
echo "Before: $(cat /tmp/before.json | jq '.categories.performance.score * 100')%"
echo "After: $(cat /tmp/after.json | jq '.categories.performance.score * 100')%"
```

## Requirements

- Node.js 18+
- Chrome/Chromium (for CLI audits)
- Website must be publicly accessible

## Notes

- Mobile scores are default (throttled 4G, mobile viewport)
- Use `--preset=desktop` for desktop scores
- PageSpeed Insights API has rate limits
- Scores can vary between runs (network conditions, server load)
- Use multiple runs and average for accurate comparison
