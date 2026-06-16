---
layout: app
slug: tokencounter
title: TokenCounter
description: A privacy-friendly app for iOS and Android to monitor your Anthropic API spend.
permalink: /tokencounter/
---

TokenCounter reads your organization's usage straight from Anthropic's Cost API
and shows it as a clean month-to-date dashboard — finalized spend, today's
running estimate, and a per-model breakdown. Bring your own admin API key; it
stays in your device's secure storage (iOS Keychain / Android Keystore) and is
never sent anywhere except `api.anthropic.com`.

No account, no server, no analytics. Just your numbers.

**Now on iOS and Android.** The Android app is live on Google Play at full
feature parity with iOS — same dashboard, same privacy posture, built natively
in Jetpack Compose.

**New in 1.1:** set a monthly spend limit and watch the dashboard gauge fill as
the month goes on — it turns orange at 80% and red when you're over — plus an
opt-in alert the first time you cross 90% of your limit. The spend limit is a
local target on your device; quick links take you to the Anthropic Console for
your real billing limit, credit balance, and auto-reload.

**Open source.** TokenCounter is fully open source — audit exactly how your key
is handled, file an issue, or send a PR on
[GitHub](https://github.com/arunjeetsingh/token-tracker).

**What's next:** OpenAI token cost tracking — same private, bring-your-own-key,
on-device approach, so you can keep an eye on your OpenAI spend alongside
Anthropic's.
