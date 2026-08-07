---
layout: post
title: "Chal Rickshaw is Live in the iOS App Store"
date: 2026-08-07
author: Arun Singh
description: "Chal Rickshaw is now live on iOS. Seven trips through App Review: a consent form users couldn't act on, a frozen sign-in screen, purchases that required sign-up, and a paywall the reviewer couldn't find — and how each rejection made the game better."
image: /assets/img/posts/chal-rickshaw-appstore-cover.png
excerpt: "After seven rounds of App Review, Chal Rickshaw is live in the iOS App Store! An EEA consent bug, a Godot engine freeze, a correct password that wouldn't work, in-app purchases that silently stopped vending, and a paywall the reviewer couldn't find. Every rejection was right, and every one made the game better. Read on for the gory details."
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/chal-rickshaw-appstore-cover.png' | relative_url }}" alt="Chal Rickshaw's turbaned Sikh driver celebrating with his phone raised in front of an App Store badge, his green-and-yellow auto-rickshaw garlanded with marigolds on a festive Delhi street" loading="lazy">
</figure>

TLDR: After seven rounds of App Review, Chal Rickshaw is finally live in the iOS App Store! Getting through required fixing an EEA-specific consent bug, tracking down a Godot engine issue that froze the sign-in screen, and rebuilding the sign-in flow so a stranger could survive it. In the process the game genuinely got better: you can now buy rupee packs (the in-app currency top-ups) without signing in at all, and there's a proper in-app account deletion flow. Some rounds were just questions and answers, and one rejection I could have avoided entirely. I learned a lot about how App Review actually works, and more than I ever wanted to know about how App Store Connect handles in-app purchases. Read on for the details of the journey, or just install the game and give it a play!

## Links

- 🍎 **iOS — live now:** [Chal Rickshaw! on the App Store](https://apps.apple.com/us/app/chal-rickshaw/id6775334841)
- 🤖 **Android — live now:** [Chal Rickshaw! on Google Play](https://play.google.com/store/apps/details?id=studio.maximumimpact.chalrickshaw)
- ▶️ **Watch the preview:** [https://youtu.be/UT7DxPjG2TU](https://youtu.be/UT7DxPjG2TU)
- 📝 **Previously in this series:** [the Play Store launch post]({{ '/2026/08/chal-rickshaw-live-in-play-store/' | relative_url }})

## 7 submissions over 13 days

From the first rejection on July 23 to approval on August 5, Chal Rickshaw went through App Review seven times. Here's a quick timeline:

- **Jul 23:** Reviewer saw a consent form in EEA they shouldn't have and ran into a frozen sign in screen
- **Jul 27:** I resubmitted without attaching the new build. Had to cancel the submission and start from scratch.
- **Jul 28:** Rejected because of sign-in errors. To the reviewer, it looked like the demo password didn't work
- **Jul 30:** Questions about where to find in-app purchases and about the paid-apps agreement
- **Jul 31:** Rejected because reviewer didn't think purchases should be gated behind account creation. The reviewer also brought up account deletion.
- **Aug 4:** Rejected because demo account balance was too low (multiple reviews drained it) and reviewer couldn't find IAP to add rupees. Discovery issue.
- **Aug 5:** Approved!

Every review was something real. Even the one that made me frustrated because the feature the reviewer was asking for actually existed. In that case, I had a discovery issue.

## The consent form nobody could say yes to

The first rejection was a geography lesson. Apple reviews from wherever Apple pleases, and this reviewer's traffic came from an EEA IP address. In the EEA, Google's ad SDK shows its GDPR consent form. The problem is that the form includes choices about personalized ads, and Chal Rickshaw doesn't do cross-app tracking and never requests iOS's App Tracking Transparency permission. So the app was effectively asking users to consent to something it could never deliver.

The simplest fix, albeit with a small revenue compromise, was to remove the form for EEA users. European iOS players get an ad-free game, and ad requests elsewhere explicitly ask for non-personalized treatment. Proving the fix worked meant making my simulator pretend to be in Europe, which turned into a mini-project of its own because the official Godot export templates don't ship an Apple Silicon simulator build. So I had to compile the engine's simulator library from source to reproduce the bug on the same iPad model Apple tested on.

## Frozen sign-in screen

The same round said the sign-in page froze on iPad. I couldn't reproduce it, until I noticed the timestamps in Apple's screenshots: the consent form had appeared one minute before the freeze. That was the clue. There's a known Godot engine issue ([godotengine/godot#102393](https://github.com/godotengine/godot/issues/102393)) where a touch that's in flight when a native overlay appears never gets its release delivered back to the engine. Godot is left believing a phantom finger is still pressing the screen, and the UI stops responding to everything else. The fix injects a synthetic touch-release after every native overlay closes and whenever the window regains focus. Invisible when it isn't needed, and it un-freezes the screen when it is. The same fix also applied to the Android version, which has native ad overlays of its own.

## Sign-in problems

The next rejection was entirely about sign-in. Two findings. First, the reviewer typed a brand-new email address into the SIGN IN screen which is for existing accounts. Unfortunately, instead of telling them they needed to create an account we had a confusing backend error: "Signups not allowed for otp." To a normal person that looks like a broken app. The fix maps backend errors to human words that point at the right place ("New here? LINK ACCOUNT creates one.").

Second, the reviewer reported the demo account didn't work due to invalid login credentials. I checked the access code (effectively the demo account's password) against the live backend, and it was correct. Turns out the code field was masked, and because the field is drawn by the game engine rather than iOS, the system keyboard treated it as ordinary text and capitalized the first letter. The reviewer couldn't see the stray capital. The fix was to have the code field show its text and the code itself got rotated to all-lowercase characters that are hard to mistype.

## Purchase issues

A reviewer reported they couldn't complete a purchase. We found an issue wherein the store could brick itself. The app asked Apple for its product catalog only once at launch. If that request failed, purchasing stayed broken for the whole session, and the error message blamed the player's device. This is likely what the reviewer saw. Now the catalog request retries with backoff, refreshes whenever the Bazaar opens, and says something honest while the store is still connecting.

The game also required linking an email before buying rupee packs. Apple does not allow gating of non-account based content behind account creation (Guideline 5.1.1(v)). Rupee packs aren't account-based content. But removing the gate and anonymizing purchases turned into a large project quickly because the assumption that purchasing users have an account went pretty deep in the architecture. Also, if a purchase isn't tied to a durable account, what stops someone from deleting the account and replaying the same store transaction for a second credit? The answer was a fraud-proofing rework I should have designed in the first place, and a linked wallet still follows players across devices if they want it to. Apple's rejection forced a better payment system than the one I built on purpose.

## The day my products stopped existing

During one of the reviews I also learned that in-app purchases have their own review state separate from the app. When Apple rejects your app, the in-app purchases attached to the submission end up in a "Rejected" state in App Store Connect. In that state the App Store returns them to your app as invalid products. My device logs showed that six products were requested but five of them were invalid. The five invalid ones were exactly the five sitting in "Rejected." Apple's current documentation (TN3186) says sandbox testing doesn't require submitting your IAPs for review. However, the archived documentation (TN2413) said a rejected binary breaks IAP testing. The archived version matches what I observed. The recovery was per-product. I had to edit each one so it returned to "Ready for Review," make sure all of them are attached to your next submission, and wait for propagation (about an hour). If your products suddenly come back invalid, check their review state in App Store Connect before you debug your code.

## Delete your account, on camera

The July 31 round also noted the app had no in-app account deletion. A web form alone doesn't satisfy Apple because ideally, they want the flow inside the app. They also asked for a screen recording of it in the review notes. So account deletion (and sign-out) became features, built and shipped mid-review-cycle. That's also why the Android launch post could already list in-app deletion as a bullet point. The feature was created to get past the App Store app review.

During the video recording I caught a real bug which I didn't have automated tests for. The app promised a 6-digit code but the backend (Supabase) was sending 8-digit ones. It worked because I allowed additional characters in the code field but the experience was obviously broken. I fixed it and added an automated test to ensure it doesn't happen again.

## The case of the missing paywall

This is my favorite rejection, because it looked absurd and was completely deserved. On August 4, Apple claimed "no in-app purchase paywall showed in your app". Their screenshot told the story better than the citation did. The Bazaar had opened on Speed Boost, an item priced in earned rupees. The wallet showed ₹500 because multiple reviews had drained the wallet of the review demo account. BUY was greyed out because the item costs ₹5000. Even worse, the message on screen said: "Not enough rupees — play a round to earn more!" Reading that screenshot as someone not familiar with the game I realized I was telling players the only way to get currency is to grind for it. The real-money packs were four swipes away, and nothing on that screen hinted they existed. The in-app products were hidden and the app was actively steering people away from it.

I made three fixes: 1) a gold ADD ₹ button next to the wallet that jumps straight to the rupee packs; 2) a "not enough rupees" message that points at the button instead of at grinding; and 3) the demo wallet re-seeded to ₹500,000, roughly ten times what all the review rounds together had spent.

## Final approval

I submitted the final build on August 4 and it was approved by August 5. One final test I ran live was to buy a rupee pack with real money on the production App Store and watch it verify against Apple's production servers, credit exactly once, and show up in analytics with the exact amount. Same thing I did on Android two days earlier. Both platforms are now verified with actual dollars.

## Conclusion

Here's what I'd tell you before your first App Review

1. **The reviewer's screenshot is the bug report.** Every round cracked open when I reproduced what the screenshot showed instead of arguing with the citation text.
2. **Reviews fail on state left by previous reviews.** Reset your demo account between rounds: balance, owned items, everything. A wallet drained three rounds ago can reject you today.
3. **Your review notes are product surface.** One rejection was fixed almost entirely by rewriting prose. The notes had taught the reviewer the wrong mental model of the app.
4. **App Store Connect state is invisible app behavior.** Rejected IAPs silently stop vending, even in sandbox. Check product state before debugging code.
5. **Masked fields and shared credentials don't mix** when the system keyboard doesn't know your text field holds a password.
6. **Verify fixes the way the reviewer will encounter them.** Same device class, same region, same account state.

## 🛺 Go play!

Chal Rickshaw is now live on both stores — the [App Store](https://apps.apple.com/us/app/chal-rickshaw/id6775334841) and [Google Play](https://play.google.com/store/apps/details?id=studio.maximumimpact.chalrickshaw).

Chal, chal, chal! 🛺💨
