---
layout: post
title: "Build a GitHub Resume with OpenClaw: A Setup Guide"
date: 2026-06-22
author: Arun Singh
description: "Everyone says they can vibe code now. Far fewer can point at a public GitHub repo and say I built and shipped this. Here's how to stand up an OpenClaw agent and turn that claim into living evidence."
image: /assets/img/posts/build-github-resume-cover.png
excerpt_separator: <!--more-->
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/build-github-resume-cover.png' | relative_url }}" alt="A cheerful red OpenClaw lobster reaching out with its claw toward the GitHub logo" loading="lazy">
</figure>

TLDR: Want proof you can build using an agent? Stand up an OpenClaw agent on a cheap machine, give it a Claude account and a personality, talk to it from Telegram or WhatsApp, and have it build a real project end-to-end on a public GitHub repo with source control, Continuous Integration (CI), and Continuous Deployment (CD) included. That public repo is now living evidence that you can ship. Here's exactly how to set it up.
<!--more-->

## Why do this?

Everyone says they can "vibe code" now. Far fewer can point at a public GitHub repo and say "I built and shipped this." That repo is the difference between claiming the skill and proving it. The fastest way to get there is to identify a discrete problem and work with your agent to tackle it. By setting up CI and CD as you do it, you set yourself up for a mature solution that can accommodate public contributions.

Here's my step-by-step recommendation.

## The setup

1. **Get a machine to run it on.** Find a spare desktop you have lying around, or buy a cheap Mac mini. If neither is an option, rent a virtual private server (VPS).
2. **Get yourself a Claude platform account.** This is what your agent will think with.
3. **Install OpenClaw.** Log in to your desktop/VPS (using whatever access details you set up), go to [openclaw.ai](https://openclaw.ai), and follow the instructions to install it.
4. **Hook up your model and budget.** If you choose Anthropic models, OpenClaw walks you through connecting your Anthropic account. This is the point where you start burning tokens and paying for them. Expect to spend tens of dollars to get fully set up.
5. **Name your agent and give it a personality.** Pick a name you like and answer the personality questions. If your goal is to code, tell it to imagine it's a software engineer and be as descriptive as possible. The more specific you are, the more useful it'll be.
6. **Set up a channel to talk to it.** I like Telegram but WhatsApp also works. This is how you'll chat with your agent day to day.
7. **Now the hardest part, pick a discrete, achievable project.** I wanted to keep a close eye on my token spend so I decided to create an app to track that. A web app is even easier to start with. If you're out of ideas, hop on to Upwork or Fiverr, pick a posted project, and just build it for practice. You don't actually have to deliver it to the client or bid on it.
8. **Set up a GitHub account and ask your agent to use it for source control, CI, and CD.** Getting CI/CD in place early means every change is built, tested, and shipped automatically which is exactly the muscle you want to show off. This also preps your repo/product for public contributions.
9. **Make the GitHub repo with your code public.** This public repo is now evidence that you can use agents to build solutions. Anyone can look at the commit history, the PRs, the CI runs, and see real work.

## For extra credit

Ship something simple but real. A working, deployed thing beats ten half-finished experiments. I used the approach above to ship the [TokenCounter]({% post_url 2026-05-31-announcing-tokencounter %}) app I've been talking about in my last few posts.
