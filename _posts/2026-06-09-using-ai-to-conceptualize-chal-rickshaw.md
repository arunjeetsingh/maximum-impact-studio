---
layout: post
title: "Using AI to conceptualize a game: Chal Rickshaw!"
date: 2026-06-09
author: Arun Singh
description: "How I worked with Chintu (my OpenClaw bot on Opus), Gemini's Nano Banana Pro, and Veo to turn a handful of reference images into two concept videos for Chal Rickshaw! — an endless runner set on the streets of Delhi."
---

TLDR: Ever since I first saw Temple Run and Subway Surfers, the idea of a game where players weave through the chaotic streets of Delhi got stuck in my head. With some time on my hands and an ongoing fascination with agentic engineering, I finally took the plunge and started building it. For now I'm calling it Chal Rickshaw! I'm sharing a couple of concept videos of what I'm going for. Both videos were produced by Chintu, my OpenClaw bot, running on Opus and orchestrating Gemini's Nano Banana Pro for image generation and Veo for video generation. Here's the story of how a few reference images turned into a moving concept reel and how we iterated it into a second, sharper cut.

## Building the concept videos

### Starting with a concept

Like a lot of game ideas, this one was easier to feel than to describe. Instead of writing a brief, I generated a handful of reference images in Gemini to capture the vibe. A behind-the-rickshaw chase view down a busy Delhi street with AUTO STAND and TEA STALL signs and an overhead street sign in English and Hindi. A green-and-yellow auto with two passengers, dust trails, and a display with score, coins and power-ups. Those frames became the visual bar. They showed the desi street interactions I wanted to build the whole game around:

<figure class="post-figure post-figure-portrait">
  <img src="{{ '/assets/img/posts/chal-rickshaw-hero.jpg' | relative_url }}" alt="Concept art of Chal Rickshaw: a behind-the-rickshaw chase view down a busy Delhi street with shop signs and an overhead route banner" loading="lazy">
  <figcaption>Concept art: the behind-the-rickshaw hero view that set the visual bar.</figcaption>
</figure>

Then I storyboarded some key elements:

- A gau mata (holy cow) wandering into the lane. Something to respect and dodge, never hit!
- A Delhi Police constable (lovingly called thulla) flagging you down for a little chai pani.
- A babu/neta (VIP) convoy that brings the whole road to a crawl.

### From stills to a moving reel

Reference stills are nice, but I wanted to see it move. So Chintu and I worked together to expand the world. We generated more scenes with Nano Banana Pro with each new beat, the cow approach, the constable shakedown, the VIP convoy, and the victory bonus rendered in the same illustrated style by referencing the existing start frame so everything stayed consistent.

Then, overnight, Chintu stitched it all together into the first concept video animating each still with subtle motion and sequencing the beats into a single portrait-format reel using Veo. I woke up to a finished sizzle reel built from art that didn't exist 24 hours earlier.

<figure class="post-video">
  <video controls playsinline preload="metadata" poster="{{ '/assets/img/posts/chal-rickshaw-concept-v1-poster.jpg' | relative_url }}">
    <source src="{{ '/assets/video/posts/chal-rickshaw-concept-v1.mp4' | relative_url }}" type="video/mp4">
    Your browser doesn't support embedded video. <a href="{{ '/assets/video/posts/chal-rickshaw-concept-v1.mp4' | relative_url }}">Download the clip</a>.
  </video>
  <figcaption>Concept video 1: the first version that showed all the interactions stitched together.</figcaption>
</figure>

### Iterating on the interactions

The first cut was fun, but watching it back surfaced things only a human (and a desi one) would catch. We did a focused revision pass on the interactions:

- **Gau mata:** In the first cut the auto could appear to hit the cow. That's a no go! We retired that beat entirely. Now the cow drifts between lanes on approach, the reward (a "Gaumata Speed Award") is only for a clean dodge, and a mistake turns into a "say sorry / feed the cow" moment instead of a collision.

<figure class="post-figure post-figure-portrait">
  <img src="{{ '/assets/img/posts/chal-rickshaw-cow.jpg' | relative_url }}" alt="Concept art of a holy cow (gau mata) wandering into the rickshaw's lane" loading="lazy">
  <figcaption>Gau mata wandering into the lane — respect and dodge, never hit.</figcaption>
</figure>

- **Thulla:** This is everyone's favourite scene, so we gave it more personality. An angry preamble as he flags you down, and a satisfied payoff line once he's satisfied. More character, more comedy.

<figure class="post-figure post-figure-portrait">
  <img src="{{ '/assets/img/posts/chal-rickshaw-thulla.jpg' | relative_url }}" alt="Concept art of a Delhi police constable (thulla) flagging the rickshaw down for a bribe" loading="lazy">
  <figcaption>The thulla shakedown — flag you down, collect the chai paani.</figcaption>
</figure>

- **VIP/babu convoy:** Already landed well, so we left it as-is.

<figure class="post-figure post-figure-portrait">
  <img src="{{ '/assets/img/posts/chal-rickshaw-convoy.jpg' | relative_url }}" alt="Concept art of a VIP/babu convoy with police escort jamming the whole road" loading="lazy">
  <figcaption>The VIP/babu convoy with police escort that jams the whole road.</figcaption>
</figure>

We regenerated just the affected frames then rebuilt the reel with Veo into a second, tighter concept video.

<figure class="post-video">
  <video controls playsinline preload="metadata" poster="{{ '/assets/img/posts/chal-rickshaw-concept-v2-poster.jpg' | relative_url }}">
    <source src="{{ '/assets/video/posts/chal-rickshaw-concept-v2.mp4' | relative_url }}" type="video/mp4">
    Your browser doesn't support embedded video. <a href="{{ '/assets/video/posts/chal-rickshaw-concept-v2.mp4' | relative_url }}">Download the clip</a>.
  </video>
  <figcaption>Concept video 2: being kinder to gau mata and jazzing up the thulla.</figcaption>
</figure>

### What this workflow looked like

The fun part is how little of this was "me drawing" and how much was directing:

- I set the creative direction with reference art and feedback in plain language.
- Chintu (Opus) orchestrated the tools by prompting Nano Banana Pro for consistent frames, cleaning them up, and driving Veo to animate and stitch.
- We iterated conversationally. I would watch the cut, give notes, Chintu would regenerate only what changed and rebuild. Tight loop, no asset pipeline ceremony.

### What's next

These are concept reels, not the game itself — but the actual Godot build is already underway, with the same cow, thulla and convoy interactions making their way in. More to share soon. For now, Chal Rickshaw!
