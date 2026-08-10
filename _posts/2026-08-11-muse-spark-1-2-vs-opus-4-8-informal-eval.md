---
layout: post
title: "Muse Spark 1.2 vs Opus 4.8: An Informal Eval"
date: 2026-08-11
author: Arun Singh
description: "I gave Muse Spark 1.2 and Opus 4.8 the same tiny, real coding task — harness-free, each in its own OpenClaw agent behind a Telegram bot — and measured tokens, wall-clock time, and PR quality. A neutral GPT-5.5 judge scored the results."
image: /assets/img/posts/muse-spark-eval-cover.png
excerpt_separator: <!--more-->
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/muse-spark-eval-cover.png' | relative_url }}" alt="A gladiator-style face-off between two armored robots against a futuristic city skyline: a gold warrior labeled OPUS 4.8 wielding a fiery sword on the left, and a blue-and-purple warrior labeled MUSE SPARK 1.2 with a shield and hammer on the right, split by a lightning bolt and a glowing VS" loading="lazy">
</figure>

TLDR: Muse Spark 1.2 and its coding model launched on Aug 5, 2026. I'd seen early versions back when I was still at Meta, so I was curious how it stacked up against Anthropic's Opus 4.8 — the same model that wrote 95%+ of Chal Rickshaw and TokenCounter. I deliberately skipped Opus 5 and Fable/Mythos because I've only just started using them and don't have strong opinions yet. I gave both models the same tiny task (a one-line fix in Chal Rickshaw), ran each in its own OpenClaw agent behind a Telegram bot so no coding harness could tilt the field, and measured tokens burned and wall-clock time from my prompt to a fix PR. Finally, I had GPT-5.5 running in a Hermes agent judge the two PRs on accuracy, completeness, and code quality. Opus 4.8 won, but there's clear directional improvement possible for Muse Spark, especially running in OpenClaw. Read on for the details.
<!--more-->

## Why do this at all?

I've been building on Opus 4.8 for months. It's the engine behind both apps at [maximumimpact.studio/apps]({{ '/apps/' | relative_url }}), and I'm genuinely happy with it. But a new, slightly cheaper challenger showing up is a good excuse to check whether my defaults still make sense. Muse Spark 1.2's coding model was the interesting one since I'd seen its lineage from the inside at Meta, and I wanted a fair, apples-to-apples read on it against what I actually use every day.

The main question I care about: if I hand each model the same small, real task the same way, what does it cost me in tokens and time, and which one gives me the better PR?

## Keeping it fair

Coding tools like Claude Code and Muse Code wrap their models in their own harnesses — scaffolding, context strategies, and model-specific tricks. That's great in practice, but it muddies a model-vs-model comparison. So I took the harnesses out of it.

Both models ran inside the same setup: an OpenClaw agent with a Telegram bot as the gateway. I kept the same workflow, pointed them at the same repo, and gave them the same constraint: open a PR, but don't merge. (I wrote about that rule before, in [how I have two AI coders review each other's work]({% post_url 2026-06-30-two-ai-coders-cross-review %}).) Opus 4.8 ran in one agent (Chintu) and Muse Spark 1.2 ran in a second, identical agent (Babli) pointed at a Meta proxy.

The task was a deliberately small issue in the Chal Rickshaw repo: a degraded in-app purchase was emitting the same `iap:done` analytics event twice, inflating the buy-to-done funnel. The real fix is essentially one line (give the degraded path its own event id, `iap:done_unpriced:<pid>`) plus a regression test. My prompt to each was intentionally open-ended: "Take a look at the open issues and start working on a PR for the one you think you can fix without impacting too much other code." Both correctly picked the same issue.

## What I measured

Two objective numbers came straight off each agent's session ledger: tokens burned and wall-clock time from my prompt to the pushed PR. Then a subjective read from a neutral judge (Hermes + GPT-5.5).

Both counts are individual LLM calls, so they're directly comparable: Muse Spark made 101, Opus made 18. Opus's ledger groups those 18 calls into 2 agent turns, but the call count is the like-for-like number. Muse Spark also had a hiccup where it stalled on tool calling and I had to prompt it again ("Where's the PR?") to finish, which added a couple of seconds to its wall clock. I kept that in the total.

### Muse Spark 1.2

- **Wall clock, prompt to PR pushed:** 13m 36s.
- **Provider-metered tokens:** ~6.04M across 101 LLM calls (~60k/call). Of that, only ~1.13M was genuinely new (input + output). The other ~4.9M was cached prefix re-billed on every call.
- **Notes:** 101 calls for a one-line fix is 7–10x more than it should be. Muse Spark got stuck in stall loops, re-reading the same test file ~10 times, running `git status`/`git diff` over and over without committing, and only pushed the PR after I nudged it with "Where's the PR?" A tight run should have been 10–15 calls in 5–7 minutes.

### Opus 4.8 — PR #167

- **Wall clock, prompt to PR pushed:** 4m 11s.
- **Provider-metered tokens:** ~1.44M total across 18 LLM calls (grouped into 2 agent turns). Unique new tokens (input + output): ~42k. Cache read (re-billed prefix): ~1.38M, which is 95.8% of the total. Breakdown: input 34,165 · output 7,795 · cacheRead 1,383,489 · cacheWrite 19,312.
- **Notes:** No stall loops. Opus went straight to the fix and pushed the PR in 18 calls — about a fifth of Muse Spark's 101.

## The judge

To score the subjective stuff, I didn't want to grade myself, so I brought in a third model as a neutral referee: GPT-5.5, via Codex, running in a Hermes agent. I gave it both PRs and asked it to compare them on accuracy, completeness, and code quality.

Its verdict: both PRs are correct and low-risk, and they make the identical production change. It called it a tie on the fix itself, the healthy-purchase path, and the risk profile. Where it gave Opus 4.8 the edge was code comments and test clarity — Opus explained the subtle analytics invariant (why reusing the funnel's event id double-counts, and how the new event doubles as a production signal for a related pricing bug). Muse Spark's fix was just as correct but more terse. The judge's final call was to merge the Opus PR, and I agree, because it leaves the code in a more maintainable state.

## The head-to-head

| | Muse Spark 1.2 | Opus 4.8 |
|---|---|---|
| Picked the right issue | Yes | Yes |
| Production fix | Correct, one line | Correct, one line |
| Regression coverage | Yes (both degraded cases) | Yes (same cases) |
| Wall clock, prompt to PR | 13m 36s | 4m 11s |
| LLM calls | 101 (with stall loops) | 18 (2 turns, no stalls) |
| Tokens (metered total) | ~6.04M | ~1.44M |
| Unique new tokens | ~1.13M | ~42k |
| Code comments / clarity | Terse but correct | Clearer — judge's edge |
| Judge's pick | Loser | **Winner** |

## So what did I learn?

Opus 4.8 won, but it was close. Both models landed the same correct fix on the same tiny task. The gap wasn't correctness — it was time taken, polish, and discipline. Opus wrote clearer, more maintainable code and got to the PR in about a third of the wall-clock time and a fraction of the tokens, without the stall loops that ballooned Muse Spark's run to 101 calls (Opus needed just 18). Muse Spark 1.2 is genuinely capable for a model I ran with zero harness help. It found the right issue and shipped a correct fix. It was just less efficient and less thorough with the explanatory bits, so it lost out.

This is an informal eval I set up in an afternoon. A few caveats worth calling out:

- **I deliberately did not compare dollar cost.** Opus 4.8 is an established, priced-for-market coding product with real product-market fit. Muse Spark 1.2 is brand new from a big-tech firm (Meta) and is being introduced at a heavily discounted rate to win adoption. Putting Opus's real per-token price next to Muse Spark's promotional free proxy would be an apples-to-oranges comparison that tells you nothing about which model is actually cheaper to run at steady state. So I compared efficiency by way of tokens and time rather than dollars.
- **N=1 task.** One tiny, unambiguous bug fix tells you about a narrow slice. It says nothing about architecture, big multi-file features, or long-horizon planning.
- **Harness-free cuts both ways.** Stripping the coding harness makes it a cleaner model-vs-model test, but in real life you'd use Claude Code or Muse Code, and those harnesses would change these numbers.

Unsurprisingly, for now I'm staying on Opus 4.8 as my daily driver. But Muse Spark 1.2 held up better than I expected for a brand-new model, and I'll keep an eye on it. If you want to run your own version of this, the setup is nothing fancy: two OpenClaw agents, the same repo, a small real issue, and a third model to keep score. You can get started with OpenClaw at [openclaw.ai](https://openclaw.ai), set up Muse Spark at [dev.meta.ai](https://dev.meta.ai/) (you'll need an account and credit card to use it), and set up Opus at [platform.claude.com](https://platform.claude.com/).
