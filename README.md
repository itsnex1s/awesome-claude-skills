# Awesome Claude Code Skills

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

A curated collection of skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — Anthropic's AI-powered CLI for software development.

## What are Skills?

Skills extend Claude Code's capabilities by providing domain-specific knowledge, commands, and workflows. They help Claude assist you with the full development lifecycle.

## Skills

### Deployment & Infrastructure

| Skill | Description |
|-------|-------------|
| [vercel-cli](skills/vercel-cli) | Deploy and manage Vercel projects, env vars, domains, cron jobs |
| [docker-cli](skills/docker-cli) | Build images, run containers, Docker Compose |
| [github-actions](skills/github-actions) | CI/CD workflows, automated testing and deployment |

### Testing & Quality

| Skill | Description |
|-------|-------------|
| [testing](skills/testing) | Vitest, Jest, Playwright — unit, integration, E2E tests |
| [agent-browser](skills/agent-browser) | Browser automation, screenshots, UI testing |
| [lighthouse-audit](skills/lighthouse-audit) | Performance audits, Core Web Vitals |
| [code-review](skills/code-review) | Code review guidelines and best practices |

### Development

| Skill | Description |
|-------|-------------|
| [git-workflow](skills/git-workflow) | Conventional commits, branching, PR workflows |
| [database-cli](skills/database-cli) | Prisma ORM — migrations, schema, queries |
| [changelog-generator](skills/changelog-generator) | Generate release notes from commits |

## Installation

### All Skills

```bash
git clone https://github.com/itsnex1s/awesome-claude-skills.git
cp -r awesome-claude-skills/skills/* ~/.claude/skills/
```

### Individual Skill

```bash
mkdir -p ~/.claude/skills
cp -r awesome-claude-skills/skills/vercel-cli ~/.claude/skills/
```

## Usage

Once installed, Claude Code automatically uses relevant skills. Examples:

```
Deploy this to Vercel production
→ Uses vercel-cli skill

Take a screenshot of the deployed site on mobile
→ Uses agent-browser skill

Run Lighthouse audit
→ Uses lighthouse-audit skill

Set up CI/CD with GitHub Actions
→ Uses github-actions skill
```

## Creating Skills

See [docs/CREATING_SKILLS.md](docs/CREATING_SKILLS.md) for a complete guide.

### Quick Start

```markdown
---
name: my-skill
description: What this skill does
allowed-tools: Bash, Read
user-invocable: true
---

# my-skill

## Commands

\`\`\`bash
my-command --flag value
\`\`\`
```

## Contributing

Contributions welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting a PR.

## License

MIT

## Related

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [agent-browser](https://github.com/vercel-labs/agent-browser) — Browser automation CLI
