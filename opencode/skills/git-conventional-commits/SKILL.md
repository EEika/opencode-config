---
name: git-conventional-commits
description: Write Git commit messages following the Conventional Commits specification
license: MIT
compatibility: opencode
metadata:
  category: git
  workflow: development
---

## What I do

Help you write clear, structured Git commit messages following the [Conventional Commits](https://www.conventionalcommits.org/) specification v1.0.0.

## Commit Message Format

Every commit message should follow this structure:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Types

Use these commit types:

| Type | Description |
|------|-------------|
| `feat` | A new feature for the user |
| `fix` | A bug fix |
| `docs` | Documentation only changes |
| `style` | Changes that don't affect code meaning (formatting, whitespace) |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or correcting tests |
| `build` | Changes to build system or external dependencies |
| `ci` | Changes to CI configuration files and scripts |
| `chore` | Other changes that don't modify src or test files |
| `revert` | Reverts a previous commit |

## Rules

1. **Subject line** (first line):
   - Use imperative mood ("add" not "added" or "adds")
   - Don't capitalize the first letter after the colon
   - No period at the end
   - Maximum 72 characters total
   - Format: `type(scope): description` or `type: description`

2. **Scope** (optional):
   - Noun describing section of codebase (e.g., `api`, `auth`, `ui`, `db`)
   - Use lowercase
   - Can be omitted if change is global or affects many areas

3. **Body** (optional):
   - Separated from subject by blank line
   - Explain *what* and *why*, not *how*
   - Wrap at 72 characters

4. **Footer** (optional):
   - `BREAKING CHANGE: <description>` for breaking changes
   - Reference issues: `Fixes #123`, `Closes #456`
   - Co-authors: `Co-authored-by: Name <email>`

## Examples

### Simple feature
```
feat: add user profile page
```

### Feature with scope
```
feat(auth): implement OAuth2 login flow
```

### Bug fix with body
```
fix(api): handle null response from payment gateway

The payment gateway occasionally returns null instead of an error
object when the service is degraded. This adds proper null checking
to prevent crashes.

Fixes #234
```

### Breaking change
```
feat(api)!: change authentication to use JWT tokens

BREAKING CHANGE: API now requires JWT tokens instead of session cookies.
Clients must update their authentication flow.
```

### Docs update
```
docs: update README with installation instructions
```

### Multiple footers
```
fix(database): resolve connection pool exhaustion

Connections were not being properly returned to the pool after
query timeouts, causing gradual exhaustion under load.

Fixes #567
Reviewed-by: Jane Doe
Co-authored-by: John Smith <john@example.com>
```

## When to use me

- When you need to write a commit message
- When reviewing commit messages for consistency
- When you want to understand what changes were made and structure them properly
- Before running `git commit`

## How to invoke

After staging your changes, describe what you changed and I'll help you craft a proper conventional commit message. For example:

> "I added a new login button to the header and fixed a bug where users couldn't log out"

I'll respond with properly formatted commit message(s) you can use.
