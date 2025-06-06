---
slug: opal-technical-overview 
title: Opal - Technical Overview
authors: clementjuventin
tags: []
---

In this article, I’ll dive into the technical underpinnings of the Opal protocol, a project I’ve contributed to extensively. If you haven’t yet read the previous article about Opal, I recommend starting [there](./2024-09-15-opal.md) to better understand the context of what follows.

<!-- truncate -->

Here, we’ll focus on two of the protocol’s most important smart contracts: the **Omnipool** and the **Reward Manager**.

The Omnipool is the core of the protocol—it’s a single-asset liquidity pool that can dynamically rebalance and reallocate funds across different strategies. It lies at the heart of Opal’s yield-generation mechanism and represents the foundation of the platform's financial logic.

The second contract, the Reward Manager, is one I developed almost entirely independently. Its role is to handle the distribution of rewards to users participating in the Omnipool. I find the mathematical model behind it particularly elegant and effective. While similar systems are likely common and well-studied in DeFi, in this article, we’ll take a closer look at Opal’s unique implementation.