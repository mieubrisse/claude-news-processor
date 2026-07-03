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

- [Getting started with loops](https://claude.com/blog/getting-started-with-loops) — analyzed 2026-07-03
- [Building effective human-agent teams](https://claude.com/blog/building-effective-human-agent-teams) — analyzed 2026-07-03
- [Agent identity in Claude Tag: a new access model](https://claude.com/blog/agent-identity-access-model) — analyzed 2026-07-03
- [Claude in Microsoft Foundry is now generally available](https://claude.com/blog/claude-in-microsoft-foundry) — analyzed 2026-07-03
- [Giving admins more visibility and control over Claude spend](https://claude.com/blog/giving-admins-more-visibility-and-control-over-claude-usage-and-spend) — analyzed 2026-07-03
- [Introducing the Claude apps gateway for Amazon Bedrock and Google Cloud](https://claude.com/blog/introducing-the-claude-apps-gateway) — analyzed 2026-07-03
- [The full Claude Desktop experience on AWS, Google Cloud, and Microsoft Foundry](https://claude.com/blog/the-full-claude-desktop-experience-on-aws-google-cloud-and-microsoft-foundry) — analyzed 2026-07-03
- [Steering Claude Code: skills, hooks, rules, subagents and more](https://claude.com/blog/steering-claude-code-skills-hooks-rules-subagents-and-more) — analyzed 2026-06-19
- [Claude Code now supports artifacts](https://claude.com/blog/artifacts-in-claude-code) — analyzed 2026-06-19
- [Claude Design now stays on brand for daily work](https://claude.com/blog/claude-design-stays-on-brand-for-daily-work) — analyzed 2026-06-19
- [Meet the winners of the Built with Opus 4.7 Claude Code hackathon](https://claude.com/blog/meet-the-winners-of-built-with-opus-4-7-claude-code-hackathon) — analyzed 2026-06-19
- [Meet the winners of our Claude Opus 4.8 Build Day hackathon](https://claude.com/blog/meet-the-winners-of-our-claude-opus-4-8-build-day-hackathon) — analyzed 2026-06-19
- [Centrally manage authorization for MCP connectors](https://claude.com/blog/enterprise-managed-auth) — analyzed 2026-06-19
- [Workload Identity Federation now generally available on the Claude Platform](https://claude.com/blog/workload-identity-federation) — analyzed 2026-06-19
- [The evolution of agentic surfaces: building with Claude Managed Agents](https://claude.com/blog/building-with-claude-managed-agents) — analyzed 2026-06-12
- [New in Claude Managed Agents: run agents on a schedule and store environment variables in vaults](https://claude.com/blog/whats-new-in-claude-managed-agents) — analyzed 2026-06-12
- [Building intelligent apps for Apple platforms with Claude in the Foundation Models framework](https://claude.com/blog/claude-for-foundation-models) — analyzed 2026-06-12
- [Observability for developers building connectors](https://claude.com/blog/observability-for-developers-building-connectors) — analyzed 2026-06-12
- [The Claude Cowork product guide](https://claude.com/blog/the-claude-cowork-product-guide) — analyzed 2026-06-12
- [How one Anthropic seller rebuilt his team's workflows with Claude Code](https://claude.com/blog/how-anthropic-uses-claude-gtm-engineering) — analyzed 2026-06-12
- [How Anthropic enables self-service data analytics with Claude](https://claude.com/blog/how-anthropic-enables-self-service-data-analytics-with-claude) — analyzed 2026-06-05
- [Lessons from building Claude Code: How we use skills](https://claude.com/blog/lessons-from-building-claude-code-how-we-use-skills) — analyzed 2026-06-05
- [Best practices for getting started with Claude Cowork](https://claude.com/blog/best-practices-for-getting-started-with-claude-cowork) — analyzed 2026-06-05
- [Running an AI-native engineering org](https://claude.com/blog/running-an-ai-native-engineering-org) — analyzed 2026-06-05
- [A harness for every task: Dynamic workflows in Claude Code](https://claude.com/blog/a-harness-for-every-task-dynamic-workflows-in-claude-code) — analyzed 2026-06-05
- [Introducing dynamic workflows in Claude Code](https://claude.com/blog/introducing-dynamic-workflows-in-claude-code) — analyzed 2026-05-29
- [Using LLMs to secure source code](https://claude.com/blog/using-llms-to-secure-source-code) — analyzed 2026-05-29
- [How CodeRabbit used Claude to build an agent orchestration system](https://claude.com/blog/how-coderabbit-used-claude-to-build-an-agent-orchestration-system) — analyzed 2026-05-29
- [Zero Trust for AI agents](https://claude.com/blog/zero-trust-for-ai-agents) — analyzed 2026-05-29
- [Code w/ Claude London 2026: Rethinking how we build](https://claude.com/blog/code-w-claude-london-2026-rethinking-how-we-build) — analyzed 2026-05-29
- [How Anthropic's finance team uses Claude to shape the narrative behind the numbers](https://claude.com/blog/how-anthropics-finance-team-uses-claude-to-shape-the-narrative-behind-the-numbers) — analyzed 2026-05-29
- [New in Claude Managed Agents: self-hosted sandboxes and MCP tunnels](https://claude.com/blog/claude-managed-agents-updates) — analyzed 2026-05-22
- [Claude now works with more security and compliance tools](https://claude.com/blog/compliance-api-security-partners) — analyzed 2026-05-22
- [How our partners are putting Opus to work for cybersecurity](https://claude.com/blog/how-our-partners-are-putting-opus-to-work-for-cybersecurity) — analyzed 2026-05-22
- [Using Claude Code: The unreasonable effectiveness of HTML](https://claude.com/blog/using-claude-code-the-unreasonable-effectiveness-of-html) — analyzed 2026-05-22
- [How an Anthropic sales leader uses Claude Cowork to run a 4,000-account book](https://claude.com/blog/how-an-anthropic-sales-leader-uses-claude-cowork-to-run-a-4-000-account-book) — analyzed 2026-05-22
- [Deploying Claude across the legal industry](https://claude.com/blog/deploying-claude-across-the-legal-industry) — analyzed 2026-05-22
- [Redesigning Claude Code on desktop for parallel agents](https://claude.com/blog/claude-code-desktop-redesign) — analyzed 2026-05-15
- [Preparing your security program for AI-accelerated offense](https://claude.com/blog/preparing-your-security-program-for-ai-accelerated-offense) — analyzed 2026-05-15
- [The founder's playbook: Building an AI-native startup](https://claude.com/blog/the-founders-playbook) — analyzed 2026-05-15
- [How Claude Code works in large codebases: Best practices and where to start](https://claude.com/blog/how-claude-code-works-in-large-codebases-best-practices-and-where-to-start) — analyzed 2026-05-15
- [Best practices for computer and browser use with Claude](https://claude.com/blog/best-practices-for-computer-and-browser-use-with-claude) — analyzed 2026-05-15
- [Code w/ Claude SF 2026: Building on the AI exponential](https://claude.com/blog/code-w-claude-sf-2026-sf) — analyzed 2026-05-15
- [Claude for the legal industry](https://claude.com/blog/claude-for-the-legal-industry) — analyzed 2026-05-15
- [How Anthropic's cybersecurity team built a threat detection platform with Claude Code](https://claude.com/blog/how-anthropic-uses-claude-cybersecurity) — analyzed 2026-05-15
- [Agent view in Claude Code](https://claude.com/blog/agent-view-in-claude-code) — analyzed 2026-05-15
- [Introducing the Claude Platform on AWS](https://claude.com/blog/claude-platform-on-aws) — analyzed 2026-05-15
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

Last fetched: 2026-07-03
Post links found: 24
