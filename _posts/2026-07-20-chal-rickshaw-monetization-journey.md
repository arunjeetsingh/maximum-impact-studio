---
layout: post
title: "Chal Rickshaw: Monetization Journey — Ads, IAPs, and More"
date: 2026-07-20
author: Arun Singh
description: "How Chal Rickshaw went from a game where rupees just piled up to a full economy — a bazaar to spend in, boosts to buy, reward ads to earn faster, real-money rupee packs, and an optional wallet backend that follows you across devices."
image: /assets/img/posts/chal-rickshaw-monetization-cover.png
excerpt: "For the past 2 weeks I've been juggling monetization for Chal Rickshaw and job interviews. A clever idea from a 7-year-old beta tester gave birth to a bazaar where players spend the rupees they earn — which snowballed into reward ads, real-money rupee packs, interstitials, and an optional wallet backend."
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/chal-rickshaw-monetization-cover.png' | relative_url }}" alt="Chal Rickshaw's turbaned Sikh driver waving from his green-and-yellow auto-rickshaw as golden rupee coins and banknotes swirl around him in a colorful Delhi bazaar, with a VIP red beacon and a speed-boost trail" loading="lazy">
</figure>

TLDR: For the past 2 weeks, I've been juggling work to monetize Chal Rickshaw and job interviews. A clever idea from one of my young beta testers gave birth to a new marketplace where players can spend the in-game rupees they earn to buy boosts they can use during gameplay. When I realized it might take a while to earn enough money to buy those boosts, that gave me the idea to use reward ads (double your fare by watching an ad) to help players earn faster. Eventually this turned into rupee packs players can buy with real money, interstitial ads, and another booster idea. Along the way, I also ended up building a basic player account backend (fully optional!) to store wallet data so users can keep their earnings across phones and installs. Read on to understand the exciting monetization journey of Chal Rickshaw!

- 🍎 **iOS public beta (TestFlight):** [https://testflight.apple.com/join/Zgn6udB4](https://testflight.apple.com/join/Zgn6udB4)
- 🤖 **Android public beta:** [Google Play opt-in](https://play.google.com/apps/testing/studio.maximumimpact.chalrickshaw)

## 🛒 A place to spend rupees

Players earn rupees or kamai (₹) as they drive their rickshaw in the game. Dodging the holy cow earns a ₹50 bonus, and avoiding a thulla shakedown helps hold on to the money. But until recently there was nothing to do with the rupees except watch them pile up. So the first real step toward monetization was a marketplace (bazaar) where players can actually spend the rupees they've earned in game. Suddenly every good run had a point beyond the high score. As a player, you were saving up for something.

That one change reframed the entire economy. Now there was a demand side. And a demand for rupees is exactly the thing that makes everything downstream — including reward ads and rupee packs — make sense to a player.

## 🧒 An idea from a 7-year-old

My favorite part of this story is that I didn't come up with what to put in the bazaar. One of my young beta testers, mid-game, dodging traffic in the usual chaos, said what he actually wanted was to just fly over the traffic. That was too good to ignore. So, it became the **Speed Boost**, a purchasable item that literally makes the rickshaw take off, launching over the jam for a few glorious seconds. It's exactly the kind of idea a kid would have and it turned out to be one of the most fun things in the game. A second boost, the **Lal Batti** (the VIP red-beacon that makes everyone get out of your way) later joined it in the bazaar.

The boosts are what gave the marketplace teeth. Now rupees could buy something players might genuinely want mid-run. That created the first honest tension in the economy — boosts cost rupees and earning enough of them takes a while.

## 📺 Reward ads: earn faster

That tension is what pointed me toward ads. Rather than an interruption, it is a way to double earnings. The centerpiece is a rewarded ad. On the win screen, a player can choose to watch a short video to double the fare they earned for that run. It's opt-in, it's honest, and it directly attacks the "grind is too slow" problem the boosts created. Watch a 15–30 second ad, walk away with twice the earnings, go buy your Speed Boost sooner. This is the retention-friendly kind of ad where the player is in control, they feel like they earned the reward, and skipping it never blocks normal play. It became the backbone of the free-to-play model.

## 🎯 Choosing an ad provider: why AdMob

Once I knew I wanted ads, I had to pick who serves them. I looked at the landscape and boiled it down to AppLovin MAX, ironSource/Unity LevelPlay, and AdMob. The deciding factor was a straightforward, well-documented integration story across both iOS and Android. Google AdMob won that on integration clarity which included a maintained Godot 4.x plugin covering interstitial, rewarded, and banner formats, plus the consent and iOS App Tracking Transparency plumbing I'd need anyway. I built the game's ad layer behind a single clean interface, then swapped AdMob in behind it. During development everything runs on Google's public test ad units so I never risk serving or clicking live ads on my own devices.

## ⚖️ Two ad formats, chosen carefully

I deliberately kept the ad footprint small. Two formats, each with a job:

- **Rewarded ad: Double your fare** — The opt-in win-screen ad above. Highest value, fully player-controlled, and it feeds the economy loop directly.
- **Interstitial ad: every 3 rounds** — A full-screen ad at the natural break between rounds. The one place ad policies actually sanction interstitials is a logical break between stages, and a round transition is exactly that. Critically, I cap the frequency to one interstitial every three round-ends, never back-to-back, never on app launch or exit, with a grace period for brand-new players. This will hopefully address ad fatigue. A run where the player crashes often could easily throw an ad in their face every 30 seconds, and that's how you get uninstalled. Gating by rounds (not minutes, and not every round) keeps the game feeling like a game.

## 💳 In-App Purchases: Rupee packs and Remove Ads

Reward ads help players earn faster, but I figured some players might want to just pay to try out the boosts. So the next layer was in-app purchases which fit right into the bazaar the player already knows.

**Rupee packs.** A ladder of consumable packs — playfully themed from Chillar (loose change) up through Mutthi (Handful), Thaila (Bag), Tijori (Safe), to Khazana (a treasure). Bigger packs give bigger bonus rupees.

**India-specific pricing.** India is a key audience for this game, so the packs are anchored to real India-appropriate price points (₹99 through ₹1,999) rather than whatever an exchange rate spits out. Every other region localizes automatically from the store's own localized price.

**Remove Ads.** For players who just want an experience without the every-3-round interstitial, a one-time Remove Ads purchase turns them off. Reward ads stay available (they're a benefit players opt in to double their fare), but the forced round-transition ads go away.

## 🗄️ The payments backend and account system

This is where it got properly interesting under the hood. If players can buy rupees and spend them, then their wallet is a real balance that has to survive across app restarts, reinstalls, and phone switches. If that balance lived only on the device, a reinstall would wipe money someone actually paid for. That's unacceptable. But a spendable balance can't be protected by the app stores' "restore purchases" alone. Restoring lifetime purchases would just re-grant rupees the player already spent. The wallet has to have a source of truth that sees both the credits and the debits. So I stood up a small backend to own the wallet, with the device acting as an offline cache.

The choices behind it:

- **Supabase** for the backend and the lightweight accounts system. It gave me a managed Postgres database, authentication, and serverless edge functions in one place. The wallet is stored as an append-only ledger of changes rather than one editable number, which makes offline play trivial to reconcile. Queued events replay and apply exactly once when you reconnect, with no guesswork. And real-money rupees are only ever credited after the server validates the store receipt.
- **Resend** for transactional email. This enables account verification, keeping player identity and wallet linked to them. Resend is a clean, developer-friendly way to send emails reliably without dragging in a heavyweight marketing platform.

The account is fully optional and the gameplay is 100% offline-playable. Players never have to sign in to drive, dodge cows, or earn kamai. The account exists so player wallets and purchases follow them across installs and devices.

## 🔒 What this meant for privacy

Adding ads, analytics, and an optional backend changed the game's data story. The privacy policy got a full rewrite to describe exactly what's collected and why. The game now uses two third-party SDKs: GameAnalytics for anonymous gameplay analytics and Google AdMob for advertising. Supabase hosts the optional wallet backend, and Apple/Google process all payments. The commitments I'm holding to, and stating plainly in the policy:

- **The account is optional.** Players can play everything without one.
- **Analytics is anonymous and consent-gated.** Events tie to a random install ID and where a consent form is required (EEA/UK), nothing runs until the player answers it. There is no cross-app tracking so the game never shows Apple's ATT prompt.
- **Payments stay with Apple/Google.** We never see the player's card or bank details. Only the store's transaction ID which we verify server-to-server before crediting rupees.
- **We do not sell player personal data,** use it for cross-app tracking, or have any plans to monetize the data. The business model is based on ads and purchases, not on player data.
