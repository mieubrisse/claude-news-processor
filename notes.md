Setup Context
=============

### AgenC Configuration

- Orchestrates multiple Claude Code agents in isolated missions (ephemeral workspaces with own git clone, config, and tmux window)
- Missions auto-commit and push — unpushed work is lost
- Solo repos: commit directly to default branch. Collaborative repos: feature branches
- Can spawn new missions for cross-repo work; headed (visible) by default
- Repo library at ~/.agenc/repos for read-only reference across missions

### Skills

Extensive skill library covering:
- **Process:** brainstorm, systematic-debugging, test-driven-development, writing-plans, executing-plans, verification-before-completion, receiving-code-review, requesting-code-review, subagent-driven-development
- **Engineering:** software-engineer (mandatory for all code work), go-coding, typescript-coding, bash-coding, python-coding, prompt-engineer, claude-skill-management
- **Domain:** entrepreneurship-advice, kurtosis (startup lessons), coaching-journal, personal-journal, ux-designer, content-brainstormer, substack-writer, instagram-video-producer
- **Meta:** using-superpowers (skill discovery), self-refine, claude-code-configuration, agenc-engineer

### Hooks

- PreToolUse hook: `remove-redundant-git-c.sh` strips redundant `git -C` targeting current directory

### MCP Servers

- Todoist (comprehensive task/project management integration)

### Coding Conventions

- Languages: Go, TypeScript, Bash, Python
- Markdown: underline-style h1 (`===`) and h2 (`---`), no `#`/`##`
- Git: single-line commit messages, no Co-Authored-By, separate git operations (never chain with `&&`)
- No emoji unless explicitly requested
- Mandatory `/software-engineer` invocation before any code work
- Mandatory language-specific skill invocation (go-coding, typescript-coding, etc.)

### Workflows

- Request refinement protocol: complexity-scaled questioning before execution
- Plan mode only when user explicitly asks
- `dangerouslyDisableSandbox: true` used when command is permitted by settings.json but blocked by sandbox layer
- GitHub username: mieubrisse
- "My assistant" = mieubrisse/todoist-manager repo (spawn mission to delegate tasks)

Processed Posts
===============

- [Claude now creates interactive charts, diagrams and visualizations](https://claude.com/blog/claude-builds-visuals) — analyzed 2026-04-01
- [How enterprises are building AI agents in 2026](https://claude.com/blog/how-enterprises-are-building-ai-agents-in-2026) — analyzed 2026-04-01
- [Improving frontend design through Skills](https://claude.com/blog/improving-frontend-design-through-skills) — analyzed 2026-04-01
- [Building AI agents for financial services](https://claude.com/blog/building-ai-agents-in-financial-services) — analyzed 2026-04-01
- [Claude Code on the web](https://claude.com/blog/claude-code-on-the-web) — analyzed 2026-04-01
- [Claude and Slack](https://claude.com/blog/claude-and-slack) — analyzed 2026-04-01
- [Piloting Claude in Chrome](https://claude.com/blog/claude-for-chrome) — analyzed 2026-04-01
- [How Anthropic teams use Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) — analyzed 2026-04-01
- [Claude can now connect to your world](https://claude.com/blog/integrations) — analyzed 2026-04-01
- [Introducing the Max Plan](https://claude.com/blog/max-plan) — analyzed 2026-04-01

Blog Health
===========

Last fetched: 2026-04-01
Post links found: 10
