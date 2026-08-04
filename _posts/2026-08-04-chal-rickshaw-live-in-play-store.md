---
layout: post
title: "Chal Rickshaw is Live in the Google Play Store"
date: 2026-08-04
author: Arun Singh
description: "Chal Rickshaw is now live on Android in the Google Play Store. The gory details of the launch last mile — review demo accounts, trailer and screenshots, privacy declarations, and getting ads and IAPs right in every region."
image: /assets/img/posts/chal-rickshaw-launch-cover.png
excerpt: "Chal Rickshaw is now live in the Google Play Store (Android)! iOS is still in Apple's App Review queue. After adding ads and in-app purchases, there was still a mountain of launch work — a trailer, screenshots, logos, a store description, a reviewer demo account, privacy declarations, and making ads and IAPs behave under every region's laws. Read on for the gory details."
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/chal-rickshaw-launch-cover.png' | relative_url }}" alt="Chal Rickshaw's turbaned Sikh rickshaw driver celebrating in front of a Google Play badge, with his green-and-yellow auto-rickshaw and a colorful Delhi street behind him" loading="lazy">
</figure>

TLDR: Chal Rickshaw is now live in the Google Play Store (Android)! I'm still waiting on Apple's App Review team for the iOS App Store, but I'm hopeful that lands any day now. After adding ads and in-app purchase support, there was still a mountain of launch work including cutting a trailer video, capturing and labeling compelling screenshots, fixing up the logos, and writing a store description that sells the game in a few lines. For the reviewers, I set up a demo account so they could test account-only features like preserving purchases and carrying a wallet across devices. Then a big chunk of work went into the privacy policy and other legal requirements to spell out that we don't sell or share user data with third parties. Finally, I had to make sure ads and in-app purchases behave correctly under the laws of every region we ship to. It's been a fun (and occasionally maddening) journey. Read on for the gory details.

## Get the game

- 🤖 **Android — live now:** [Chal Rickshaw! on Google Play](https://play.google.com/store/apps/details?id=studio.maximumimpact.chalrickshaw)
- 🍎 **iOS — in App Review** (TestFlight beta still open): [https://testflight.apple.com/join/Zgn6udB4](https://testflight.apple.com/join/Zgn6udB4)
- ▶️ **Watch the preview:** [https://youtu.be/UT7DxPjG2TU](https://youtu.be/UT7DxPjG2TU)
- 📝 **The monetization backstory:** [Ads, IAPs, and a wallet backend]({{ '/2026/07/chal-rickshaw-monetization-journey/' | relative_url }})

## 🚀 Code complete to in-store

The surprising thing about launching a real store app is how little of the last mile is actual game code. By the time the ads and in-app purchases worked (that's the [previous post]({{ '/2026/07/chal-rickshaw-monetization-journey/' | relative_url }})), the game itself was done. And then a second, entirely different project began with store listings, creating review demo accounts, researching privacy law, making compelling screenshots, filling ratings questionnaires, and dealing with a reviewer on the other side of the world trying to break your app on a device you don't own. Here's everything that stood between "it runs on my phone" and "it's in the store."

## 🔑 A demo account so reviewers can test the paid stuff

Both Apple and Google make you prove your app works and that includes the parts hidden behind a sign-in. Chal Rickshaw is 100% playable with no account (you never sign in to drive, dodge cows, or earn rupees). But creating an account and signing in ensures players can carry their wallet across devices so a reinstall or a new phone doesn't wipe rupees the player actually paid for. A reviewer can't test any of that without credentials. So I built a dedicated review demo account:

- A fixed login with a fixed access code so no email round-trip is required. A reviewer shouldn't have to wait for a one-time code to test.
- The account is pre-funded with in-game currency so the reviewer can immediately walk into the Bazaar (marketplace) and exercise the spend/restore flows.
- Clear step-by-step instructions in the store console to open the Bazaar from the win screen, tap the account prompt, and sign in with the demo email + code.

That detail about no email delivery needed turned out to be the single most important sentence in the whole submission. More on that in the App Review saga below, because the demo account is exactly where things went sideways with Apple.

## 🎬 Trailer, screenshots, logos, and a description

A store listing is a shop window, and the game deserved a good one.

- **Preview video.** A short store trailer cut to show the actual moment-to-moment game like the boost flight over the traffic jam, the thulla shakedown, the VIP lal batti, and the night round. Google Play wants it on YouTube and links it at the top of the listing. The preview is the first thing a browsing player sees.
- **Screenshots.** Six phone screenshots pulled from the current build and ordered deliberately: Boost Flight → Thulla → Lal Batti → Night Round → Doggy Chase → Bazaar. Each one is a different hook showing the fun physics, desi humor, festive art, and the economy. Keeping them at 9:16 and ≥1080px also keeps the app eligible for Google's promotional surfaces, which is free reach.
- **Logos and icons.** Cleaned-up app icon (512×512) and a feature graphic (1024×500) so the game looks finished at every size Play renders it.
- **Description.** I wanted a short line to grab players: "Jump red lights, dodge holy cows & bribe cops in a desi rickshaw runner!" The full description that leans into the personality: jump the red lights for kamai, respect Gaumata, bribe the thulla, survive the babu convoy, and spend your rupees in the Bazaar. Plain text with some emoji thrown in.

## 🔒 Privacy and data policies

Adding ads, anonymous analytics, and an optional wallet backend changed the game's data story, so both the published store declarations and the policy hosted on maximumimpact.studio got a full pass to make sure a player can understand exactly what is and isn't collected. The commitments, stated plainly and consistently across the privacy policy, the Play Data Safety form, and the site:

- **The account is optional.** Everything works with no email linked. The account just helps carry the user's purchases across reinstalls and phone changes if they want it.
- **We do not sell player personal data,** we don't use it for cross-app tracking, and we don't share it for cross-context behavioral advertising (as California law defines those terms). The business model is ads and purchases, not player data.
- **Payments stay with Apple and Google.** We never see player card or bank details, only the store's transaction ID, which we verify server-to-server before crediting rupees.
- **Analytics is anonymous and consent-gated.** Gameplay events tie to a random install ID, never player identity or advertising ID, and where a consent form is required nothing runs until the player has answered it.
- **Built-in account deletion.** Google Play's Data Safety section requires a way to request deletion so we built it right into the app.

The full [privacy policy]({{ '/chal-rickshaw/privacy/' | relative_url }}) is the source of truth and the store forms are answered to match it line for line. A mismatch between Data Safety answers and the actual policy could lead to rejection.

## ⚖️ Ads and IAPs

Showing ads and taking payments is simple until you remember every region has its own rules. A few of the things that had to be right before this could ship:

- **A real consent flow for the EEA/UK.** Where GDPR requires it, the game shows a consent form and loads neither ads nor analytics until the player has answered.
- **India-appropriate pricing.** India is a core audience, so the rupee packs are anchored to real India price points (₹99 through ₹1,999) instead of whatever an exchange rate spits out. Every other region localizes automatically from the store's own prices.
- **Honest virtual currency.** Kamai (earnings) is clearly labeled as an in-game currency with no real-world value. Refunds correctly reverse the corresponding in-game credit.
- **Truthful store declarations.** The Ads declaration flipped to "Yes," the content rating questionnaire was redone now that ads and purchases exist, and app-ads.txt authorizes our AdMob inventory so the ad ecosystem trusts the requests.
- **Frequency caps that respect the player.** We show one interstitial ad at most every three round-ends, never back-to-back, never on launch or exit, with a grace period for brand-new players.

## 🛺 It's live! Go play :)

Android is out of beta and live in the Google Play Store right now. iOS is in Apple's queue and, fingers crossed, close behind. If you play it, tell me what breaks, what makes you laugh, and how far you get before the SIYAPPA.

Chal, chal, chal! 🛺💨
