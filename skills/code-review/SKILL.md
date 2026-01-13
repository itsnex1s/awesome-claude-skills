---
name: code-review
description: Request and perform code reviews. Guidelines for reviewing code quality, finding bugs, and ensuring best practices.
allowed-tools: Bash, Read, Edit
user-invocable: true
---

# code-review

Guidelines for requesting and performing code reviews effectively.

## Core Principle

**Review early, review often** - catch issues before they escalate.

## When to Request Review

### Mandatory Reviews
- After completing individual tasks in workflows
- After finishing major features
- Before merging to main branch
- After significant refactoring

### Recommended Reviews
- Every 3 tasks in a longer workflow
- When unsure about implementation approach
- After adding new dependencies
- When touching security-sensitive code

## How to Request Review

### Get Commit Context
```bash
# Recent commits
git log --oneline -10

# Diff from main
git diff main..HEAD

# Changed files
git diff --name-only main..HEAD

# Specific commit
git show <commit-hash>
```

### Provide Context
When requesting review, include:
1. What changed and why
2. Areas of concern or uncertainty
3. Testing done
4. Related issues/PRs

## Review Checklist

### Functionality
- [ ] Code does what it's supposed to do
- [ ] Edge cases are handled
- [ ] Error handling is appropriate
- [ ] No obvious bugs or logic errors

### Code Quality
- [ ] Code is readable and self-documenting
- [ ] Functions/methods are focused (single responsibility)
- [ ] No unnecessary complexity
- [ ] DRY - no significant duplication
- [ ] Naming is clear and consistent

### Security
- [ ] No hardcoded secrets
- [ ] Input validation present
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] Auth/permissions checked

### Performance
- [ ] No N+1 queries
- [ ] No unnecessary re-renders (React)
- [ ] Expensive operations are optimized
- [ ] Caching used where appropriate

### Testing
- [ ] Tests cover new functionality
- [ ] Tests cover edge cases
- [ ] Existing tests still pass
- [ ] Test names are descriptive

### Style & Conventions
- [ ] Follows project coding standards
- [ ] Consistent with surrounding code
- [ ] Linting passes
- [ ] TypeScript types are appropriate

## Severity Levels

### 🔴 Critical (Must Fix Now)
- Security vulnerabilities
- Data loss potential
- Crashes or major bugs
- Breaking changes without migration

**Action**: Stop and fix immediately

### 🟡 Important (Fix Before Merge)
- Logic errors
- Missing error handling
- Performance issues
- Missing tests for critical paths

**Action**: Resolve before proceeding

### 🟢 Minor (Can Fix Later)
- Style inconsistencies
- Non-critical improvements
- Documentation gaps
- Refactoring suggestions

**Action**: Create follow-up task or address if quick

## Responding to Feedback

### Prioritization
1. Fix all critical issues immediately
2. Address important issues before advancing
3. Note minor issues for follow-up

### Challenging Feedback
You may push back on feedback if:
- You have technical justification
- The suggestion contradicts project patterns
- The change would introduce more complexity

Explain your reasoning clearly.

## Common Review Comments

### Code Smells
```
- Function too long (>50 lines) - consider splitting
- Too many parameters (>4) - consider options object
- Deep nesting (>3 levels) - consider early returns
- Magic numbers - use named constants
- Commented-out code - remove or explain
```

### React Specific
```
- Missing dependency in useEffect
- Inline function in render causing re-renders
- Missing key prop in lists
- State that should be derived
- Prop drilling - consider context
```

### TypeScript Specific
```
- Using 'any' type - be more specific
- Missing return type annotation
- Non-null assertion (!) without justification
- Type assertion without validation
```

## Review Commands

### GitHub CLI
```bash
# Request review
gh pr edit --add-reviewer username

# View PR diff
gh pr diff <number>

# Check PR status
gh pr checks <number>

# Approve PR
gh pr review --approve

# Request changes
gh pr review --request-changes --body "Please address..."

# Comment
gh pr review --comment --body "Looks good overall..."
```

### Git Commands for Review
```bash
# Checkout PR locally
gh pr checkout <number>

# View changes
git diff main...HEAD

# View specific file changes
git diff main...HEAD -- path/to/file.ts

# View commit messages
git log main..HEAD --oneline
```

## Anti-Patterns to Avoid

### As Reviewer
- ❌ Nitpicking style when linter should catch it
- ❌ Rewriting code to personal preference
- ❌ Blocking on minor issues
- ❌ Vague feedback ("this looks wrong")

### As Author
- ❌ Skipping review because "it's simple"
- ❌ Ignoring critical feedback
- ❌ Proceeding with unresolved important issues
- ❌ Dismissing valid technical feedback
- ❌ Large PRs that are hard to review

## Best Practices

### For Authors
1. Keep PRs small and focused (<400 lines)
2. Self-review before requesting
3. Provide context in PR description
4. Respond to all comments
5. Be open to feedback

### For Reviewers
1. Review promptly (within 24h)
2. Be constructive, not critical
3. Explain the "why" not just "what"
4. Praise good code too
5. Use suggestions feature for fixes

## Integration with Development

### Batching Reviews
- Review every 3 tasks in longer workflows
- Balance thoroughness with velocity
- Don't let reviews pile up

### Automated Checks
- Run linting before review
- Run tests before review
- Use automated code analysis tools

## Attribution

Inspired by [obra/superpowers](https://github.com/obra/superpowers/blob/main/skills/requesting-code-review/SKILL.md)
