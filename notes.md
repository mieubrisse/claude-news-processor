Setup Context
=============

### AgenC Configuration

- Orchestrates multiple Claude Code agents in isolated missions (ephemeral workspaces with own git clone, config, and tmux window)
- Missions auto-commit and push — unpushed work is lost
- Solo repos: commit directly to default branch. Collaborative repos: feature branches
- Can spawn new missions for cross-repo work; headed (visible) by default
- Repo library at ~/.agenc/repos for read-only reference across missions
- Writeable-copy repos (dotfiles, exobrain) are daemon-persisted — agents must NOT git add/commit/push there
- Cron fleet includes: claude-news-processor (this repo), build-weekly-plan, daily-state-summary, flight-watcher, hn-daily-pull, exobrain-update, verify-workspace-mcp-denylist, arpan-claude-optimization-suggestions
- **Model: varies per mission — do not assume. Re-read settings each run; these numbers drift fast.** As of 2026-10-02, `settings.json` line 690 sets the session default to `"model": "fable"` (an ALIAS — this run could NOT determine whether it resolves to `claude-fable-5` or `claude-fable-5-1`, and the difference is 4x on cache reads: $1.00 vs $0.25/MTok. Resolve before costing anything). Prior runs recorded `"opus[1m]"` (2026-09-11) and `"Fable 5"` — both stale. This mission ran as Opus 5 per its own system prompt, so the cron fleet and the settings default are NOT necessarily the same model.
- `settings.json` line 1396 sets `"effortLevel": "xhigh"` globally (was line 1218 on 2026-09-11). **48** entries hard-pin `"model": "claude-opus-5"` (was 24 on 2026-09-11 — the count is growing) — all Guidance-Edit Gate judge hooks, each `timeout: 600`. **Unresolved and worth settling:** whether a model-pinned hook inherits the global `effortLevel` or falls back to the model's own default. It decides whether a model swap is cost-only or also a quality change, since Opus 5.5 defaults to `medium` where Opus 5 defaulted to `high`.
- **Model lineup as of 2026-10-02** (verified against platform.claude.com docs, not inferred): current = Claude Opus 5.5 `claude-opus-5-5` ($4/$20, cache read $0.20 at 0.05x, default effort **`medium`**), Claude Sonnet 5.5 `claude-sonnet-5-5` ($2/$10, launched 2026-09-28), Claude Fable 5.1 `claude-fable-5-1` ($10/$50, cache read $0.25 at 0.025x), Claude Haiku 4.5. **Opus 5 (`claude-opus-5`) is now listed LEGACY.** Docs guidance: "start with Claude Opus 5.5 for most workloads; use Claude Fable 5.1 for demanding reasoning and long-horizon agentic work, or when your evals on Claude Opus 5.5 at higher effort still fall short." Opus 5.5 fast mode is $8/$40 (vs Opus 5's $10/$50).
- Installed Claude Code CLI: **2.1.274** (Homebrew cask; `brew info --cask claude-code` reports 2.1.274 as latest available, so Homebrew is at parity as of 2026-10-02). Its bundled `claude-api` skill ships subcommands `migrate`, `managed-agents-onboard`, `prompt-audit`, `upgrade`, and now also `cost-optimize`, `build-eval`, `hillclimb` (all present in the 2.1.274 skill text; the latter three were absent in 2.1.236). `prompt-audit` targets prompts in API-calling application code, not CLAUDE.md/SKILL.md guidance files.
- **STALE BUNDLED SKILL (noted 2026-10-02):** the CLI's bundled `claude-api` skill carries a model table explicitly "cached: 2026-06-24" that omits Opus 5.5 and Sonnet 5.5 entirely, and instructs in bold "**ALWAYS use `claude-opus-5`**" — now a legacy model. Any agent of Kevin's writing Claude API code will default to legacy unless told otherwise. Not Kevin's file to fix (it ships with the CLI), but worth knowing; the skill does say to query the Models API for live lookups.
- **MODS REQUIRE CLI v2.1.287+** — Kevin is on 2.1.274, so mods are unreachable on the Homebrew channel as of 2026-10-02. The 2.1.274 binary contains partial scaffolding (`registerMod`, `sec-default` strings present) but the docs set the floor at 2.1.287. Re-check Homebrew each run; this gates the whole mods opportunity.
- Harness includes Workflow tool (multi-agent orchestration scripts), Agent tool subagents, and an advisor tool (stronger-reviewer consult)

### Skills

100+ skill library. Major clusters:

- **Meta/config:** cc-configuration umbrella (cc-skill-management, cc-rules, cc-settings-editing, cc-mcp-editing), prompt-engineer, structural-fix, self-refine, exobrain, master-information-architecture, kevin-decision-policy
- **Process:** diamond-cascade, brainstorm, complexity-estimator, question-burndown, superpowers pack (TDD, systematic-debugging, verification-before-completion, etc.)
- **Engineering:** software-engineer (mandatory for code work), go/typescript/bash/python-coding, beads-system, agenc-cron-loops
- **Content production:** vr-content-* verifier fleet (icp-reviewer, source-verifier, external-fact-verifier, concreteness-reviewer), writing-proofreader, compressibility-audit, vr-instagram-* pipelines, vr-newsletter-production, substack-writer, tech-landing-page-creation-pipeline
- **Life ops:** todoist-system, process-todoist-inbox, google-calendar-management, personal-journal, flight-planning, notion-crm

### Hooks & Guards

- Guidance-Edit Gate: PreToolUse judge reviews every Write/Edit to guidance files (CLAUDE.md, SKILL.md, rules, commands, agents) against prompt-engineer doctrine; blocks with cited findings
- block-direct-beads-writes.sh (no direct .beads/*.jsonl|*.db mutation), bd unsandbox hook (loopback TCP)
- block-workspace-mcp-destructive-actions.sh + monthly verify-workspace-mcp-denylist check-loop (the canonical loop/check-loop pair)
- dcg destructive-command guard on Bash (e.g., blocks `rm -rf` on home-rooted paths — observed live this run)
- Shell snapshot aliases basic POSIX tools (`ls` etc.) — use `command <tool>` as discriminator when flag-parsing errors appear

### MCP Servers

workspace-mcp (Google Workspace, account k@kevintoday.com), hevy (workouts), grain (meetings), canva, deepwiki, polar, meta-ads, posthog, plus plugin servers (resend, vercel, context7). Todoist via `td` CLI, Notion via `notion` CLI, beads via `bd` CLI.

### Coding Conventions

- Languages: Go, TypeScript, Bash, Python
- Markdown: underline-style h1 (`===`) and h2 (`---`), no `#`/`##`
- Git: single-line commit messages; ONLY permitted trailer is `AgenC mission: <uuid>`; no Co-Authored-By or other attribution (overrides harness defaults)
- No emoji unless explicitly requested
- Mandatory `/software-engineer` + language-skill invocation before code work

### Environment Gotchas (this repo's runs)

- **git push over SSH**: broken on the 2026-07-17 run (global `insteadOf` rewrite → broken `nc` proxy; workaround was `GIT_CONFIG_GLOBAL=/dev/null` + HTTPS token push) but worked plainly on 2026-08-28. If a push fails with connection-class errors, reach for that workaround before diagnosing anything else, and verify with `git log origin/main -1`.
- WebFetch works; Bash network access is sandboxed. `agenc` CLI works at top level.
- Kevin's assistant is Erika Mioshi (per global CLAUDE.md /erika); handoff via the Kevin/Erika Work Tracker.

Processed Posts
===============

- [Customize Claude Code with mods](https://claude.com/blog/claude-code-mods) — analyzed 2026-10-02
- [Coding sessions are longer and use more context. Claude Opus 5.5 is built with that in mind.](https://claude.com/blog/claude-opus-5-5-built-for-coding-sessions-that-use-more-context) — analyzed 2026-10-02
- [Build plugins for Claude](https://claude.com/blog/build-plugins-for-claude) — analyzed 2026-10-02
- [Claude Marketplace: one place to discover plugins, agents, and services from our partners](https://claude.com/blog/claude-marketplace) — analyzed 2026-10-02
- [Claude Cowork and chat are now one Claude](https://claude.com/blog/cowork-is-now-claude) — analyzed 2026-10-02
- [Projects redesigned: from folder to conversation](https://claude.com/blog/projects-redesigned) — analyzed 2026-10-02
- [Agentic coding is straining CI. Here's how we scaled test impact analysis at Anthropic](https://claude.com/blog/agentic-coding-is-straining-ci-heres-how-we-scaled-test-impact-analysis-at-anthropic) — analyzed 2026-10-02 (found ONLY via category sweep, not the index)
- [How to prepare for AI-driven code modernization projects](https://claude.com/blog/how-to-prepare-for-ai-driven-code-modernization-projects) — analyzed 2026-10-02
- [Working at the frontier: How Balyasny Asset Management evaluates and governs Claude Fable 5](https://claude.com/blog/working-at-the-frontier-how-balyasny-asset-management-evaluates-and-governs-claude-fable-5) — analyzed 2026-10-02
- [How Anthropic's sales team rebuilt inbound with Claude Managed Agents](https://claude.com/blog/how-anthropics-sales-team-rebuilt-inbound-with-claude-managed-agents) — analyzed 2026-10-02
- [Giving companies more control over their AI agents, with NVIDIA](https://claude.com/blog/giving-companies-more-control-over-their-ai-agents-with-nvidia) — analyzed 2026-10-02
- [Agents you can coach: how Asana builds human-agent teams with Claude](https://claude.com/blog/agents-you-can-coach-how-asana-builds-human-agent-teams-with-claude) — analyzed 2026-10-02
- [Claude for Government is now generally available](https://claude.com/blog/claude-for-government-is-now-generally-available) — analyzed 2026-10-02
- [Claude Tag now supports personal connectors in channels](https://claude.com/blog/claude-tag-now-supports-personal-connectors-in-channels) — analyzed 2026-10-02
- [How CodeRabbit, Power Digital, and ThoughtSpot scale with Snowflake and Vercel on Claude Marketplace](https://claude.com/blog/how-coderabbit-power-digital-and-thoughtspot-scale-with-snowflake-and-vercel-on-claude-marketplace) — analyzed 2026-10-02
- [Bringing Salesforce into Claude](https://claude.com/blog/salesforce-in-claude) — analyzed 2026-10-02
- [Claude for Small Business launches new workflows, integrations, and training programs](https://claude.com/blog/claude-for-small-business-launches-new-workflows-integrations-and-training-programs) — analyzed 2026-10-02 (found ONLY via category sweep)
- [Claude for Financial Advisors](https://claude.com/blog/claude-for-financial-advisors) — analyzed 2026-10-02 (found ONLY via category sweep)
- [Barclays scales Claude to upgrade operations and improve client experience](https://www.anthropic.com/news/barclays-scales-claude) — analyzed 2026-10-02 (anthropic.com/news)
- [Claude discovers a novel enzyme system with CRISPR-like repeats](https://www.anthropic.com/news/claude-discovers-novel-enzyme-system) — analyzed 2026-10-02 (anthropic.com/news)
- [Partnering with Accenture on embedded evaluation](https://www.anthropic.com/news/accenture-embedded-evaluation) — analyzed 2026-10-02 (anthropic.com/news)
- [Introducing the Life Sciences Verification Program](https://www.anthropic.com/news/life-sciences-verification-program) — analyzed 2026-10-02 (anthropic.com/news)
- [Developing Enterprise Frontier Safeguards with our customers](https://www.anthropic.com/news/enterprise-frontier-safeguards) — analyzed 2026-10-02 (anthropic.com/news)
- [Improving our alignment and security efforts](https://www.anthropic.com/news/improving-alignment-security-efforts) — analyzed 2026-10-02 (anthropic.com/news)
- [Previewing the Model Hardware Standard](https://www.anthropic.com/news/model-hardware-standard-research-preview) — analyzed 2026-10-02 (anthropic.com/news)
- [Expanding our support for scientists](https://www.anthropic.com/news/expanding-support-for-scientists) — analyzed 2026-10-02 (anthropic.com/news)
- [Funding better evaluations of AI's impact on wellbeing](https://www.anthropic.com/news/wellbeing-research-grants) — analyzed 2026-10-02 (anthropic.com/news)
- [How Claude's text watermark works](https://www.anthropic.com/news/claude-text-watermark) — analyzed 2026-10-02 (anthropic.com/news)
- [T. Rowe Price brings more of Claude to its investment process](https://claude.com/blog/t-rowe-price-brings-more-of-claude-to-its-investment-process) — analyzed 2026-09-11
- [What 1,000 small business owners taught us about AI](https://claude.com/blog/what-1-000-small-business-owners-taught-us-about-ai) — analyzed 2026-09-11
- [Reducing cost and improving performance with Claude Platform](https://claude.com/blog/reducing-cost-and-improving-performance-with-claude-platform) — analyzed 2026-09-11
- [Introducing Claude Fable 5.1 and Claude Mythos 5.1](https://www.anthropic.com/claude-fable-and-mythos-5-1) — analyzed 2026-09-11 (anthropic.com/news; found via web search, NOT via the blog index)
- [A guide to the anatomy of effective commerce agents](https://claude.com/blog/the-anatomy-of-effective-commerce-agents) — analyzed 2026-09-06
- [Building commerce agents with Claude](https://claude.com/blog/claude-for-commerce-agents) — analyzed 2026-09-06
- [How Anthropic employees use Claude Tag](https://claude.com/blog/how-anthropic-employees-use-claude-tag) — analyzed 2026-09-06
- [Claude for Teachers, now available for U.S. K-12 schools and districts](https://claude.com/blog/claude-for-teachers-now-available-for-schools-and-districts) — analyzed 2026-09-06
- [How Warp builds self-improving agents on Claude](https://claude.com/blog/how-warp-builds-self-improving-agents-on-claude) — analyzed 2026-08-28
- [Claude in Chrome is generally available](https://claude.com/blog/claude-in-chrome-generally-available) — analyzed 2026-08-28
- [Claude gets its own browser in Cowork](https://claude.com/blog/cowork-built-in-browser) — analyzed 2026-08-28
- [Bain & Company joins the Claude Partner Network as a Global Premier partner](https://claude.com/blog/bain-company-joins-the-claude-partner-network-as-a-global-premier-partner) — analyzed 2026-08-28
- [Claude's memory works everywhere, and you decide what's in it](https://claude.com/blog/claudes-memory-works-everywhere-and-you-decide-whats-in-it) — analyzed 2026-08-28
- [How an Anthropic field marketer uses Claude Code to send weekly personalized updates to every sales rep](https://claude.com/blog/how-an-anthropic-field-marketer-uses-claude-code-to-send-weekly-personalized-updates-to-every-sales-rep) — analyzed 2026-08-28
- [Bringing the cybersecurity capabilities of Claude Mythos 5 to more defenders](https://claude.com/blog/bringing-claude-mythos-5-to-more-defenders) — analyzed 2026-08-28
- [The AI-Native SDLC playbook](https://claude.com/blog/the-ai-native-sdlc-playbook) — analyzed 2026-08-28
- [Anthropic's approach to teaching and learning AI](https://claude.com/blog/anthropics-approach-to-teaching-and-learning-ai) — analyzed 2026-08-28
- [How monday.com transformed its platform into an agent-first product where humans and agents collaborate](https://claude.com/blog/how-monday-com-transformed-its-platform-into-an-agent-first-product-where-humans-and-agents-collaborate) — analyzed 2026-08-28
- [The Claude Code guide for startups](https://claude.com/blog/claude-code-guide-for-startups) — analyzed 2026-08-28
- [Build production agents with computer use, the Skills API, and the Files API](https://claude.com/blog/computer-use-skills-api-files-api) — analyzed 2026-08-28
- [Turning conversation into knowledge: how Slack builds human-agent teams](https://claude.com/blog/turning-conversation-into-knowledge-how-slack-builds-human-agent-teams) — analyzed 2026-08-28
- [Claude on call: How Claude Tag serves as Anthropic's first responder for CI/CD failures](https://claude.com/blog/ai-ci-cd-on-call) — analyzed 2026-08-28
- [How ABC Legal turned every employee into a builder with Claude Managed Agents](https://claude.com/blog/how-abc-legal-turned-every-employee-into-a-builder-with-claude-managed-agents) — analyzed 2026-08-28
- [Maximizing the value of your Claude Code sessions](https://claude.com/blog/maximizing-the-value-of-your-claude-code-sessions) — analyzed 2026-08-28
- [Securing the frontier: How JetBrains evaluates and deploys Claude Fable 5](https://claude.com/blog/how-jetbrains-evaluates-and-deploys-claude-fable-5) — analyzed 2026-08-28
- [Self-service data analytics in Slack: how Anthropic deploys Claude Tag for ad-hoc questions](https://claude.com/blog/self-service-data-analytics-in-slack-how-anthropic-deploys-claude-tag-for-ad-hoc-questions) — analyzed 2026-08-28
- [Claude Tag now reads even more of the room](https://claude.com/blog/claude-tag-now-reads-even-more-of-the-room) — analyzed 2026-08-28
- [The Claude in Chrome side panel is now Claude Cowork](https://claude.com/blog/cowork-chrome-side-panel) — analyzed 2026-08-28
- [Compliance API coverage extends to Claude Cowork and Claude Code](https://claude.com/blog/compliance-api-cowork-and-claude-code) — analyzed 2026-08-28
- [Auto mode is now the default in Claude Code for Pro, Max, and Team plans](https://claude.com/blog/auto-mode-default-in-claude-code) — analyzed 2026-08-28
- [Running auto mode in production](https://claude.com/blog/auto-mode-in-production) — analyzed 2026-08-28
- [How Anthropic's business development team uses Claude to run inbound and outbound at scale](https://claude.com/blog/how-anthropics-business-development-team-uses-claude-to-run-inbound-and-outbound-at-scale) — analyzed 2026-08-28
- [Millennium and Anthropic are building a digital risk analyst with Claude](https://claude.com/blog/millennium-and-anthropic-are-building-a-digital-risk-analyst-with-claude) — analyzed 2026-08-28
- [Run Claude Code sessions on your own compute](https://claude.com/blog/run-claude-code-sessions-on-your-own-compute) — analyzed 2026-08-28
- [Inference hooks: inline data loss prevention for Claude Enterprise](https://claude.com/blog/claude-enterprise-inference-hooks) — analyzed 2026-08-28
- [Bringing MCP 2026-07-28 to Claude](https://claude.com/blog/bringing-mcp-2026-07-28-to-claude) — analyzed 2026-08-28
- [The new rules of context engineering for Claude 5 generation models](https://claude.com/blog/the-new-rules-of-context-engineering-for-claude-5-generation-models) — analyzed 2026-08-28
- [Claude models explained: choosing the best model for your use case](https://claude.com/blog/claude-models-explained-choosing-the-best-model-for-your-use-case) — analyzed 2026-08-28
- [How the product designer who built Claude Design uses it to explore ideas before building them](https://claude.com/blog/how-the-product-designer-who-built-claude-design-uses-it-to-explore-ideas-before-building-them) — analyzed 2026-08-28
- [Four role-based Claude certifications](https://claude.com/blog/four-role-based-claude-certifications) — analyzed 2026-08-28
- [Think through hard problems in voice mode](https://claude.com/blog/think-through-hard-problems-in-voice-mode) — analyzed 2026-08-28
- [Building verification loops in Claude Code with skills](https://claude.com/blog/building-verification-loops-in-claude-code-with-skills) — analyzed 2026-08-28
- [How Outtake built a cyber investigator on Claude](https://claude.com/blog/how-outtake-built-a-cyber-investigator-on-claude) — analyzed 2026-08-28
- [How Anthropic secures its AI-native software development lifecycle](https://claude.com/blog/how-anthropic-secures-its-ai-native-software-development-lifecycle) — analyzed 2026-08-28
- [How Datadog built a "universal machine tool" for Claude Code](https://claude.com/blog/how-datadog-built-a-universal-machine-tool-for-claude-code) — analyzed 2026-08-28
- [Working at the frontier: Rakuten](https://claude.com/blog/working-at-the-frontier-rakuten) — analyzed 2026-08-28
- [Working at the frontier: Cursor](https://claude.com/blog/working-at-the-frontier-cursor) — analyzed 2026-08-28
- [The CISO guide to agentic AI](https://claude.com/blog/ciso-guide-to-agentic-ai) — analyzed 2026-08-28
- [How Anthropic runs large-scale code migrations with Claude Code](https://claude.com/blog/ai-code-migration) — analyzed 2026-07-17
- [Working with Claude Fable 5 in Claude Cowork](https://claude.com/blog/working-with-claude-fable-5-in-claude-cowork) — analyzed 2026-07-17
- [Working at the frontier: Why Base44 trusts Claude Fable 5 with their most challenging engineering work](https://claude.com/blog/working-at-the-frontier-why-base44-trusts-claude-fable-5-with-their-most-challenging-engineering-work) — analyzed 2026-07-17
- [Working at the frontier: How Hebbia builds AI for financial diligence that can't miss a detail](https://claude.com/blog/working-at-the-frontier-how-hebbia-builds-ai-for-financial-diligence-that-cant-miss-a-detail) — analyzed 2026-07-17
- [Working at the frontier: How Cognition trusts Claude Fable 5 to work through the night](https://claude.com/blog/working-at-the-frontier-how-cognition-trusts-claude-fable-5-to-work-through-the-night) — analyzed 2026-07-17
- [Working at the frontier: How Thomson Reuters builds AI for high-stakes professional work](https://claude.com/blog/working-at-the-frontier-how-thomson-reuters-builds-ai-for-high--stakes-professional-work) — analyzed 2026-07-17
- [How Anthropic's marketing operations team uses Claude Cowork to automate reporting and campaign builds](https://claude.com/blog/how-anthropics-marketing-operations-team-uses-claude-cowork-to-automate-reporting-and-campaign-builds) — analyzed 2026-07-17
- [Bringing Claude Code and Claude Cowork to government](https://claude.com/blog/bringing-claude-code-and-claude-cowork-to-government) — analyzed 2026-07-17
- [Choosing a Claude model and effort level in Claude Code](https://claude.com/blog/claude-model-and-effort-level-in-claude-code) — analyzed 2026-07-17
- [Claude Cowork is coming to mobile and web](https://claude.com/blog/cowork-web-mobile) — analyzed 2026-07-17
- [How people are using Claude Cowork](https://claude.com/blog/how-people-are-using-claude-cowork) — analyzed 2026-07-17
- [A field guide to Claude Fable 5: Finding your unknowns](https://claude.com/blog/a-field-guide-to-claude-fable-finding-your-unknowns) — analyzed 2026-07-17
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

Last fetched: 2026-10-02
Post links found: 15 on claude.com/blog index page 1 (pagination now reads "1 / 17"; was "1 / 16" on 2026-09-11).
Category sweep found 3 MORE posts the index had already dropped. 10 posts on anthropic.com/news page 1.
Total processed this run: 28 (18 blog + 10 news).

<!-- CATEGORY SWEEP IS MANDATORY FOR ANY GAP > ~1 WEEK (re-confirmed 2026-10-02 with
     evidence): the 21-day gap this run recovered proved the structural note below. The
     index's 15-post window had already dropped three posts that the category sweep caught:
     agentic-coding-is-straining-ci... (/blog-category/claude-code),
     claude-for-small-business-launches... and claude-for-financial-advisors
     (/blog-category/announcements). Index-only discovery would have silently lost all three.
     Note /blog-category/enterprise-ai was NOT swept this run (the other three sufficed to
     reconcile the count); sweep it too on the next multi-week gap. -->

<!-- SOURCE GAP (discovered 2026-09-11, STILL UNFIXED as of 2026-10-02): this repo's CLAUDE.md names
     https://claude.com/blog as "the Claude blog," singular. Anthropic runs TWO. The split
     appears to be product/customer content on claude.com/blog, and company/research/MODEL
     ANNOUNCEMENTS on https://www.anthropic.com/news. The Fable 5.1 launch (2026-09-01) —
     a frontier model release with direct pricing and capability impact — was never seen by
     this cron because of it; it surfaced only because a quoted sentence in an unrelated
     cost post mentioned the name and this run web-searched it. Until CLAUDE.md is fixed,
     EVERY RUN MUST ALSO FETCH https://www.anthropic.com/news.
     STATUS 2026-10-02: the 09-11 backlog is now processed EXCEPT TWO, both of which had
     already scrolled off news index page 1 and were therefore never fetched —
     news/threat-intelligence-report-september-2026 (Sep 10) and
     news/improving-fable-5-s-biology-safeguards (Aug 7). Neither is in the processed list;
     both remain unverified gaps. A future run wanting them must reach them by DIRECT URL,
     because the news index is a rolling window with the same drop-off behaviour as the blog.
     THIRD SOURCE NEEDED: Claude Sonnet 5.5 (launched 2026-09-28) had NO post on either
     source's page 1 — it was found only in the platform docs model table. Model launches
     can therefore be missed while watching both blogs. Check
     platform.claude.com/docs/en/about-claude/models/overview and .../pricing each run;
     that is now the authoritative source for IDs and prices, and the only one this run
     found Sonnet 5.5 through. -->

<!-- STRUCTURAL FACT (confirmed 2026-08-28, still true 2026-09-11): the claude.com/blog index
     is a rolling window of ~15 most-recent posts with JS-driven pagination ("1 / 16" pages;
     "View more" uses hashed query params like ?b7eea976_page=2 that do NOT work via plain
     fetch — page 2 returns page-1 content). Category pages DO enumerate more per topic and
     are plain-fetchable: /blog-category/announcements, /blog-category/agents,
     /blog-category/claude-code, /blog-category/enterprise-ai. Any run recovering a multi-week
     gap MUST sweep all four category pages; the index alone silently drops backlog. The
     2026-09-11 run needed no sweep — a 5-day gap sat well inside the 15-post window. -->

<!-- SLUG WARNING: the Claude Design post's slug is the LONG form
     how-the-product-designer-who-built-claude-design-uses-it-to-explore-ideas-before-building-them
     — the truncated form (...-to-explore-ideas) 404s. Do not trust slugs recorded from index
     extraction without a successful fetch. -->

<!-- RELIABILITY — THREE DISTINCT FAILURE CLASSES, updated 2026-10-02. Six of the last nine
     runs failed to deliver. The classes matter more than the count, because no single alarm
     catches all three:
       CLASS 1 — hard error, nothing produced: 2026-06-26, 07-10, 07-31 (API connection errors).
       CLASS 2 — work completed, never persisted: 2026-09-06 (detail below). Nothing errored.
       CLASS 3 — MACHINE SLEPT MID-RUN (new, found 2026-10-02): 2026-09-18 (mission f1af88d3)
         died 17 minutes in with the literal error "API Error: Your computer went to sleep
         mid-response." 2026-09-25 (mission b55b03ff) is logged at 3h51m but its transcript
         ends roughly an hour in, right after fetching the blog, with multi-minute gaps
         between consecutive tool calls that look like a throttled or sleeping machine.
         BOTH produced nothing: this run checked both mission dirs — clean trees, no
         stranded findings file, so there was nothing to recover (unlike 09-06).
     CLASS 3 IS A POWER-MANAGEMENT PROBLEM, NOT AN AGENT PROBLEM. The cheap fix is upstream
     of any alarm: assert `caffeinate` for the duration of a cron-fired mission, or move the
     10:00 fire to a window when the Mac is reliably awake. That removes the class rather
     than detecting it. Do that BEFORE building more detection.
     A slept run may also exit cleanly having done nothing, so an exit-status alarm misses
     Class 3 as well as Class 2 — which independently confirms, from a second direction, the
     09-11 conclusion below that the check must assert ARTIFACTS LANDED.
     Prior detail: the 2026-09-06 run (mission
     9eb25b0c) is a DISTINCT and more dangerous failure mode: it completed the full analysis
     and wrote findings/2026-09-06.md, then hit its session limit seconds later — before
     commit, push, or notification. The work looked finished from the inside and was invisible
     from the outside. The 2026-09-11 run recovered that file verbatim from the dead mission's
     working directory. Implication for bead agenc-inrf (AgenC repo, P2, tracks the cron
     failure-alarm check-loop): an exit-status alarm would NOT have caught 09-06, because
     nothing errored. The check must verify the artifacts landed — commit pushed AND
     notification posted — not merely that the run exited cleanly.
     ORDERING RULE for future runs: write files, then commit and push and VERIFY
     (git log origin/main -1), then notify, and only then do anything slow. Never leave the
     deliverable undurable while a long call runs. -->
