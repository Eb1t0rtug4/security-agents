# security-agents
Autonomous, guardrailed security agents designed and run in Claude Code for real vulnerability, pentest, and DFIR work.

# Autonomous Security Agents (Claude Code)

Agents I design and run in Claude Code, in the terminal, to do real work on live security engagements: vulnerability validation, penetration-test finding triage, and digital forensics. They run largely unattended, inside hard guardrails, and hand me back structured, reviewable output.

I'm a cybersecurity lead, not a software engineer. I build these the way a practitioner builds tools: start from the job, encode the rules, let the agent grind, and keep a human in the loop for judgment. Over time each agent needs less of me.

## The problem they solve

Security engagements are full of work that is high-volume, repetitive, and unforgiving of mistakes: validating scanner output finding by finding, confirming whether a vulnerability is real or a false positive, combing forensic disk images across many passes. By hand it's slow and burns the hours that should go to analysis. With a dumb script it's dangerous, because in security an agent that wanders out of scope or "confirms" something it invented does real damage.

So I build agents that are autonomous but boxed: they do the grind, they stay inside the rules, and they never get the final word. I do.

## How they're built (the pattern)

Every agent follows the same skeleton, which lives as plain files in the engagement folder:

- `CLAUDE.md` — the guardrails. Read first, governs the entire run: what the agent may and may not do, hard stops, what to do on ambiguity.
- `scope.txt` — the single source of truth for what's in scope. The agent may not edit it mid-run.
- `plan.md` — the approved list of checks/tasks, prepared and human-reviewed before the agent runs.
- `template.md` — the mandatory output structure, so every run produces a comparable, complete report.
- `evidence/` — a fixed convention for storing evidence plus a manifest, so everything is reproducible and chain-of-custody holds.

The agent reads those in order, then executes.

## The safeguards that make it safe to run unattended

- **Non-interactive by design.** The agent doesn't stop to ask. On ambiguity it takes the safe branch (read-only, no exploitation), logs it, and keeps going. An agent stopped waiting for an answer is an agent that delivered nothing.
- **Operational-window enforcement.** For jobs with an agreed testing window, the agent checks the system clock and won't touch the network outside it. It does the offline prep instead, notes the pending run, and stops.
- **Read-only / no-exploitation defaults.** Anything that needs human approval is skipped and recorded in an "omitted" section, never escalated mid-run.
- **Human-in-the-loop review, always.** The agent produces a draft; I validate every finding against the evidence before anything is final. I've caught agents inventing data points and drawing a backwards conclusion, which is exactly why the human stays in the loop.

## Where I've run this (genericized, real engagements are under NDA)

- **Overnight vulnerability validation.** An agent that runs during the agreed window, validates each scanner finding against the live hosts read-only, reclassifies false positives with evidence, and leaves a finished report by morning. It needs almost no supervision now.
- **Penetration-test finding validation.** Same pattern, with scope pinned by hostname instead of IP because shared-IP tenants make IP-based scope unsafe. A guardrail I added after spotting the risk.
- **Digital forensics (DFIR).** A forensic agent running on an isolated analysis instance, working read-only across disk-image snapshots over many structured passes, pausing between phases for my validation before continuing. Coordinated across Claude Code for execution and a separate surface for report drafting.

## How I keep it independent (and cheap)

- The agents run unattended during off-hours, so the expensive resource, my time, goes to review instead of grinding.
- Structured, bounded runs: a fixed plan and output template mean the agent isn't wandering or redoing work, which keeps each run tight.
- The pattern is reusable: a new engagement is mostly a new `scope.txt` and `plan.md`, not a new agent from scratch.

## What I'm not claiming

I don't hand-write the tooling from scratch. I design these agents with AI assistance and run them in Claude Code. That's the point: you don't have to be an engineer to build agents that do real work, if you know the work cold and you're disciplined about the guardrails.
