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

- [Collaborate with Claude across Excel, PowerPoint, Word and Outlook](https://claude.com/blog/collaborate-with-claude-across-excel-powerpoint-word-and-outlook) — analyzed 2026-05-10
- [New in Claude Managed Agents: dreaming, outcomes, multiagent orchestration](https://claude.com/blog/new-in-claude-managed-agents) — analyzed 2026-05-10
- [Deploying Claude across financial services](https://claude.com/blog/deploying-claude-across-financial-services) — analyzed 2026-05-10
- [How a non-technical PM shipped a stress-management app in 6 weeks](https://claude.com/blog/how-a-non-technical-project-manager-built-and-shipped-a-stress-management-app-with-claude-code-in-six-weeks) — analyzed 2026-05-10
- [Building AI agents for the enterprise](https://claude.com/blog/building-ai-agents-for-the-enterprise) — analyzed 2026-05-10
- [Claude Security is now in public beta](https://claude.com/blog/claude-security-public-beta) — analyzed 2026-05-10
- [Lessons from building Claude Code: Prompt caching is everything](https://claude.com/blog/lessons-from-building-claude-code-prompt-caching-is-everything) — analyzed 2026-05-10
- [How Kepler built verifiable AI for financial services with Claude](https://claude.com/blog/how-kepler-built-verifiable-ai-for-financial-services-with-claude) — analyzed 2026-05-10
- [Deploying agentic AI across the enterprise with Claude Cowork](https://claude.com/blog/new-guide-deploying-claude-across-the-enterprise-with-claude-cowork) — analyzed 2026-05-10
- [Claude API skill now in CodeRabbit, JetBrains, Resolve AI, Warp](https://claude.com/blog/claude-api-skill) — analyzed 2026-05-10
- [Product development in the agentic era](https://claude.com/blog/product-development-in-the-agentic-era) — analyzed 2026-05-10
- [Onboarding Claude Code like a new developer: Lessons from 17 years of development](https://claude.com/blog/onboarding-claude-code-like-a-new-developer-lessons-from-17-years-of-development) — analyzed 2026-05-10
- [New connectors in Claude for everyday life](https://claude.com/blog/connectors-for-everyday-life) — analyzed 2026-05-10
- [Built-in memory for Claude Managed Agents](https://claude.com/blog/claude-managed-agents-memory) — analyzed 2026-05-10
- [Building agents that reach production systems with MCP](https://claude.com/blog/building-agents-that-reach-production-systems-with-mcp) — analyzed 2026-05-10
- [Claude Managed Agents: get to production 10x faster](https://claude.com/blog/claude-managed-agents) — analyzed 2026-04-10
- [Harnessing Claude's intelligence](https://claude.com/blog/harnessing-claudes-intelligence) — analyzed 2026-04-03
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

Last fetched: 2026-05-10
Post links found: 15
