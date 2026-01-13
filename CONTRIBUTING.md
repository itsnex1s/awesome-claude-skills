# Contributing to Claude Code Skills

Thank you for your interest in contributing! This document provides guidelines for contributing new skills or improving existing ones.

## How to Contribute

### Adding a New Skill

1. **Fork the repository**

2. **Create a new skill directory**
   ```bash
   mkdir -p skills/your-skill-name
   ```

3. **Create SKILL.md** with the required format:
   ```yaml
   ---
   name: your-skill-name
   description: Brief description (shown in skill list)
   allowed-tools: Bash, Read, Edit, WebFetch
   user-invocable: true
   ---

   # your-skill-name

   Detailed description and instructions...
   ```

4. **Add documentation** including:
   - Installation instructions
   - Command reference
   - Workflow examples
   - Requirements

5. **Update README.md** to add your skill to the table

6. **Submit a pull request**

### Improving Existing Skills

1. Fork the repository
2. Make your changes
3. Test the changes locally
4. Submit a pull request with a clear description

## Skill Guidelines

### Structure

Each skill should have:
- Clear, concise description
- Installation instructions
- Command reference with examples
- Common workflow examples
- Requirements section
- Notes for edge cases

### Writing Style

- Use clear, imperative language
- Include code examples for all commands
- Document flags and options
- Provide real-world workflow examples
- Keep commands copy-pasteable

### YAML Frontmatter

Required fields:
- `name`: Skill identifier (lowercase, hyphenated)
- `description`: One-line description
- `allowed-tools`: Comma-separated list of Claude Code tools
- `user-invocable`: Usually `true`

### Allowed Tools

Choose only the tools your skill needs:
- `Bash`: Run shell commands
- `Read`: Read files
- `Edit`: Edit files
- `Write`: Write files
- `WebFetch`: Fetch web content
- `WebSearch`: Search the web

## Code of Conduct

- Be respectful and constructive
- Focus on improving the project
- Help others learn

## Questions?

Open an issue if you have questions about contributing.
