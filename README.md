# opencode-config

Custom agent configurations for [opencode](https://github.com/opencode-ai/opencode).

## Setup (macOS)

1. Clone this repository:
   ```bash
   git clone <repo-url>
   cd opencode-config
   ```

2. Create a symlink to your opencode config directory:
   ```bash
   ln -s $(pwd)/opencode ~/.config/opencode
   ```

   Or copy the files directly:
   ```bash
   cp -r opencode ~/.config/opencode
   ```

3. Restart opencode to load the custom agent configurations.

## Included Agents

### Primary Agent
- **cerebrate** — Strategic intelligence coordinator that delegates to sub-agents

### Reconnaissance & Research
- **zergling** — Fast, lightweight scout for quick reconnaissance and simple tasks
- **hydralisk** — Comprehensive code exploration, multi-pattern searches, and complex research

### Implementation
- **drone** — Efficient worker for general coding tasks and feature implementation

### Specialized Analysis
- **abathur** — Deep architectural analysis and design decisions
- **code-reviewer** — Code quality and pattern analysis (C# / .NET focused)
- **backlog-planner** — Agile backlog planning and sprint organization with Jira integration

### Coordination & Delivery
- **overmind** — Parallel coordination across git worktrees
- **nydus** — Crafting conventional commits and creating GitHub pull requests

## Included Skills

- **dotnet-solution-navigator** — Navigate and understand .NET solution architecture with Clean Architecture patterns
- **git-conventional-commits** — Write Git commit messages following the Conventional Commits specification
- **pr-workflow** — Create well-structured pull requests for corporate banking .NET development teams
- **release-notes** — Generate automated release notes from git history using conventional commits
- **zeebe-workflow-patterns** — Camunda/Zeebe workflow orchestration patterns for .NET/C# projects
