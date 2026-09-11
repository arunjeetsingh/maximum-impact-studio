---
layout: post
title: "Announcing Maximum Impact Coding Agent"
date: 2026-09-11
author: Arun Singh
description: "Three coding skills — open-pr, review-pr, and address-review-comments — extracted from over 300 merged pull requests across TokenCounter, this site, and Chal Rickshaw, and packaged as an open, MIT-licensed agent that runs the same PR loop across OpenClaw, Claude Code, and Codex."
image: /assets/img/posts/mi-coding-agent-cover.png
excerpt_separator: <!--more-->
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/mi-coding-agent-cover.png' | relative_url }}" alt="Three cartoon robots in different colors arranged around a floating GitHub pull request card, connected by a looping arrow labeled Open PR, Review PR, and Address Comments, with a locked Merge button in the center" loading="lazy">
</figure>

TLDR: I've been coding apps and web sites with a combination of Anthropic, OpenAI, and occasionally Meta's LLMs for about 8 months now. What started as tinkering evolved into a project/metric management web app within Meta, an iOS/Android utility app ([TokenCounter](https://apps.apple.com/app/id6772613833)), a web site ([maximumimpact.studio](https://maximumimpact.studio)), and a game ([Chal Rickshaw!](https://apps.apple.com/us/app/chal-rickshaw/id6775334841)). Across those four projects with over 300 merged pull requests and counting, I've leaned on the same small set of coding skills for the everyday PR workflow: open a review-ready PR, review it against its own history without drifting, and address feedback round by round until it's actually ready. Today I'm packaging that first set of skills as an open agent at [github.com/arunjeetsingh/mi-coding-agent](https://github.com/arunjeetsingh/mi-coding-agent). The repo can bootstrap a brand-new agent from scratch or teach the same three skills to an existing one. I've proven the loop end-to-end across OpenClaw, Claude Code, and Codex. Every one of those tools is allowed to open PRs, review them, and fix them. None of them is allowed to merge, but that's a deliberate call by me, not a technical limit. I won't claim this is the best way to run an agentic PR loop, but it's mine. It's held up across four real codebases and is now MIT-licensed and open for anyone to fork, adapt, or improve. Comments, critiques, and contributions welcome!

<!--more-->

## From copy-pasted prompts to skills

Somewhere around PR #30 on Chal Rickshaw I noticed I was retyping the same instructions into every new agent session. I'd ask the agent to build a feature in its own worktree, prove it works with tests, open the PR with an easy-to-read description of the change and tests, and then wait for review. A few weeks later I was doing the same thing on the Maximum Impact Studio site, and again on TokenCounter. Each time the instructions drifted a little — a fix discovered on one project didn't make it back to the others, a wording that worked for Claude Code needed rephrasing for Codex.

Eventually I stopped copy-pasting prompts and wrote the workflow down as three skills — `open-pr`, `review-pr`, and `address-review-comments` — living alongside the code as `SKILL.md` files. Eventually, they became a real, versioned process I could point any agent at.

## Battle-tested across four real projects

These skills were extracted from actually shipping software, and each project put a different kind of pressure on them:

1. **[TokenCounter](https://apps.apple.com/app/id6772613833)** ([source](https://github.com/arunjeetsingh/token-tracker)): an iOS/Android app that reads your Anthropic org's Cost API and shows spend at a glance. 67 pull requests, 66 merged, across roughly 10,000 lines of Swift and Kotlin.
2. **[maximumimpact.studio](https://maximumimpact.studio)**: a Jekyll site with an apps catalog, a privacy/policy layer for two live App Store apps, and every post I've made since May 2026. 44 pull requests, 42 merged, around 2,200 lines of Markdown, HTML, and Sass.
3. **[Chal Rickshaw!](https://apps.apple.com/us/app/chal-rickshaw/id6775334841)** ([Android](https://play.google.com/store/apps/details?id=studio.maximumimpact.chalrickshaw)): a mobile game about dodging cows, cops, and Delhi traffic in an auto rickshaw, built in Godot with a Supabase backend. This is the biggest project, with 214 pull requests, 211 merged, roughly 72,000 lines of GDScript, SQL migrations, and tooling. That doesn't even count art, audio, and vendored SDKs, which push the repo past 2.6 million lines total. One PR in particular went 19+ review rounds before it converged, which is exactly the kind of long adversarial review that stress-tested `review-pr`'s "bounded convergence" rules into existence.

Between the three projects, over 320 merged pull requests have run through some version of this loop.

## What the three skills actually do

Put together, the skills teach an agent (or a coding tool) the same three-phase loop, front to back:

1. **`open-pr`**: build the requested change in its own git worktree, never touching whatever else is checked out. Prove the change with real tests and mutation testing — that is, a test doesn't count as a guard until the defect it addresses is reintroduced and the guard is observed failing — run a self-audit before committing, and open the PR with a body that says what the change does and does not do. The last step of `open-pr` is arming a monitor that watches the PR for a reviewer response, so the loop doesn't need a human to notice a comment landed.
2. **`review-pr`**: review the PR against its entire comment history, not just the diff since last time, so a fix doesn't quietly regress something a much earlier round already flagged. The first broad pass establishes the baseline, and later rounds are scoped: a new blocking finding has to be an unresolved earlier issue, a regression from a fix, or a genuinely missed high-impact bug, which avoids scope creep or a fresh nitpick. When the review posts findings, it also flips on a watch for the author's response. When a review is clean with no issues to fix, `review-pr` stops watching.
3. **`address-review-comments`**: pull every reviewer comment — all three GitHub streams (top-level comments, formal reviews, and inline review replies), fully paginated — fix each finding with a per-edit assertion that the change actually landed, run its own pre-commit self-audit for consistency, regression, and staleness, plus a platform fact-check lens, since some of the worst review rounds turned out to be reviewers and fixers both confidently asserting things about a platform's API that simply weren't true. Then one commit, one push, with one reply per round.

The whole point of the three working together is that none of them assumes a human is watching the middle of the loop. `open-pr` hands off to a watch, `review-pr` posts and watches, `address-review-comments` fixes, replies, and re-arms the watch (or recognizes an all-clear review and stops). A human only needs to show up at the start to say what to build, and at the end to press merge.

## One loop, three different runtimes

The interesting engineering problem was writing the skills so the same workflow runs correctly whether the agent executing it is OpenClaw, Claude Code, or Codex. Each of those runtimes has a different way to wait for a PR comment without wasting tokens:

- **Claude Code** has a `Monitor` primitive. This kicks off a background shell process that polls quietly and only wakes the model when a GitHub comment actually matches. Token use cost while idle is effectively zero.
- **Codex** has no equivalent background process, so the same job runs as a scheduled heartbeat that re-invokes the skill every 30 minutes. It costs a small, real amount of model usage while quiet. This is an inherent tradeoff of the runtime, not a design choice — I hope this is something OpenAI fixes soon.
- **OpenClaw**'s answer is a `cron` job whose `trigger.script` gate runs the same polling predicate headlessly, and only invokes the actual agent when the predicate fires. That gets Claude Code's near-zero idle cost and Codex's durability (the watch survives past a single session), which is a nice property neither of the other two gets on its own.

Rather than write three separate versions of the "watch a PR" logic and have them drift, all three skills point at one shared contract file with an explicit host-mapping table. Each skill says "wait the way that file prescribes for your host" instead of assuming one mechanism everywhere.

## Agents open PRs, review them, but don't merge

Every one of these skills explicitly asks the running agent or tool to never merge and never deploy. An agent can build the change, review it as many rounds as it takes, fix every finding, and land the PR in a state where the automated reviewer has posted "No issues to fix." This is a personal choice, not a technical limitation. In principle, `address-review-comments` already has everything it needs to close the loop itself — it knows how to detect a clean review, and a merge is one command away. Shipping code across a live app store presence and real users is a decision I want to make myself, even when the machine is confident.

If you fork this and decide your risk tolerance is different, the rule lives in exactly one sentence per skill file. That's a deliberate design choice too, because I wanted to make it easy to find and change.

## Contributions welcome!

The repo is public and MIT licensed so it's frictionless for both individuals and companies to fork, adapt, and send fixes back. If you run these skills against a different kind of repo and hit an edge case they don't handle cleanly, that's exactly the kind of thing I'd love a PR for.

- **Repo:** [github.com/arunjeetsingh/mi-coding-agent](https://github.com/arunjeetsingh/mi-coding-agent)
- Bootstrap a new agent from it, or add the skills to an existing one — the README covers OpenClaw, Claude Code, Codex, and Hermes.
- Published on ClawHub for one-line OpenClaw installs (`openclaw skills install @arunjeetsingh/open-pr`, and so on for the other two) once the automated security review clears.

This is the first release of what I expect to be an evolving set of skills. As my own workflow picks up new habits, I plan to keep feeding them back into this repo. If you try it, I'd genuinely like to hear what broke, what surprised you, and what you'd change.
