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

- **cerebrate** - Strategic intelligence coordinator
- **drone** - Feature implementation and coding tasks
- **overlord** - Tactical executor for multi-step tasks
- **zergling** - Fast reconnaissance and simple tasks
- **ultrathinker** - Deep reasoning and architecture analysis
- **code-reviewer** - Code quality and pattern analysis
