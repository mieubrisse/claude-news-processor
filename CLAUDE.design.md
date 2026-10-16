CLAUDE.design.md
================

Design rationale for this repository's `CLAUDE.md`. Bootstrapped from current state on
2026-10-16, per `/cc-global-instructions-management`'s rule that a missing companion document
is created on the first edit rather than skipped.

Purpose
-------

This `CLAUDE.md` is the entire operating definition of a weekly cron loop. The repo holds no
code — only the instruction file, a state file (`notes.md`), and an append-only `findings/`
archive. So unlike a normal project `CLAUDE.md`, which orients an agent that would be mostly
competent without it, this one *is* the program. The progressive-disclosure decision test
resolves differently here as a result: almost everything in it is needed on every run, because
there is only one kind of run.

What it optimizes for, in priority order:

1. **Not silently reporting "nothing new."** The loop's characteristic failure is not a crash —
   it is a run that completes, reports an empty week, and is wrong. Several sections exist
   purely to make that outcome loud (the blog-health-check, the mandatory multi-source sweep,
   the verify-before-finishing list).
2. **Surviving its own observer.** Each run is a fresh context with no memory. The file
   therefore tells the agent to reconstruct taste from two places — `notes.md` for setup state
   and the Standing Calibration Rules for durable preference — rather than assuming continuity.
3. **Earning Kevin's attention rather than consuming it.** The Standing Calibration Rules all
   push the same direction: tie every suggestion to something that actually exists in his
   setup, skip material with no transferable technique, and favour findings that provoke a
   mechanical follow-up question.

Provenance
----------

### Created

- **2026-10-16** — Bootstrapped by AgenC mission `fd174ff0-cfdd-476d-8d74-1f1390042def`, Claude
  session `575bdff6-086b-4b0c-a2ff-09b19c9be07a`. Reconstructed from the then-current
  `CLAUDE.md` (233 lines) plus the accumulated structural notes in `notes.md`, which had been
  serving as the de-facto design record for everything except the instruction file itself. No
  prior design document existed; the `CLAUDE.md`'s own history is recoverable from `git log`.

### Edits

- **2026-10-16** — **ATTEMPTED AND NOT LANDED.** A source-map correction to the `<role>` block,
  the `Blog Source` section, the step 4 discovery instruction, its `<blog-health-check>` block,
  and the `<notes-md-schema>` block's example URLs and count format. The Guidance-Edit Gate
  blocked five attempts and tripped its circuit breaker, so `CLAUDE.md` on disk is **unchanged
  and still factually wrong about its own source**. AgenC mission
  `fd174ff0-cfdd-476d-8d74-1f1390042def`, Claude session
  `575bdff6-086b-4b0c-a2ff-09b19c9be07a`. Pending Kevin's ruling; status and the full rationale
  are in `findings/2026-10-16.md`, Suggestion 6. The sections below describe the *intended*
  design, which is still the right design — they are kept so whoever lands this edit does not
  have to re-derive it, not because the edit happened.

  **Why it did not land, since the next agent will hit the same wall.** The source map is one
  fact expressed in five places, so no single-spot edit leaves the file coherent — the gate
  correctly blocked the narrow attempts on exactly that ground. It then judged the atomic
  whole-file write as too broad and asked for "one MultiEdit," which the session had no
  MultiEdit tool to perform. Both available moves were refused, each round surfacing new and
  mostly fair findings on the newly written prose. Four of the five blocks were right and were
  fixed (a missing session UUID here; a false "workflow untouched" claim here; a summarizing
  principle that would have suppressed a change step 3 authorizes; and an undefined
  `LISTING-LEVEL ONLY` entry state, which also exposed a real defect in that run's `notes.md`
  entries). The remaining disagreement is about scope granularity, not correctness, and is
  Kevin's to settle.

  **Scope, and why these five spots and no others:** the five are not independent edits, they
  are one fact — how many surfaces exist and what their URLs look like — expressed in five
  places. Correcting only `Blog Source` would have left the file at war with itself: step 4
  carried its own copy of the single old URL, the health-check recorded one aggregate "link
  count" that is meaningless across four surfaces and would hide one of them going dark, the
  schema modelled processed-post URLs in the dead `/blog/<slug>` form, and `<role>` still
  called the source "the Claude blog" singular. The Guidance-Edit Gate blocked a narrower first
  attempt on exactly that ground, which is the record of why the scope is this wide.

  **Why:** the section had become factually wrong about the thing it exists to describe.
  `claude.com/blog` now redirects into `claude.com/resources/articles/`, and the section's
  concrete instruction — extract `href` values matching the pattern `/blog/<slug>` — matched
  nothing on the live site. Separately, the same run discovered a fourth publishing surface,
  `claude.dev/blog` (Anthropic's developer blog), which no run of this cron had ever read
  despite it being the densest source of Claude Code and agent-building material and therefore
  the most relevant surface to Kevin's setup. Ten unprocessed posts were sitting on it.

  **Sources:** live fetches of `claude.com/resources` and its five section pages,
  `claude.dev/blog`, `www.anthropic.com/news`, and
  `platform.claude.com/docs/en/about-claude/models/overview`, all 2026-10-16. The relocation
  and slug-mutation behaviour was confirmed by fetching the old URL
  `claude.com/blog/claude-code-mods` and observing it serve
  `/resources/articles/customize-claude-code-with-mods`. Full analysis in
  `findings/2026-10-16.md`, Suggestion 6.

Design Decisions
----------------

### The source list stays inline, as a table

The decision test's first question — is this needed in most conversations? — is trivially yes:
every run begins by fetching these sources. Its second question matters more here. The source
list is **load-bearing** in the specific sense the skill defines: an agent acting on a stale
source list does not fail loudly, it reports an empty week and commits that report. The cost
of being wrong is a silent false negative that looks exactly like a true one, which is why the
list is not offloaded to a skill that a run might neglect to invoke.

The table form was chosen over prose because the fourth column is the part that resists drift:
naming *why* each surface matters lets a future agent decide whether a newly-discovered surface
belongs in the list, rather than treating the list as closed.

### The section states intent before it states URLs

Rejected alternative: a bare corrected checklist of four URLs and five section paths. That is
what the previous version effectively was, scoped to one URL, and it failed in the one way a
checklist always fails — the world moved and the checklist did not know it had. The rewritten
section closes by naming the deeper obligation (a documented pattern that stops matching *is*
the finding; record it and fix this section) so that the next relocation produces a correction
rather than a quiet empty report. This follows `/prompt-engineer` and `/parable-of-the-orange`
doctrine: give the executing agent the WHY so it can act correctly in a situation the author
did not foresee.

### Discovery step 4 points at this section instead of restating a URL

The old step 4 carried its own copy of the blog URL, which is how the two drifted: the section
and the step had to be corrected independently, and a reader fixing one would not necessarily
find the other. Step 4 now defers to `Blog Source` as the single authoritative home for the
source map, per the Single Source of Truth principle in Kevin's global instructions. The
`notes-md-schema` block's example URLs were updated in the same pass for the same reason —
illustrative examples that model a dead URL pattern teach the wrong shape.

### What was deliberately left alone, and what was routed to Kevin instead

Untouched: workflow steps 1-3, 5-6 and 8-9, the `<output-format>` block, `Quality Standards`,
the `Standing Calibration Rules`, and the mission-spawning guidance. Step 4 with its
`<blog-health-check>` and step 7's `<notes-md-schema>` block were changed, as the Edits entry
above records, because they carry part of the source map rather than part of the workflow's
logic.

This boundary was tested during the 2026-10-16 edit. An independent verifier run against that
session found three genuine defects elsewhere in `CLAUDE.md`: the `<output-format>` block caps
Blog Digest entries at 2-3 sentences while Standing Calibration Rule 3 demands mechanism depth
that cannot fit in 2-3 sentences; nothing requires the five-field suggestion schema on
speculative entries; and nothing tells a run to check for an existing bead before proposing
work. The session drafted fixes for all three into `CLAUDE.md`, and the Guidance-Edit Gate
blocked the write as scope creep against this paragraph.

The fixes were pulled back out and filed as **Suggestion 9 in `findings/2026-10-16.md`**,
where Kevin rules on them — the channel this repo already has for proposals. The same logic
applied to a sixth Standing Calibration Rule the session wanted to add: the section admits
entries on Kevin's feedback or on a cross-run pattern, and a rule drawn from one self-observed
near-miss meets neither bar, so it went to Suggestion 9 as a standing-rule candidate rather
than into the file.

The working rule this run operated under, on the evidence of one gate verdict rather than a
pattern: change this file when a fact it asserts becomes false, when a cross-run pattern meets
the bar step 3 sets, or when Kevin rules on a proposal — but not merely because a run had a
good idea about it. Note that the middle path is step 3's and is genuinely open to a future run;
nothing here narrows it. The reasoning is that a cron's instruction file is an expensive place
to land an unapproved improvement, because every change takes effect on every future run with
nobody watching.
