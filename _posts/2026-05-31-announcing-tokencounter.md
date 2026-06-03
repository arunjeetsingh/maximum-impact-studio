---
layout: post
title: "Announcing TokenCounter"
date: 2026-05-31
author: Arun Singh
description: "TokenCounter is live on the App Store — track your Anthropic API spend at a glance: month-to-date cost, today's running estimate, and a per-model breakdown. My first app built with an AI agent."
---

TokenCounter is live on the App Store. 🚀

TokenCounter helps you track your Anthropic API spend including month-to-date cost, today's running estimate, and a per-model breakdown in a simple iOS app. This is the first time I've built an app with an AI agent (Chintu, my OpenClaw/Claude buddy). From the first github commit to building the data layer to App Store review took about 7 days. A lot of that time was spent with me reviewing Chintu's code because I really hate the idea of shipping code I don't understand. Like an in-person review, Chintu walked me through all the bits I didn't understand and implemented good patterns (lots of DI for testing!) when I called out shortcuts it took.

**Why I made it:** if you're burning Anthropic tokens through an agent (OpenClaw, Hermes, whatever), you know Anthropic's cost dashboard is painful on mobile. I wanted to see what I spent at a glance and I figured others would too.

**What's next:**

→ Android app (hopefully landing next week)

→ OpenAI + Gemini cost tracking

→ Custom date ranges, trends, predictions, and spend alerts

This is also the first launch from [maximumimpact.studio](https://maximumimpact.studio), a new space I'm spinning up to build apps and games that bring joy. Mostly to me, but hopefully also to others :)

📲 App Store: [https://apps.apple.com/app/id6772613833](https://apps.apple.com/app/id6772613833)

🧪 TestFlight beta: [https://testflight.apple.com/join/Jb7eKT8c](https://testflight.apple.com/join/Jb7eKT8c)

Try it, break it, tell me what's missing. A deeper why/how writeup comes next week.

<figure class="post-figure post-figure-pair">
  <img src="{{ '/assets/img/posts/tokencounter-dashboard.png' | relative_url }}" alt="TokenCounter dashboard showing month-to-date Anthropic spend, a 30-day trend chart, and a top-models breakdown" loading="lazy">
  <img src="{{ '/assets/img/posts/tokencounter-onboarding.png' | relative_url }}" alt="TokenCounter onboarding screen explaining the one-time Anthropic admin key setup" loading="lazy">
  <figcaption>TokenCounter on iOS — the spend dashboard, and the 30-second one-time setup.</figcaption>
</figure>
