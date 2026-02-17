---
name: pr-workflow
description: Create well-structured pull requests for corporate banking .NET development teams
license: MIT
compatibility: opencode
metadata:
  category: git
  workflow: development
  domain: corporate-banking
---

## What I do

Guide you through creating professional, well-structured pull requests for enterprise .NET banking software with multiple repositories. Ensures consistency, traceability, and clear communication across the team.

## PR Title Format

Follow conventional commits pattern with Jira ticket reference:

```
<type>(scope): <description> [TICKET-123]
```

**Examples:**
- `feat(payments): add real-time ACH validation [BSBM-456]`
- `fix(auth): resolve session timeout in mobile app [BSBM-789]`
- `refactor(accounts): simplify balance calculation logic [BSBM-321]`

## Branch Naming Convention

Match your PR title pattern:

```
<type>/<ticket>-<short-description>
```

**Examples:**
- `feat/BSBM-456-ach-validation`
- `fix/BSBM-789-session-timeout`
- `refactor/BSBM-321-balance-calc`

## PR Description Template

```markdown
## Summary
Brief description of what this PR does and why it's needed.

## Changes Made
- List key changes or components modified
- Include important technical decisions
- Note any dependencies or related PRs

## Testing Done
- [ ] Unit tests added/updated (pass locally)
- [ ] Integration tests pass
- [ ] Manual testing performed
- [ ] Tested edge cases: [describe]

## Jira Link
[BSBM-123](https://jira.company.com/browse/BSBM-123)

## Screenshots
<!-- Include if UI changes -->

## Breaking Changes
<!-- List any breaking changes or write "None" -->

## Migration Notes
<!-- Required steps for deployment or write "None" -->
```

## Pre-Submit Checklist

Before creating your PR, verify:

- [ ] **Tests pass locally** - all unit and integration tests green
- [ ] **No new warnings** - build output clean
- [ ] **Self-reviewed the diff** - read every line of your changes
- [ ] **Documentation updated** - README, API docs, inline comments
- [ ] **Tests added/updated** - new code has test coverage
- [ ] **No secrets or sensitive data** - no credentials, keys, tokens
- [ ] **Breaking changes documented** - impact clearly explained
- [ ] **Code follows team conventions** - style, patterns, naming

## PR Size Guidelines

**Target: 200-400 lines changed**

- ✅ **Small (< 200 lines)**: Ideal - fast review, low risk
- ⚠️ **Medium (200-400 lines)**: Acceptable - one focused feature
- ❌ **Large (> 400 lines)**: Split into multiple PRs

**When to split:**
- Multiple unrelated changes
- Feature + refactoring (separate these)
- Can be deployed independently

**Exceptions:**
- Generated code (migrations, client SDKs)
- Large file moves/renames
- Data migrations

## Reviewer Assignment

**Who to request:**
- **Required**: Team lead or designated code owner
- **Optional**: Subject matter expert for the area
- **FYI**: Stakeholders who should be aware

**When to request:**
- After self-review and checklist completion
- When CI/CD checks are passing
- During business hours for urgent reviews

**Review SLA:**
- **Hotfix**: 2-4 hours
- **Standard**: 1 business day
- **Complex**: 2-3 business days

## Complete Examples

### Example 1: Feature Addition

**Title:** `feat(wire-transfer): add SWIFT validation for international transfers [BSBM-1205]`

**Branch:** `feat/BSBM-1205-swift-validation`

**Description:**
```markdown
## Summary
Implements SWIFT/BIC code validation for international wire transfers to prevent invalid routing. Validates format and performs checksum verification per ISO 9362 standard.

## Changes Made
- Added `SwiftValidator` service with format and checksum validation
- Updated `WireTransferRequest` model with `SwiftCode` property
- Added validation to `InternationalTransferController`
- Created comprehensive unit tests for edge cases

## Testing Done
- [x] Unit tests added (20 new tests, all passing)
- [x] Integration tests pass
- [x] Manual testing with sample SWIFT codes from test data
- [x] Tested edge cases: invalid length, bad checksums, malformed codes

## Jira Link
[BSBM-1205](https://jira.company.com/browse/BSBM-1205)

## Breaking Changes
None

## Migration Notes
None - backward compatible addition
```

### Example 2: Bug Fix

**Title:** `fix(accounts): prevent race condition in concurrent balance updates [BSBM-891]`

**Branch:** `fix/BSBM-891-balance-race-condition`

**Description:**
```markdown
## Summary
Fixes race condition where concurrent transactions could result in incorrect account balances. Implements optimistic concurrency control using row versioning.

## Changes Made
- Added `RowVersion` timestamp column to `Accounts` table
- Updated `AccountRepository.UpdateBalance()` to check version
- Added retry logic for concurrency conflicts
- Updated EF Core configuration for concurrency tokens

## Testing Done
- [x] Unit tests for concurrency scenarios (new)
- [x] Integration tests with parallel transactions
- [x] Load testing with 100 concurrent updates
- [x] Verified no balance discrepancies under load

## Jira Link
[BSBM-891](https://jira.company.com/browse/BSBM-891)

## Breaking Changes
None - database migration handles schema change

## Migration Notes
Run migration `20260212_AddAccountRowVersion` before deployment. Zero downtime - backward compatible.
```

### Example 3: Refactoring

**Title:** `refactor(auth): extract JWT token generation into reusable service [BSBM-742]`

**Branch:** `refactor/BSBM-742-jwt-service`

**Description:**
```markdown
## Summary
Extracts duplicated JWT token generation logic into a centralized `JwtTokenService`. Improves testability and consistency across authentication flows.

## Changes Made
- Created `JwtTokenService` with `GenerateToken()` and `ValidateToken()`
- Refactored `AuthenticationController`, `RefreshTokenHandler`, `SsoController`
- Added unit tests for token service
- Removed 3 copies of duplicate logic

## Testing Done
- [x] All existing auth tests pass (no behavior changes)
- [x] New unit tests for `JwtTokenService` (15 tests)
- [x] Manual testing of login, refresh, SSO flows
- [x] Verified token claims identical to previous implementation

## Jira Link
[BSBM-742](https://jira.company.com/browse/BSBM-742)

## Breaking Changes
None - internal refactoring only, no API changes

## Migration Notes
None
```

## When to use me

- Before creating a PR in corporate banking repositories
- When reviewing PR quality and completeness
- When training new team members on PR standards
- As a reference for PR best practices

## How to invoke

When you're ready to create a PR, tell me about your changes:

> "I need to create a PR for adding multi-factor authentication to the login flow. I modified the auth controller, added a new MFA service, and updated the database schema."

I'll help you craft a complete, professional PR following these guidelines.
