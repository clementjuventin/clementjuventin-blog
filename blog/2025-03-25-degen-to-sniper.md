---
slug: degen-to-sniper
title: From A Nooby Degen to a Bloodthirsty Sniper
authors: clementjuventin
tags: []
---

In this article I'd like to recount my first steps as a degen and what prompted me to turn to the world of sniping.

<!-- truncate -->

Up until recently, I had always kept an eye on the web3 and blockchain space from a distance. I followed the news, attended events and conferences, and got to know many of the key players who set the **foundations and rules of the ecosystem**.

But there was always this one group that felt almost mythical to me — the **degens**. A close friend of mine was part of what I used to label as the degen community. He belonged to private groups focused on **alpha leaks, technical collaboration, and all sorts of niche financial services**. For a long time, it all seemed a bit unproductive to me from a personal growth standpoint, so I kept my distance.

Over time, however, I began to realize that beyond the financial aspect, there was a real sense of connection — a network that could be nurtured and leveraged as an additional driver for success. To be honnest, I also had more time than ever for this kind of activity.

So I decided to dive in and start my journey as a degen. It turned out to be much more challenging than I had anticipated. I quickly discovered that this world demands an **almost full-time commitment**. It’s a game of **hunting, anticipating, and executing transactions** based on public data. Starting out on **Base** (Coinbase's blockchain), I quickly lost my first few hundred dollars. But I knew the potential was there. After a few weeks, I managed to break even and eventually started seeing small profits.

As the weeks passed, I identified some of my key strengths and weaknesses.

**Strengths**:

- I have a technical background. I can understand the intricate details of projects and deeply master the tools needed to operate effectively in this space.
- I have valuable contacts in the industry who’ve generously shared their knowledge and saved me months of trial and error.

**Weaknesses**:

- I work solo. Despite those contacts, I can’t rely on them indefinitely. In the degen world, data is money. I can’t expect them to constantly feed me information. Also, being a solo operator means I’m limited by time, whereas groups can distribute the workload and share information more efficiently.

That’s when I decided to team up with an old friend who was already well-established in the ecosystem. He was also a dev and had access to valuable information. I, on the other hand, had time and solid technical discipline to support him on various projects.

Together, we began developing a range of analytics and monitoring tools. Without going into too much detail, we built:
- AlphaGate monitoring
- Twitter activity monitors
- Factory contract monitors on Base
- Onchain OSINT for Twitter/Warpcaster addresses
- Onchain Data analytics tools

All these projects were just the beginning — a warm-up for something much bigger: diving into **crypto sniping**.

## What Is Sniping?

By sniping, I mean setting up a system to **quickly buy a cryptocurrency just before it catches a wave of attention**. Imagine a highly anticipated token is about to launch. The market hasn’t priced it in yet, and for a brief moment, it’s undervalued. A sniper aims to e**xploit that gap** — buying at launch before the market corrects.

This practice is often frowned upon — and understandably so. When you buy early with the sole intent to flip for a quick profit, you inevitably hurt retail investors who get in later and might immediately find themselves in the red. Indirectly, this also harms the project itself.

But in the end, the only rule that truly matters is the market. Sniping is a fierce game — full of traps, failures, and intense competition. Being consistently profitable is just as difficult as in any other degen strategy. I’ve had bots miss the entry entirely while friends manually secured massive gains.

In the coming post, I’ll break down some of the sniping techniques I’ve personally developed — along with a few wild anecdotes from the field.

![Pepe Sniper](/img/sad_pepe_sniper.png)

## Sniping Techniques

There are many different sniping techniques, and after watching countless token launches, I can confidently say that **no two are ever the same**. Sometimes projects announce the contract on Twitter, other times on Telegram, through a custom website interface, or via a launchpad. You always need to do some investigation to anticipate which sniping strategy has the highest chance of working.

The two sniping techniques that have brought me the most consistent profits are the following.

### Official Claim Sniper

What I call the *official claim sniper* is a bot that waits for a verified announcement from a trustworthy source before buying the token. Scammers often deploy fake versions of tokens, so without direct confirmation from the project team, jumping in right after deployment is **extremely risky**. Some snipers take that gamble anyway, assuming the potential profits outweigh the possible losses. Personally, I prefer the safer route, even if it means lower profit multipliers — **risk management comes first**.

This type of bot is relatively simple. It only requires a **notification module** and a **transaction module**. A typical architecture I’ve used involves multiple purchase microservices (one per chain), all exposed through a standard API, along with a separate notification microservice configured per opportunity to monitor data and trigger the buy. Hosting both services on the same machine is important to maximize speed.

For example, you could monitor tweets from a memecoin on Avalanche, extract any EVM address with a regex, and buy on whatever DEX has liquidity for that token.

In theory, it sounds simple. But in practice, **there are many potential points of failure** that can ruin the operation.

First, the competition is **brutal**. Everyone’s sniper bots are tuned to fire in the same seconds. The first few moments after a hyped token launch usually show a **huge price spike**. That's because the earliest snipers are buying aggressively, inflating the price before the market stabilizes. If your bot isn’t fast enough, you’ll end up buying at the top and instantly taking a loss as early buyers dump their tokens. You need to strike a balance: be early enough to profit, but not so aggressive that you pay too much in gas or slippage.

![KAIKU/WETH pair on Base](/img/dexscreener.com_KAIKU_WETH_2025-06-06_00-37-34.png)
> KAIKU/WETH pair on Base (at launch), one candle = 2s (ie block time). Source: [Dexscreener](https://dexscreener.com/base/0x4c9498a3f36709ee57b1b7c4b440d8481a1b9f79).

Second, the projects themselves have started fighting back. Many view sniping as toxic behavior and implement countermeasures:

- Obfuscating the contract address (e.g., inserting extra characters or posting it as an image)
- Sharing a link to the DEX liquidity pool instead of the contract directly
- Using redirect links to custom sites where the contract is revealed
- Deploying a fake token first, then warning the community about it
- Enabling extreme transaction taxes for the first few seconds to penalize snipers

All these traps carry real risks — from simply **missing the opportunity** to **losing your entire investment**. With solid preparation and strategies involving AI or semi-public information feeds, you can sometimes bypass these tricks faster than a human could.

A great example of how dangerous this can get is detailed in [this excellent post](https://tactical.deepwaterstudios.xyz/p/anti-sniper-tech-custom-dex), which explains how anti-bot tactics drained massive amounts from automated snipers. We nearly fell victim ourselves during that period. While I was casually skiing down a slope, waiting for a midday launch, my partner was glued to Twitter. He spotted a Discord message revealing the project team’s plan was to bait snipers with a fake contract. On reflex, he shut down our script. One minute later, a fake contract tweet dropped — and just like that, another sniper lost $75,000.

We were shaken. Even though we weren’t risking that much, it was enough to seriously hurt. Ironically, the competitor immediately re-entered the market with the same amount (what confidence!). In the end, both their bot and ours failed to enter the market, likely due to the a technical hiccup. But the experience was a sobering reminder: even with good tooling and timing, **the risk is never zero**.

### Block Zero Sniper

This technique relies on knowledge of the launchpad that will host the token launch, as well as additional details like the deployer address or token ticker. The idea is to deploy a smart contract that sends a flood of buy transactions every block, attempting to snipe within the same block the liquidity pool is created.

This is possible because many launchpads (like Virtuals) use a factory contract that updates a counter or state variable when a new pair is created. By monitoring that state at high frequency, the sniper can instantly detect a new pool and trigger a buy. Ideally, you filter targets using the ticker or deployer to avoid blindly buying into every new token.

This reminds me of an interesting anecdote from Binance Smart Chain. CZ, Binance’s founder, had announced he would reveal the name of his dog at 16:00 UTC — a seemingly harmless statement that degens took very seriously. Why? Because they knew people would rush to create tokens named after the dog, sparking a frenzy of speculation. One sniper had the brilliant idea to create a smart contract with $10,000, programmed to invest $1,000 into each of the first 10 tokens deployed after 16:00 UTC.

You’ve got to admire that level of confidence — blindly throwing tens of thousands into what could be worthless tokens. But in this case, it paid off. Tree of the ten tokens was related to CZ's post — BROCOLLI (the dog) — made that sniper a millionaire in under 3 seconds. WTF bro what if CZ wanted a beer at 16:00 UTC and posted at 16:01?

## Conclusion

As you've seen, sniping techniques vary wildly, and **the risks are as real as the rewards**. In practice, we only make profitable trades in about 1 out of every 5 attempts. **Most of the time, nothing happens**.

One of the biggest lessons I’ve learned is that technical sophistication alone doesn’t guarantee success. Often, creativity, originality, and unconventional thinking bring more value than raw code. Always keep an open mind and approach every opportunity like a new challenge — because in this game, that's exactly what it is.

