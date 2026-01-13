# Creating New Skills

This guide explains how to create new skills for Claude Code.

## What is a Skill?

A skill is a markdown file (`SKILL.md`) that teaches Claude Code how to use specific tools, commands, or workflows. When you ask Claude to perform a task, it references these skills to provide accurate, contextual help.

## Skill Structure

### File Location

```
skills/
└── your-skill-name/
    └── SKILL.md
```

### Required Format

Every `SKILL.md` must have:

1. **YAML Frontmatter** - Metadata about the skill
2. **Title** - Name of the skill
3. **Description** - What the skill does
4. **Commands/Instructions** - How to use it

### Minimal Example

```markdown
---
name: my-tool
description: Brief description of what this skill does. This appears in skill listings.
allowed-tools: Bash, Read
user-invocable: true
---

# my-tool

Description of what this tool/skill does.

## Installation

\`\`\`bash
npm install -g my-tool
\`\`\`

## Commands

\`\`\`bash
my-tool command --flag value
\`\`\`

## Examples

\`\`\`bash
my-tool init
my-tool build --production
\`\`\`
```

## YAML Frontmatter Reference

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Skill identifier (lowercase, hyphenated) |
| `description` | Yes | One-line description (shown in listings) |
| `allowed-tools` | Yes | Claude Code tools the skill can use |
| `user-invocable` | No | Set `true` if user can invoke directly |

### Allowed Tools

Choose only what your skill needs:

| Tool | Use For |
|------|---------|
| `Bash` | Running shell commands |
| `Read` | Reading files |
| `Edit` | Editing existing files |
| `Write` | Creating new files |
| `WebFetch` | Fetching web content |
| `WebSearch` | Searching the web |

## Content Guidelines

### 1. Start with Installation

If the skill requires a tool to be installed:

```markdown
## Installation

\`\`\`bash
npm install -g tool-name
\`\`\`
```

### 2. Quick Reference First

Put the most common commands at the top:

```markdown
## Quick Reference

\`\`\`bash
tool-name init          # Initialize
tool-name build         # Build project
tool-name deploy        # Deploy to production
\`\`\`
```

### 3. Group Related Commands

Organize commands logically:

```markdown
## Development

\`\`\`bash
tool dev      # Start dev server
tool watch    # Watch mode
\`\`\`

## Production

\`\`\`bash
tool build    # Build for production
tool deploy   # Deploy
\`\`\`
```

### 4. Include Common Workflows

Show real-world usage patterns:

```markdown
## Common Workflows

### Deploy to Production

\`\`\`bash
tool build
tool test
tool deploy --prod
\`\`\`
```

### 5. Add Troubleshooting

Help with common issues:

```markdown
## Troubleshooting

### Error: Port already in use

\`\`\`bash
lsof -i :3000
kill -9 <PID>
\`\`\`
```

## Complete Example

Here's a complete skill template:

```markdown
---
name: example-cli
description: Example CLI tool for demonstration. Use for building, testing, and deploying applications.
allowed-tools: Bash, Read, Edit
user-invocable: true
---

# example-cli

A CLI tool for building, testing, and deploying applications.

## Installation

\`\`\`bash
npm install -g example-cli
\`\`\`

## Quick Reference

\`\`\`bash
example init            # Initialize new project
example dev             # Start development server
example build           # Build for production
example test            # Run tests
example deploy          # Deploy to production
\`\`\`

## Commands

### Initialize Project

\`\`\`bash
example init                    # Interactive setup
example init --template react   # Use template
example init --yes              # Accept defaults
\`\`\`

### Development

\`\`\`bash
example dev                     # Start dev server
example dev --port 3001         # Custom port
example dev --open              # Open in browser
\`\`\`

### Building

\`\`\`bash
example build                   # Production build
example build --analyze         # Bundle analysis
example build --sourcemap       # Generate sourcemaps
\`\`\`

### Testing

\`\`\`bash
example test                    # Run all tests
example test --watch            # Watch mode
example test --coverage         # With coverage
example test src/utils          # Specific folder
\`\`\`

### Deployment

\`\`\`bash
example deploy                  # Deploy to default
example deploy --prod           # Production
example deploy --preview        # Preview environment
\`\`\`

## Configuration

### example.config.js

\`\`\`javascript
module.exports = {
  port: 3000,
  output: 'dist',
  publicPath: '/',
}
\`\`\`

## Common Workflows

### Start New Project

\`\`\`bash
example init my-project
cd my-project
example dev
\`\`\`

### Deploy to Production

\`\`\`bash
example test
example build
example deploy --prod
\`\`\`

## Environment Variables

| Variable | Description |
|----------|-------------|
| `EXAMPLE_TOKEN` | API token for deployment |
| `EXAMPLE_ENV` | Environment (dev/prod) |

## Troubleshooting

### Build fails with memory error

\`\`\`bash
NODE_OPTIONS="--max-old-space-size=4096" example build
\`\`\`

### Port already in use

\`\`\`bash
example dev --port 3001
\`\`\`

## Requirements

- Node.js 18+
- npm or pnpm

## Related Skills

- [[other-skill]]
```

## Best Practices

1. **Be Concise** - Include only what's needed
2. **Use Examples** - Show real commands, not abstractions
3. **Copy-Pasteable** - Commands should work when copied
4. **Keep Updated** - Update when tools change
5. **Test Commands** - Verify everything works

## Adding Your Skill

1. Create folder: `skills/your-skill-name/`
2. Create `SKILL.md` with frontmatter and content
3. Test by copying to `~/.claude/skills/`
4. Submit PR to this repository

## Questions?

Open an issue if you need help creating a skill.
