---
layout: post
title: "TokenCounter: New features, and now on Android too!"
date: 2026-06-16
description: "TokenCounter 1.1 is here — more accurate spend tracking, a monthly spend-limit gauge with a 90% alert, and the app is now live on Google Play alongside iOS. It's also fully open source."
image: /assets/img/posts/tokencounter-1-1-cover.png
excerpt_separator: <!--more-->
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/tokencounter-1-1-cover.png' | relative_url }}" alt="TokenCounter 1.1 — your Anthropic API spend at a glance, now on iOS and Android" loading="lazy">
</figure>

I launched TokenCounter a couple of weeks ago, a small, private app that lets you track your Anthropic token spend as you go on your agentic journey. Version 1.1 just dropped with more accurate spend tracking and the ability to set a monthly spend limit (with a heads-up alert before you blow past it). And the big one: **TokenCounter is now on the Google Play Store too.** It's also fully open source.
<!--more-->

- 📱 iOS App Store: [https://apps.apple.com/app/id6772613833](https://apps.apple.com/app/id6772613833)
- 🤖 Google Play: [https://play.google.com/store/apps/details?id=studio.maximumimpact.tokencounter](https://play.google.com/store/apps/details?id=studio.maximumimpact.tokencounter)
- 💻 Git repo: [https://github.com/arunjeetsingh/token-tracker](https://github.com/arunjeetsingh/token-tracker)

## What is TokenCounter?

If you're building with Claude models (Opus, Fable etc.) using agents, pipelines, or by directly using the API, your Anthropic bill has a way of sneaking up on you. TokenCounter reads your organization's usage straight from Anthropic's Cost API and shows it as a clean, month-to-date dashboard on your phone:

- Finalized month-to-date spend across your whole org
- Today's running estimate — closes the gap Anthropic's daily report leaves open
- Per-model breakdown so you can see what's actually costing you
- A 30-day trend for a quick gut-check on your burn

You bring your own admin API key. It stays in the device's secure storage and is never sent anywhere except `api.anthropic.com`. No account, no server, no analytics, no trackers. Just your numbers.

## TokenCounter is open source

TokenCounter is now fully open source. If you want to see exactly how your key is handled, audit the privacy claims, file an issue, or send a PR, go to [https://github.com/arunjeetsingh/token-tracker](https://github.com/arunjeetsingh/token-tracker) and get started.

## What's new in 1.1

Set a monthly spend limit and watch the new dashboard gauge fill as the month goes on. It turns orange at 80% and red when you're over.

- **Spend-limit gauge on the dashboard** so you always know whether you're under, on track, or over budget at a glance.
- **Opt-in alert the first time you cross 90% of your limit** in a month. This is a quiet early-warning system I find useful to avoid being surprised that the API stopped responding. It's off by default and only asks for notification permission when you turn it on.
- **Quick links to the Anthropic Console** for your real billing limit, credit balance, and auto-reload.
- **Fixed:** Claude Opus 4.8 usage no longer inflates today's estimate by charging the legacy Opus rate, so your running number is more accurate.
- **Fixed:** a key revoked right after setup now returns you to onboarding instead of a stuck error.

A quick note on the spend limit: Anthropic's public Admin API exposes your spend amount, but not your configured limit, credit balance, or auto-reload. So the limit you set in TokenCounter is a local target on your device, and anything that genuinely lives in the Console deep-links you straight there.

## Now on Android

TokenCounter started on iOS. As of 1.1, the Android app is live on Google Play at full feature parity. It has the dashboard, spend limit, alerting, same privacy posture, and is built natively in Jetpack Compose. Your key lives in the Android Keystore, protected by your device lock/biometrics, and a one-tap "Disconnect" wipes it.

## What's next: OpenAI cost tracking

TokenCounter is Claude-first today. Next on the roadmap is OpenAI token cost tracking — same private, bring-your-own-key, on-device approach, so you can keep an eye on your OpenAI spend alongside Anthropic's.

## Get TokenCounter

- 📱 iOS App Store: [https://apps.apple.com/app/id6772613833](https://apps.apple.com/app/id6772613833)
- 🤖 Google Play: [https://play.google.com/store/apps/details?id=studio.maximumimpact.tokencounter](https://play.google.com/store/apps/details?id=studio.maximumimpact.tokencounter)

Would love to hear what you think — and if there's a provider you want supported next, the issue tracker is open.
