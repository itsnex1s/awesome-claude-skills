---
name: vercel-cli
description: Deploy and manage Vercel projects. Use for deployments, environment variables, logs, domains, and cron jobs.
allowed-tools: Bash, Read, Edit
user-invocable: true
---

# vercel-cli

Deploy, manage environment variables, view logs, configure domains, and set up cron jobs with [Vercel CLI](https://vercel.com/docs/cli).

## Installation

```bash
npm install -g vercel
vercel login
vercel link
```

## Quick Reference

### Project Setup

```bash
vercel init              # Initialize new project
vercel link              # Link existing directory to Vercel project
vercel whoami            # Show current user
vercel project ls        # List projects
```

### Deployment

```bash
vercel                   # Deploy preview
vercel --prod            # Deploy to production
vercel deploy --force    # Force redeploy
vercel deploy --env NODE_ENV=production -e API_KEY=secret

# Build locally first
vercel build
vercel deploy --prebuilt

# Manage deployments
vercel redeploy <deployment-url-or-id>    # Rebuild + redeploy
vercel promote <deployment-url-or-id>     # Promote preview to production
vercel rollback <deployment-url-or-id>    # Rollback to previous

# List and inspect
vercel list                               # List all deployments
vercel inspect <deployment-url-or-id>     # Get deployment info
vercel remove <deployment-id>             # Delete deployment
```

### Environment Variables

**List**
```bash
vercel env ls                    # List all env vars
vercel env ls production         # List for specific environment
vercel env ls preview
vercel env ls development
```

**Add**
```bash
vercel env add MY_VAR                      # Add interactively
vercel env add API_TOKEN production        # Add to specific environment
vercel env add SECRET_KEY --sensitive      # Add sensitive (masked)
vercel env add MY_VAR --force              # Override existing
vercel env add DB_URL production main      # Add for specific branch
```

**Update**
```bash
vercel env update MY_VAR                   # Update interactively
vercel env update API_TOKEN production     # Update specific environment
echo "value" | vercel env update MY_VAR production  # From stdin
```

**Remove**
```bash
vercel env rm API_TOKEN                    # Remove from all
vercel env rm SECRET_KEY production        # Remove from specific
vercel env rm API_TOKEN -y                 # Skip confirmation
```

**Pull to Local**
```bash
vercel env pull                            # Pull to .env.local
vercel env pull .env.development.local     # Pull to custom file
vercel env pull .env.production --environment production
```

### Domains

```bash
vercel domains ls                          # List domains
vercel domains add example.com             # Add domain
vercel domains rm example.com              # Remove domain
vercel domains verify example.com          # Verify domain
vercel domains add example.com --project my-project
```

### Logs

**Runtime Logs**
```bash
vercel logs <deployment-url>               # Stream runtime logs
vercel logs <deployment-url> --json        # JSON output
vercel logs <url> --json | jq 'select(.level == "error")'
```

**Build Logs**
```bash
vercel inspect <deployment-url> --logs             # Show build logs
vercel inspect <deployment-url> --logs --wait      # Wait for build
vercel inspect <deployment-url> --logs --timeout 90s
```

### Local Development

```bash
vercel dev                       # Run local dev server
vercel dev --listen 3001         # Specific port
vercel dev --environment preview # Specific environment
```

### Cron Jobs

Configured in `vercel.json` only (no CLI commands).

```json
{
  "crons": [
    {
      "path": "/api/cron/daily-task",
      "schedule": "0 0 * * *"
    },
    {
      "path": "/api/cron/every-5-min",
      "schedule": "*/5 * * * *"
    }
  ]
}
```

**Cron Expression Format** (UTC timezone)
```
┌───────────── minute (0-59)
│ ┌───────────── hour (0-23)
│ │ ┌───────────── day of month (1-31)
│ │ │ ┌───────────── month (1-12)
│ │ │ │ ┌───────────── day of week (0-6, Sunday=0)
│ │ │ │ │
* * * * *
```

**Common Schedules**
```
*/5 * * * *     Every 5 minutes
0 */4 * * *     Every 4 hours
0 0 * * *       Daily at midnight UTC
0 9 * * 1       Every Monday at 9 AM UTC
0 0 1 * *       First day of month
```

## Common Workflows

### Deploy New Project
```bash
vercel link
vercel env add DATABASE_URL production
vercel env add API_KEY production --sensitive
vercel --prod
```

### Fix Failed Deployment
```bash
vercel inspect <deployment-id> --logs      # Check what went wrong
vercel --prod                              # Fix and redeploy
# OR
vercel rollback <previous-deployment-id>   # Rollback
```

### Environment Variable Workflow
```bash
vercel env add DATABASE_URL production
vercel env add API_KEY production --sensitive
vercel env pull .env.local                 # Pull to local
vercel env update API_KEY production       # Update after rotation
vercel env rm OLD_TOKEN -y                 # Remove deprecated
```

### Monitor Application
```bash
vercel logs my-app.vercel.app
vercel logs my-app.vercel.app --json | jq 'select(.level == "error")'
vercel logs my-app.vercel.app --json | jq 'select(.path | startswith("/api/"))'
```

### Debug Cron Jobs
```bash
cat vercel.json | jq '.crons'              # Check config
vercel --prod                               # Deploy changes
vercel logs <url> --json | jq 'select(.path | contains("/api/cron/"))'
```

## Environment Scopes

| Scope | Description |
|-------|-------------|
| `production` | Production environment |
| `preview` | Preview/staging deployments |
| `development` | Local `.env.local` file |
| Git branch | Branch-specific overrides |

## Useful Flags

| Flag | Description |
|------|-------------|
| `--prod` | Deploy to production |
| `--force` | Force redeploy |
| `-y` | Skip confirmation prompts |
| `--json` | JSON output |
| `--debug` | Debug mode |
| `--token <token>` | Use specific auth token |

## Requirements

- Node.js 18+
- Vercel account

## Notes

- Use `vercel dev` for local development with Vercel features
- Environment variables in dashboard override CLI-added vars
- Cron jobs only run on production deployments
- User-Agent for cron: `vercel-cron/1.0` (use for authentication)
