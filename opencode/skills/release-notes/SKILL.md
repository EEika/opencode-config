---
name: release-notes
description: Generate automated release notes from git history using conventional commits
license: MIT
compatibility:
  - git
  - conventional-commits
metadata:
  version: 1.0.0
  author: OpenCode Skills
  tags: [git, release, changelog, semver, conventional-commits]
---

# Release Notes Generation Skill

Automate release notes generation from git commit history using conventional commits and semantic versioning.

## Gathering Changes

### Get commits since last release
```bash
# Commits since last tag
git log $(git describe --tags --abbrev=0)..HEAD --oneline

# Full commit messages (for BREAKING CHANGE footers)
git log $(git describe --tags --abbrev=0)..HEAD --format="%H %s%n%b"

# List recent tags
git tag --sort=-v:refnum | head -5

# Get last tag (for automation)
git describe --tags --abbrev=0
```

### Get all changes between specific versions
```bash
git log v1.2.0..HEAD --oneline
git log v1.2.0..v1.3.0 --format="%s"
```

## Conventional Commit Grouping

Parse commits and organize by type using these categories:

### 🚀 Features (feat)
- **Pattern**: `feat: ` or `feat(scope): `
- User-facing new functionality
- Triggers **MINOR** version bump (0.X.0)

### 🐛 Bug Fixes (fix)
- **Pattern**: `fix: ` or `fix(scope): `
- User-facing bug repairs
- Triggers **PATCH** version bump (0.0.X)

### ⚡ Performance (perf)
- **Pattern**: `perf: ` or `perf(scope): `
- Performance improvements without functional changes
- Triggers **PATCH** version bump

### 🔧 Maintenance (refactor, chore, build, ci)
- **Patterns**: `refactor:`, `chore:`, `build:`, `ci:`, `test:`
- Internal changes, no user-facing impact
- Usually omitted from user-facing release notes (include in technical changelog)

### 📚 Documentation (docs)
- **Pattern**: `docs: ` or `docs(scope): `
- Documentation-only changes
- Include if significant user documentation added

### ⚠️ BREAKING CHANGES
- **Pattern**: `!` after type (e.g., `feat!:`) OR `BREAKING CHANGE:` in commit body
- Incompatible API changes requiring user action
- Triggers **MAJOR** version bump (X.0.0)
- **MUST** be prominently displayed with migration guidance

## Issue/Ticket Linking

Extract and link ticket references from commit messages:

### Pattern Detection
```regex
# Jira-style: BSBM-123, PROJ-456
[A-Z]{2,10}-\d+

# GitHub issues: #123, GH-456
#\d+|GH-\d+
```

### Link Generation
- **Jira**: `https://yourcompany.atlassian.net/browse/BSBM-123`
- **GitHub**: `https://github.com/owner/repo/issues/123`
- **Linear**: `https://linear.app/team/issue/PROJ-123`

### Commit Message Parsing
```bash
# Extract all ticket references
git log v1.0.0..HEAD --format="%s" | grep -oE '[A-Z]{2,10}-[0-9]+'
```

## Semantic Versioning Guidance

**Given version**: `MAJOR.MINOR.PATCH` (e.g., 2.3.5)

| Change Type | Bump | Example | When |
|-------------|------|---------|------|
| **BREAKING CHANGE** | MAJOR | 2.3.5 → 3.0.0 | Incompatible API changes |
| **feat** | MINOR | 2.3.5 → 2.4.0 | New backward-compatible features |
| **fix, perf** | PATCH | 2.3.5 → 2.3.6 | Backward-compatible bug fixes |
| **docs, chore** | None | 2.3.5 | Internal changes only |

**Pre-1.0.0**: Breaking changes may bump MINOR instead of MAJOR (unstable API)

## Release Notes Template

```markdown
# Release v{VERSION}

**Release Date**: {DATE}
**Git Tag**: `v{VERSION}`

## 🎉 Highlights

{1-3 sentence summary of most important changes}

## ⚠️ BREAKING CHANGES

{If any breaking changes exist}

- **{Short description}** ([{COMMIT_HASH}]({COMMIT_URL}))
  - **Migration**: {How to update existing code}
  - **Affected**: {What APIs/features changed}

See [Migration Guide](#migration-guide-v{VERSION}) below.

## 🚀 Features

- **{Scope}**: {Description} ([{COMMIT_HASH}]({COMMIT_URL})) {TICKET_LINK}
- {Feature description} ([{COMMIT_HASH}]({COMMIT_URL}))

## 🐛 Bug Fixes

- **{Scope}**: {Fix description} ([{COMMIT_HASH}]({COMMIT_URL})) {TICKET_LINK}
- {Fix description} ([{COMMIT_HASH}]({COMMIT_URL}))

## ⚡ Performance

- {Performance improvement description} ([{COMMIT_HASH}]({COMMIT_URL}))

## 📚 Documentation

- {Documentation changes} ([{COMMIT_HASH}]({COMMIT_URL}))

## 🔧 Maintenance

- {Internal changes} ([{COMMIT_HASH}]({COMMIT_URL}))

---

## Migration Guide v{VERSION}

### {Breaking Change Title}

**Before:**
```language
{Old API usage}
```

**After:**
```language
{New API usage}
```

**Rationale**: {Why the change was made}

---

**Full Changelog**: [{PREV_TAG}...v{VERSION}]({COMPARE_URL})
```

## Example Output

```markdown
# Release v2.0.0

**Release Date**: 2026-02-12
**Git Tag**: `v2.0.0`

## 🎉 Highlights

Major release introducing async API support, removing deprecated synchronous methods, and adding real-time notifications.

## ⚠️ BREAKING CHANGES

- **Removed synchronous API methods** ([a1b2c3d](https://github.com/org/repo/commit/a1b2c3d))
  - **Migration**: Replace `.connect()` with `await .connectAsync()`
  - **Affected**: All connection management APIs

See [Migration Guide](#migration-guide-v200) below.

## 🚀 Features

- **api**: Add async/await support for all connection methods ([a1b2c3d](https://github.com/org/repo/commit/a1b2c3d)) [BSBM-245](https://jira.company.com/browse/BSBM-245)
- **notifications**: Real-time event notifications via WebSocket ([e4f5g6h](https://github.com/org/repo/commit/e4f5g6h))
- **auth**: Support OAuth2 PKCE flow ([i7j8k9l](https://github.com/org/repo/commit/i7j8k9l)) [BSBM-312](https://jira.company.com/browse/BSBM-312)

## 🐛 Bug Fixes

- **parser**: Fix memory leak in JSON streaming parser ([m1n2o3p](https://github.com/org/repo/commit/m1n2o3p)) [BSBM-298](https://jira.company.com/browse/BSBM-298)
- **cache**: Prevent race condition in cache invalidation ([q4r5s6t](https://github.com/org/repo/commit/q4r5s6t))

## ⚡ Performance

- Reduce bundle size by 40% through tree-shaking ([u7v8w9x](https://github.com/org/repo/commit/u7v8w9x))

---

## Migration Guide v2.0.0

### Async API Migration

All connection methods are now asynchronous. Update your code to use `async/await`:

**Before:**
```javascript
const client = new Client();
client.connect();
client.query('SELECT * FROM users');
```

**After:**
```javascript
const client = new Client();
await client.connectAsync();
await client.queryAsync('SELECT * FROM users');
```

**Rationale**: Async APIs prevent blocking operations and improve scalability.

---

**Full Changelog**: [v1.5.2...v2.0.0](https://github.com/org/repo/compare/v1.5.2...v2.0.0)
```
