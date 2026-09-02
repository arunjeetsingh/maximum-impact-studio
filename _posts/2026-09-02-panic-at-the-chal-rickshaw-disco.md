---
layout: post
title: "Panic at the Chal Rickshaw Disco"
date: 2026-09-02
author: Arun Singh
description: "A housekeeping update to Chal Rickshaw broke the Next Round button for every player, and the CI suite that should have caught it stayed green. Here's the GDScript shadowing bug that caused it, the same-day hotfix, and the release-gate work it kicked off."
image: /assets/img/posts/chal-rickshaw-panic-disco-cover.png
excerpt: "The first post-launch update of Chal Rickshaw was supposed to be invisible housekeeping. Instead it broke the Next Round button for everyone, ironically while shipping the telemetry that diagnosed it within hours. A GDScript static-method-shadowing bug, a same-day hotfix, an Apple expedited review, and the automated release gate born from the whole mess."
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/chal-rickshaw-panic-disco-cover.png' | relative_url }}" alt="A colorful disco-ball explosion of panic around the Chal Rickshaw driver, representing the game's Next Round button breaking after a routine update" loading="lazy">
</figure>

TLDR: The first post-launch update of Chal Rickshaw went out on August 11 and was a housekeeping release meant to make the game more efficient and bugs easier to investigate. Ironically, the release introduced a bug that broke the Next Round / Retry button, leaving players stuck after the first round. I found it when I tried to play the game on 8/12, and had a hotfix live the same day. To add insult to injury, the game would still show the interstitial ad every third tap of the retry/next round button. Worryingly, the CI test suite I had set up didn't catch the issue at all. This is the story of what went wrong, how I addressed it, and the long-term steps I'm taking to ensure this never happens again. Welcome to the next step in the Chal Rickshaw journey!

## Links

- 🍎 **iOS:** [Chal Rickshaw! on the App Store](https://apps.apple.com/us/app/chal-rickshaw/id6775334841)
- 🤖 **Android:** [Chal Rickshaw! on Google Play](https://play.google.com/store/apps/details?id=studio.maximumimpact.chalrickshaw)
- 📝 **Previously in this series:** [the App Store launch post]({{ '/2026/08/chal-rickshaw-live-in-app-store/' | relative_url }})

## A housekeeping release

A week after the App Store launch, I shipped the first real update: iOS 1.0.70 and Android 1.0.59. If you'd asked me what was in it for players, my honest answer would have been almost nothing, and that was by design. The release fixed two subtle things. Interstitial ads had a cache bug where already-shown ads were never removed, so occasionally the game would try to present a used ad and the overflow path slowly leaked memory. Round transitions got hardened against a nasty bug which could leave the game looking temporarily hung on the win screen if the user kept their finger down while playing.

This was also an instrumentation release. My local error log budget is eight events, and two known errors fired on every single launch, permanently eating a quarter of the budget with noise, so every error report had to be read through static. Players upgrading from early builds had ~300 analytics events clogging their outbox queue, delaying their real data behind weeks of doomed retries. And two production users had mysterious foreground deaths at the title screen with zero evidence attached, so I added breadcrumbs to make the next one diagnose itself.

In short, it was an update meant to fix minor UX issues and make my telemetry trustworthy, so that when something real broke, I'd see it clearly. Something real broke.

## When everything broke

On August 12, the morning after submitting, I sat down to play Chal Rickshaw. I won the first round, tapped Next Round, and nothing happened. I tapped again and still nothing happened. On the third tap I saw a full-screen interstitial ad. I dismissed it, tapped again, and still nothing. The game had shipped unplayable beyond one round on both stores, and it felt like I was farming for ad clicks. Yikes!

Fortunately, the freshly-shipped telemetry corroborated it immediately. The session log showed exactly what I saw on the device: three ad events about fifty seconds apart with no second round-start event in between, and a session ending with the player quitting from the win screen. The instrumentation I'd just shipped was working, by describing the update breaking the game.

And the "endless ad loop" was a misdirection. My first instinct was to distrust the ad code, but the ad frequency gate was working perfectly: two grace presses, then one ad every third press. Each press of Next Round was still counted as a round transition. The transitions just weren't working in real life.

## What broke the game?

The update's centerpiece was the `SceneRouter` class, which flushes touch state before every scene change, fixing the hang bug if a user held their finger down. It exposed three little static functions: `change_to`, `change_to_packed`, and `reload`. Turns out `reload` was where the issue was.

In Godot, a script with a `class_name` is itself an object — that is, it's an instance of the engine's `Script` class. And `Script` already has a built-in method called `reload(keep_state: bool)`. So when the win screen called `SceneRouter.reload(self)`, the GDScript analyzer looked at my static function, said "sure, that's a valid call," and compiled it happily. But at runtime, the engine dispatched to its own native `Script.reload` instead. Argument conversion failed silently (the built-in `reload` wanted a bool but got a game object), the call no-oped, and the scene never reloaded. This is one of the key pitfalls of dynamically typed languages like GDScript (and JavaScript): statics on `class_name` scripts are silently shadowed by same-named built-ins. The analyzer accepts the call, the runtime prefers the native method.

Why did CI miss it? Three reasons, each a little embarrassing and each instructive:

1. The other functions (`change_to` and `change_to_packed`) didn't collide with any native name, so the smoke tests that mainly exercised these passed.
2. The test suite for the update thoroughly tested the touch machinery and even sabotage-verified it. But the two-line wrapper functions themselves were only ever checked by grep, never actually executed by a test.
3. There was no test anywhere that pressed Next Round and asserted a new round actually started, because it was "just" two lines of glue.

## The same-day hotfix

First move, before any code: I halted the Android staged rollout in the Play Console, capping how many players could receive the broken build. On iOS, I hadn't enabled a phased release for 1.0.70, so it went to everyone at once. That asymmetry became a process rule by the end of the day (more below).

The fix itself, once the shadowing was understood, was the one-line-rename class of fix: `reload` became `reload_scene`, which collides with nothing. In addition to the rename, I added two tests whose absence had let it through:

1. **An end-to-end test.** A real button inside a real scene whose press handler calls the wrapper mid-input-dispatch, like the win screen does, and asserts the scene instance actually changes.
2. **A native-shadow lint.** No public method on the router may share a name with any engine built-in, so I can address the whole class of bug, not just this instance.

Then I verified both tests had teeth by putting the bug back. The tests failed with the exact error from production, and the lint flagged `reload`. The hotfix builds were cut a few hours after I'd found the problem.

## Working with Apple

Android's staged rollout meant the fix could start reaching players as soon as I promoted 1.0.60. Apple was the long pole because normal App Review takes about a day, and every hour meant more players hitting a wall after round one.

Luckily, Apple has a process for exactly this situation called an expedited review request. It's a short form (App Store Connect > Contact Us > request an expedited review) where you explain the situation. I kept it factual: the current live version has a critical regression that makes the game unplayable past the first round, here's the one-line cause, here's the fix build, already submitted. Apple explicitly asks that you not abuse this lane, and since I'd never used it before, I had credibility as a first-time user. I submitted the updated app for review at 11:22am PT, and Apple had turned it around by 1:52pm PT — basically 2.5 hours. Good job, Apple! The approval email looked just like a regular approval email.

## Never again

The short-term process changes came out of that first hour of damage control:

- Every production release now ships gradually: phased release on iOS, staged rollout on Android, no exceptions. The Android rollout was pausable, but the iOS release wasn't.
- A human plays the store build before any production promotion. Not the dev build, the actual store binary — TestFlight on iOS, the internal testing track on Android. Win a round, press Next Round, reach round two. Crash on purpose, press Retry.

But the real answer is bigger, and it's been my main engineering investment ever since: an end-to-end release gate that plays the game and records a video of it for review. Five days after the hotfix, I merged the design for it. Before any build gets designated launch-bound, a harness boots the real game and plays through eight-plus rounds: pressing Next Round, deliberately crashing, pressing Retry, watching a rewarded ad for double earnings, buying an item in the shop, and using that item in the game. There are assertions on every leg, and every run produces a date-stamped video that I or an LLM running as a judge can review.

Two design decisions trace straight back to this bug:

- The very first assertion in the harness is that the scene instance actually changed after Next Round.
- The harness presses buttons by injecting real input events through the engine's viewport. It never calls the button's handler directly, because this bug lived in the layer a shortcut would have skipped.

As of this week, that harness runs green on every build on my desktop tier, with five of the eight legs enforced and the remaining ad-path legs in progress. It has already changed how I think about testing. The theme of this whole incident — code that was present but never executed, tests that checked text instead of behavior — kept reappearing while building the harness, too. The standing rule I've adopted is: no guard counts until I've re-introduced the defect it guards against and watched it go red. This makes the building process a little slower, but every step forward is validated. Since Claude Code, OpenClaw, and Codex do most of the work, we're not talking about taking away from my productive time.

## The journey continues

Launch taught me how hard it is to get a game into the store. This incident taught me another lesson about how easy it is to break a game that's already there. The update designed to be invisible became the most visible failure the game has had, but the instruments it shipped helped diagnose it within hours, which is either irony or vindication, depending on how charitable you're feeling.

If you write GDScript, take the shadowing gotcha with you. If you ship anything at all, remember that green tests tell you what you asked, but only executed behavior tells you the truth.

## 🛺 Go play!

Chal Rickshaw is on the App Store and Google Play with the Next Round button in full working order.

Chal, chal, chal! 🛺💨
