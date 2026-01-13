---
name: changelog-generator
description: Generate user-friendly changelogs from git commits. Transforms technical commits into clear release notes.
allowed-tools: Bash, Read, Edit
user-invocable: true
---

# changelog-generator

Transform technical git commits into polished, user-friendly changelogs.

## When to Use

- Preparing release notes for a new version
- Creating weekly/monthly product updates
- Writing changelog entries for app stores
- Generating internal release documentation
- Maintaining a public changelog page

## How It Works

1. **Scan Git History**: Analyze commits from time period or between versions
2. **Categorize Changes**: Group into features, fixes, improvements
3. **Translate**: Convert developer commits into user-friendly language
4. **Format**: Create clean, structured changelog entries
5. **Filter Noise**: Exclude internal commits (refactoring, tests, etc.)

## Usage

### Basic Usage
```
Create a changelog from commits since last release
```

```
Generate changelog for commits from the past week
```

```
Create release notes for version 2.5.0
```

### With Date Range
```
Create changelog for commits between March 1 and March 15
```

### With Tag Range
```
Generate changelog for commits since v2.4.0
```

## Git Commands

### Get Commits Since Tag
```bash
git log v1.0.0..HEAD --oneline
git log v1.0.0..HEAD --pretty=format:"%h %s" --no-merges
```

### Get Commits by Date
```bash
git log --since="1 week ago" --oneline
git log --since="2026-03-01" --until="2026-03-15" --oneline
```

### Get Commits with Details
```bash
git log --since="1 week ago" --pretty=format:"- %s (%h)" --no-merges
```

### Group by Author
```bash
git shortlog -sn --since="1 month ago"
```

## Output Format

### Standard Changelog
```markdown
# Changelog

## [2.5.0] - 2026-03-15

### Added
- **Team Workspaces**: Create separate workspaces for different projects
- **Keyboard Shortcuts**: Press ? to see all available shortcuts

### Changed
- **Faster Sync**: Files now sync 2x faster across devices
- **Better Search**: Search now includes file contents

### Fixed
- Fixed issue where large images wouldn't upload
- Resolved timezone confusion in scheduled posts

### Security
- Updated dependencies to patch vulnerability CVE-2026-XXXX
```

### User-Friendly Format
```markdown
# Updates - Week of March 15, 2026

## ✨ New Features
- **Team Workspaces**: Create separate workspaces for different
  projects. Invite team members and keep everything organized.

## 🔧 Improvements
- **Faster Sync**: Files now sync 2x faster across devices
- **Better Search**: Search now includes file contents, not just titles

## 🐛 Fixes
- Fixed issue where large images wouldn't upload
- Resolved timezone confusion in scheduled posts
```

## Commit Categories

### Include in Changelog
```
feat:     → ✨ New Features / Added
fix:      → 🐛 Bug Fixes / Fixed
perf:     → ⚡ Performance / Changed
security: → 🔒 Security
docs:     → 📚 Documentation (if user-facing)
```

### Exclude from Changelog
```
chore:    Internal maintenance
refactor: Code restructuring
test:     Test changes
ci:       CI/CD changes
style:    Code formatting
build:    Build system
```

## Automated Generation

### Using git-cliff
```bash
# Install
cargo install git-cliff

# Generate changelog
git cliff --output CHANGELOG.md
git cliff --latest --strip header  # Only latest version
git cliff -t v1.0.0..v2.0.0        # Between tags
```

### cliff.toml
```toml
[changelog]
header = "# Changelog\n\n"
body = """
{% for group, commits in commits | group_by(attribute="group") %}
### {{ group | upper_first }}
{% for commit in commits %}
- {{ commit.message | upper_first }} ({{ commit.id | truncate(length=7, end="") }})\
{% endfor %}
{% endfor %}
"""
trim = true

[git]
conventional_commits = true
filter_unconventional = true
commit_parsers = [
  { message = "^feat", group = "Features" },
  { message = "^fix", group = "Bug Fixes" },
  { message = "^perf", group = "Performance" },
  { message = "^doc", group = "Documentation" },
  { message = "^refactor", skip = true },
  { message = "^chore", skip = true },
  { message = "^test", skip = true },
]
```

### Using GitHub CLI
```bash
# Generate release notes from PRs
gh release create v1.0.0 --generate-notes

# View generated notes
gh release view v1.0.0
```

## Best Practices

1. **Write for users, not developers**
   - Bad: "Refactor auth module to use new SDK"
   - Good: "Login is now faster and more reliable"

2. **Lead with benefits**
   - Bad: "Added caching layer"
   - Good: "Pages load 50% faster"

3. **Be specific about fixes**
   - Bad: "Fixed bug"
   - Good: "Fixed issue where uploads failed on slow connections"

4. **Group related changes**
   - Combine similar commits into single entries

5. **Include context when helpful**
   - Link to docs for new features
   - Note breaking changes prominently

## Templates

### App Store Update
```markdown
What's New in v2.5:

• Team Workspaces - Collaborate with your team in shared spaces
• Keyboard Shortcuts - Navigate faster with hotkeys (press ? for help)
• Performance - 2x faster sync and improved search

Bug Fixes:
• Fixed image upload issues
• Corrected timezone display
```

### Email Newsletter
```markdown
## What's New This Week

Hey there! Here's what we shipped:

**🎉 Team Workspaces**
You asked, we delivered! Create separate workspaces for different projects and invite team members.

**⚡ Speed Improvements**
Files now sync 2x faster. Search is smarter too - it now looks inside your files.

**🐛 Bug Squashing**
Fixed that annoying image upload issue and sorted out timezone confusion.

Questions? Reply to this email!
```

## Attribution

Inspired by [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills)
