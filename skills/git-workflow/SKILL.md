---
name: git-workflow
description: Git workflows, conventional commits, branching strategies, and PR management. Use for commits, branches, merges, rebases, and PR workflows.
allowed-tools: Bash, Read
user-invocable: true
---

# git-workflow

Git workflows, conventional commits, branching strategies, and pull request management.

## Conventional Commits

Format: `<type>(<scope>): <description>`

### Types
```
feat:     New feature
fix:      Bug fix
docs:     Documentation only
style:    Formatting, no code change
refactor: Code change, no feature/fix
perf:     Performance improvement
test:     Adding tests
chore:    Build, config, dependencies
ci:       CI/CD changes
revert:   Revert previous commit
```

### Examples
```bash
git commit -m "feat(auth): add OAuth2 login"
git commit -m "fix(api): handle null response"
git commit -m "docs: update README installation"
git commit -m "chore(deps): upgrade React to 19"
git commit -m "refactor(utils): simplify date formatting"
```

### Breaking Changes
```bash
git commit -m "feat(api)!: change response format"
# or
git commit -m "feat(api): change response format

BREAKING CHANGE: Response now returns array instead of object"
```

## Branch Naming

### Format
```
<type>/<ticket>-<description>
```

### Examples
```bash
git checkout -b feat/AUTH-123-oauth-login
git checkout -b fix/BUG-456-null-pointer
git checkout -b chore/upgrade-dependencies
git checkout -b hotfix/security-patch
```

### Types
```
feat/     Feature branch
fix/      Bug fix branch
hotfix/   Urgent production fix
chore/    Maintenance
docs/     Documentation
refactor/ Code refactoring
test/     Test additions
```

## Common Workflows

### Start New Feature
```bash
git checkout main
git pull origin main
git checkout -b feat/TICKET-123-feature-name
```

### Commit Changes
```bash
git add -A
git commit -m "feat(scope): description"
```

### Push and Create PR
```bash
git push -u origin HEAD
gh pr create --title "feat(scope): description" --body "## Summary\n- Change 1\n- Change 2"
```

### Update Branch with Main
```bash
# Rebase (clean history)
git fetch origin
git rebase origin/main

# Or merge (preserves history)
git merge origin/main
```

### Interactive Rebase (squash commits)
```bash
git rebase -i HEAD~3  # Last 3 commits
# Change 'pick' to 'squash' or 's' for commits to combine
```

### Amend Last Commit
```bash
git add .
git commit --amend --no-edit  # Keep same message
git commit --amend -m "new message"  # Change message
```

### Undo Commits
```bash
git reset --soft HEAD~1   # Undo commit, keep changes staged
git reset --mixed HEAD~1  # Undo commit, keep changes unstaged
git reset --hard HEAD~1   # Undo commit, discard changes
```

### Cherry Pick
```bash
git cherry-pick <commit-hash>
git cherry-pick <hash1> <hash2>  # Multiple commits
```

### Stash Changes
```bash
git stash                    # Stash changes
git stash -m "description"   # With message
git stash list               # List stashes
git stash pop                # Apply and remove
git stash apply              # Apply and keep
git stash drop               # Remove stash
```

## Git Log

```bash
git log --oneline -10                    # Last 10 commits, compact
git log --oneline --graph --all          # Visual branch graph
git log --since="1 week ago"             # Last week
git log --author="name"                  # By author
git log --grep="fix"                     # Search message
git log -p                               # Show diffs
git log --stat                           # Show file changes
```

## Git Diff

```bash
git diff                     # Unstaged changes
git diff --staged            # Staged changes
git diff HEAD~3              # Last 3 commits
git diff main..feature       # Between branches
git diff --name-only         # Only file names
```

## Tags

```bash
git tag v1.0.0                           # Lightweight tag
git tag -a v1.0.0 -m "Release 1.0.0"    # Annotated tag
git push origin v1.0.0                   # Push tag
git push origin --tags                   # Push all tags
git tag -d v1.0.0                        # Delete local tag
git push origin :refs/tags/v1.0.0       # Delete remote tag
```

## Clean Up

```bash
git branch -d feature-branch             # Delete merged branch
git branch -D feature-branch             # Force delete branch
git remote prune origin                  # Clean stale remote refs
git gc                                   # Garbage collect
```

## Aliases (add to ~/.gitconfig)

```ini
[alias]
  co = checkout
  br = branch
  ci = commit
  st = status
  lg = log --oneline --graph --all
  last = log -1 HEAD
  unstage = reset HEAD --
  amend = commit --amend --no-edit
```

## PR Best Practices

1. **Small PRs**: Keep changes focused, < 400 lines
2. **Clear title**: Use conventional commit format
3. **Description**: Explain what and why
4. **Tests**: Include relevant tests
5. **Self-review**: Review your own diff first
6. **Screenshots**: For UI changes

## Merge Strategies

```bash
# Merge commit (preserves history)
git merge feature-branch

# Squash merge (single commit)
git merge --squash feature-branch
git commit -m "feat: feature description"

# Rebase merge (linear history)
git rebase main
git checkout main
git merge feature-branch --ff-only
```

## Troubleshooting

### Undo pushed commit
```bash
git revert <commit-hash>
git push
```

### Fix detached HEAD
```bash
git checkout main
# Or save changes:
git checkout -b new-branch
```

### Recover deleted branch
```bash
git reflog  # Find commit hash
git checkout -b recovered-branch <hash>
```

### Resolve merge conflicts
```bash
git status                   # See conflicted files
# Edit files, resolve conflicts
git add <resolved-files>
git commit                   # Complete merge
```
