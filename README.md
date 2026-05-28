# Research Skills

My agent skills for doing real research — not vibe coding.

Research is hard. Writing papers, running experiments, and keeping code quality high while chasing novel ideas requires discipline. These skills help me stay rigorous, ship reproducible results, and write papers that pass review.

These skills are designed to be small, easy to adapt, and composable. They work with any model. Hack around with them. Make them your own. Enjoy.

## Quickstart (30-second setup)

1. Run the skills.sh installer:

```bash
npx skills@latest add potateow/skills
```

2. Pick the skills you want, and which coding agents you want to install them on. **Make sure you select `/setup-research-skills`**.

3. Run `/setup-research-skills` in your agent. It will scaffold the per-repo config that other skills consume.

4. Bam — you're ready to go.

## Why These Skills Exist

I built these skills to fix common failure modes I see with Claude Code, Codex, and other coding agents in a research context.

### #1: The Agent Didn't Do What I Want

> "No-one knows exactly what they want"
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**The Problem**. The most common failure mode is misalignment. You think the agent knows what you want. Then you see what it's built — and realize it didn't understand you at all.

**The Fix** is a **grilling session** — getting the agent to ask detailed questions about what you're building before it starts.

- [`/grill-me`](./skills/productivity/grill-me/SKILL.md) — for non-code research plans
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) — same as above, but builds a shared language with the AI and documents decisions in ADRs

These are my most popular skills. Use them _every_ time you want to make a change or start a new experiment.

### #2: The Agent Is Way Too Verbose

> With a ubiquitous language, conversations among developers and expressions of the code are all derived from the same domain model.
>
> Eric Evans, [Domain-Driven-Design](https://www.amazon.co.uk/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

**The Problem**: Agents are usually dropped into a project and asked to figure out the jargon as they go. So they use 20 words where 1 will do.

**The Fix** is a shared language. It's a document that helps agents decode the jargon used in your research.

This is built into [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md). It's a grilling session that helps you build a shared language with the AI, and document hard-to-explain decisions in ADR's.

> [!TIP]
> A shared language pays off session after session:
>
> - **Variables, functions and files are named consistently**, using the shared language
> - The **codebase is easier to navigate** for the agent
> - The agent **spends fewer tokens on thinking**, because it has access to a more concise language

### #3: The Code Doesn't Work

> "Always take small, deliberate steps. The rate of feedback is your speed limit. Never take on a task that's too big."
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**The Problem**: Even when aligned, the agent still produces crap without feedback loops.

**The Fix**: You need the usual tranche of feedback loops: static types, automated tests, and disciplined debugging.

For automated tests, a red-green-refactor loop is critical. I've built a **[`/tdd`](./skills/engineering/tdd/SKILL.md)** skill you can slot into any project.

For debugging, I've also built a **[`/diagnose`](./skills/engineering/diagnose/SKILL.md)** skill that wraps best debugging practices into a simple loop.

### #4: We Built A Ball Of Mud

> "Invest in the design of the system _every day_."
>
> Kent Beck, [Extreme Programming Explained](https://www.amazon.co.uk/Extreme-Programming-Explained-Embrace-Change/dp/0321278658)

**The Problem**: Research codebases get complex fast. Because agents speed up coding, they also accelerate software entropy.

**The Fix** is caring about the design of the code:

- [`/zoom-out`](./skills/engineering/zoom-out/SKILL.md) tells the agent to explain code in the context of the whole system
- [`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) helps rescue a codebase that has become a ball of mud. I recommend running it once every few days.

### #5: Writing Papers Is Painful

**The Problem**: Writing research papers involves dozens of micro-decisions — citation formats, venue-specific templates, figure placement, anti-AI detection — and it's easy to miss something.

**The Fix** is a unified paper-writing skill that auto-detects your scenario and runs the optimal workflow:

- [`/unified-cs-paper`](./skills/research/unified-cs-paper/SKILL.md) handles repo→paper, idea→paper, top-conference submission, journal article, reviewer response, and draft polish. Integrates zero-hallucination citation verification, LaTeX compilation checks, and venue-specific formatting.

### Summary

Research fundamentals matter more than ever. These skills are my best effort at condensing these fundamentals into repeatable practices, to help you ship the best research of your career. Enjoy.

## Reference

### Research

Skills for academic research and paper writing.

- **[unified-cs-paper](./skills/research/unified-cs-paper/SKILL.md)** — Write CS/ML research papers. Auto-detects repo→paper, idea→paper, top-conference, journal, revision, or polish scenarios.
- **[purity](./skills/research/purity/SKILL.md)** — Test proxy/VPN nodes for IP purity (fraud score) and latency. Analyze Clash/Shadowrocket subscriptions.
- **[claude-bounded](./skills/research/claude-bounded/SKILL.md)** — Use Claude Code for bounded, low-risk tasks: installs, downloads, diagnostics, summaries, narrow second opinions.

### Engineering

Skills for research code work.

- **[diagnose](./skills/engineering/diagnose/SKILL.md)** — Disciplined diagnosis loop for hard bugs and performance regressions: reproduce → minimise → hypothesise → instrument → fix → regression-test.
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)** — Grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates `CONTEXT.md` and ADRs inline.
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)** — Find deepening opportunities in a codebase, informed by the domain language in `CONTEXT.md` and the decisions in `docs/adr/`.
- **[setup-research-skills](./skills/engineering/setup-research-skills/SKILL.md)** — Scaffold the per-repo config (issue tracker, domain doc layout) that the other engineering skills consume.
- **[tdd](./skills/engineering/tdd/SKILL.md)** — Test-driven development with a red-green-refactor loop. Builds features or fixes bugs one vertical slice at a time.
- **[zoom-out](./skills/engineering/zoom-out/SKILL.md)** — Tell the agent to zoom out and give broader context or a higher-level perspective on an unfamiliar section of code.

### Productivity

General workflow tools, not code-specific.

- **[caveman](./skills/productivity/caveman/SKILL.md)** — Ultra-compressed communication mode. Cuts token usage ~75% by dropping filler while keeping full technical accuracy.
- **[grill-me](./skills/productivity/grill-me/SKILL.md)** — Get relentlessly interviewed about a plan or design until every branch of the decision tree is resolved.
- **[write-a-skill](./skills/productivity/write-a-skill/SKILL.md)** — Create new skills with proper structure, progressive disclosure, and bundled resources.
