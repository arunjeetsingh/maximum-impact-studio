---
layout: post
title: "Chal Rickshaw: Android Public Beta"
date: 2026-06-23
description: "Chal Rickshaw is now live as a public beta on Android. Here's what landed this week — a multi-round career, a persistent rupee wallet, a punchier crash sequence, finger-down steering, and a festive Diwali night round."
image: /assets/img/posts/chal-rickshaw-android-beta-cover.png
excerpt_separator: <!--more-->
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/chal-rickshaw-android-beta-cover.png' | relative_url }}" alt="Chal Rickshaw — now in Android public beta, on Google Play and iOS TestFlight" loading="lazy">
</figure>

TLDR: Chal Rickshaw is now live as a **public beta on Android** — anyone can jump in. Grab it on Google Play (link below) and tell me what you think. Since last week the game grew up a lot: a punchy new crash/loss sequence, a full multi-round career, a persistent ₹ wallet so your kamai carries across rounds, smoother "keep your finger down" steering, and a festive Diwali night round. It's all on Android and iOS — go play.
<!--more-->

## Get the beta

- 🤖 **Android public beta:** [Google Play opt-in](https://play.google.com/apps/testing/studio.maximumimpact.chalrickshaw)
- 🍎 **iOS public beta (TestFlight):** [https://testflight.apple.com/join/Zgn6udB4](https://testflight.apple.com/join/Zgn6udB4)
- 📝 The concept that started it all: [Using AI to conceptualize a game]({% post_url 2026-06-09-using-ai-to-conceptualize-chal-rickshaw %})

## What's new this week

### 🛺 A real career: multiple rounds

Chal Rickshaw is no longer a single endless dash. It's now a **round-based career**, where each round has its own name, mood, and difficulty. You climb the ladder one fare at a time.

You'll start gently on **Naya Driver** (empty roads, no cops, learn the controls), then things heat up: **Police Police** puts thullas on the street, **Itna Traffic?** gets the cars moving, **Raat Ho Gayi** drops you into the night, **VIP Movement** throws costly convoys in your way, **Busy Din** piles on a two-lane cow herd, and so on. Clear a round and you advance; the later rounds loop on the hardest difficulty so there's always one more run in you.

Under the hood the rounds are fully data-driven. Every knob — traffic, cops, cow lanes, fare and speed multipliers, night/Diwali flags — is authored in a config the game loads at startup, so balancing and adding new rounds is quick.

### 💰 Your earnings carry across rounds

Points are now money. There's a single live ₹ wallet on the HUD that tracks fares earned, minus the fines you rack up (bribing a thulla, getting stuck behind a VIP convoy). Finish a round and your earnings get **banked** into the wallet, and it all **persists between sessions**. Eventually, you'll be able to buy upgrades with these rupees. The win screen breaks down your **Total Kamai** with fare and fine line-items, so you can see exactly where the money went.

### 🎬 A new crash / loss sequence

A crash used to hard-cut straight to a defeat screen — abrupt and anticlimactic. Now a crash plays out as a proper **beat**. It turns a loss into a moment instead of a letdown.

- The world **freezes** on impact (hit-stop) with a hard screen-shake.
- A crash SFX **slams** into the silence as the engine and traffic audio duck out.
- A white impact flash, then a big **"SIYAPPA!"** banner pops over the wreck.
- The screen itself looks **shattered**.
- Then it fades to dark and hands off to the defeat screen.

### ✋ Smoother controls — keep your finger down

Beta testers told me the steering felt fiddly: you had to lift your finger after every swipe to change lanes again. Annoying mid-run. Now steering is **continuous, Temple-Run style** — you can keep your finger down and just **slide** to steer. There's also a **swipe-up to jump** so held-finger players can hop without breaking contact, and quick taps still jump too. Lane sensitivity got tuned so casual flicks don't overshoot.

### 🪔 The Diwali round

The headliner. **Diwali** is a full festive night round: a deep navy night sky with a hand-drawn moon and a procedural starfield, the city skyline lit up with warm windows, and a celebratory audio mix — festive music with interleaved firework bursts layered under the gameplay. And because it's Diwali, the fares pay **double**! Your best kamai night of the career, if you can survive the holiday traffic.

## Go play

That's the week. Hop into the **Android public beta**, climb the rounds, stack your kamai, and see how far you get before the SIYAPPA. iOS folks, TestFlight's got you too. Feedback is always welcome!
