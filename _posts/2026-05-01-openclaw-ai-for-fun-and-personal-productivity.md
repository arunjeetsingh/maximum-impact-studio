---
layout: post
title: "Chintu: OpenClaw.AI for Fun and Personal Productivity"
date: 2026-05-01
author: Arun Singh
description: "You don't need a fancy Mac Mini to try out OpenClaw. I dug out an old box I built in 2013 and was up and running in about 3 hours."
---

<p class="post-tldr"><strong>TLDR:</strong> You don't need a fancy Mac Mini to try out OpenClaw! I dug out an old box I built back in 2013 and was up and running in about 3 hours. My bot is called Chintu (IYKYK). However, be prepared to spend ~$100 on Claude tokens to get set up and about $20 a day even with light use because an old machine won't run local models for you. That's where a newer machine (eg: Mac Mini) with lots of RAM helps.</p>

## The setup

Might be late to the game but I spent the weekend setting up OpenClaw for Chintu on an old custom box I built back in 2013. Some details:

- Box has a 2012 vintage Core i5, integrated graphics, 8GB ram, and a 256GB Samsung SSD. So it is definitely no speed monster.
- Ubuntu LTS running as OS, OpenClaw on top
- Claude platform subscription to use Opus 4.7
- Connected to a WhatsApp channel so we can talk to each other easily

## What I've used it for so far

Over the weekend:

- I connected Chintu to my SmartThings hub and some other home automation stuff (Ring, Govee, Lutron, Hue)
- I asked Chintu to start using Hinglish (portmanteau of Hindi+English and what a lot of us colloquially speak in north Indian cities) and the results have been most gratifying
- Had it install faster-whisper for some basic speech recognition so it can also understand when I send a voice note. Results are middling at best.
- I used Chintu to shop for outdoor furniture covers where it did quite well despite not having access to my Amazon account

## What I learned

- My main learning is that you don't necessarily need a Mac Mini to get up and running with OpenClaw however you do need a machine with lots of RAM if you want to host your own models.
- Right now, I'm using Anthropic's Claude Opus 4.7 and the cost is already $68 for 3 days of use.
- That said, I did a lot of setup work over the past 3 days and continue to. Setting things up typically consumes more tokens and is less cache friendly work than daily use. Right now my Claude cache hit rate is 40%.

## Where I hope to go next

- I want to get Chintu to talk back to me in a Delhi Hinglish accent. ElevenLabs seems to have some TTS models that fit the mold.
- I want to dig up one of my old Raspberry Pis and buy a mic hat to set Chintu up to listen and talk to me. I think someone else spoke about doing that already and it doesn't seem super complicated.

<figure class="post-figure post-figure-pair">
  <img src="{{ '/assets/img/posts/chintu-intro-1.jpg' | relative_url }}" alt="Chintu's WhatsApp introduction message listing its capabilities" loading="lazy">
  <img src="{{ '/assets/img/posts/chintu-intro-2.jpg' | relative_url }}" alt="Chintu describing its personality and privacy boundaries" loading="lazy">
  <figcaption>Chintu's self-introduction over WhatsApp — capabilities, personality, and boundaries.</figcaption>
</figure>
