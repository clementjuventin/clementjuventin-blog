---
slug: request-throttler
title: Tackling Rate Limiting, One of My First Challenges at Cede Labs
authors: clementjuventin
tags: []
---

Cede Labs’ SDK is designed to support any number of centralized exchange accounts, allowing applications or users to query data from several CEXs seamlessly. However, this flexibility exposed a serious issue: **API rate limits**.

<!-- truncate -->

Over the past few weeks at **Cede Labs**, I’ve been diving deep into the codebase, getting familiar with the SDK, and preparing myself for the complex technical challenges ahead. One of the first major problems I encountered — and helped solve — was **rate limiting across multiple centralized exchanges (CEXs)**.

## The Problem: Managing Rate Limits in a Multi-CEX World

Cede Labs’ SDK is designed to support any number of centralized exchange accounts, allowing applications or users to query data from several CEXs seamlessly. However, this flexibility exposed a serious issue: **API rate limits**.

The tricky part is that **rate limiting strategies vary widely between exchanges**. Some enforce limits by:

- IP address
- API key
- Master/sub-account hierarchies
- Or even combinations of all three

We observed this firsthand through our **Chrome extension**, which integrates the SDK to interact with multiple exchanges. With many CEX accounts active simultaneously, we were quickly exhausting rate limits, resulting in **slowness, API errors, and buggy behavior**.

Clearly, we needed a robust, centralized system to handle API throttling across all exchange instances.

## The Solution: Building a Shared Request Throttler

Our SDK uses **CCXT**, a widely adopted library that simplifies communication with CEX APIs. CCXT conveniently includes built-in metadata about rate limits — including cost calculations and endpoint-specific constraints — which became the foundation for our fix.

I built a component called the **Request Throttler** to manage all outgoing requests. This module:

- Runs a continuous loop, processing a queue of API requests
- Delays or batches calls based on calculated rate limits
- Shares throttling logic between all exchange instances tied to the same CEX
- Supports custom priorities to fast-track high-importance requests

## The Code: A Deep Dive into the Request Throttler

Below is a simplified version of the throttler's main loop. For readability, I’ve omitted queue-empty handling (i.e., when `this.getNext()` returns `null`).

```typescript
async loop () {
    let lastTimestamp = now ();
    while (this.running) {
        const { resolver, cost, rejecter, timestamp, expireInterval } = this.getNext ();

        if (this.tokens >= 0) {
            if (timestamp + expireInterval < now ()) {
                rejecter ('Request expired');
            } else {
                this.tokens -= cost;
                resolver ();
            }
            await Promise.resolve ();
        } else {
            await sleep (this.delay);
            const current = now ();
            const elapsed = current - lastTimestamp;
            lastTimestamp = current;
            const tokens = this.tokens + this.refillRate * elapsed;
            this.tokens = Math.min (tokens, this.capacity);
        }
    }
}
```

#### Key Concepts
- `this.tokens` represents the available request "budget". It's reduced by the `cost` of each request.
- If `this.tokens` is below 0, the system will pause (`sleep`) and **refill** tokens based on elapsed time and a configured **refill rate**.
- If tokens are available (i.e., `>= 0`), the request is executed **only if it hasn't expired** (based on `timestamp + expireInterval`).
- The line `await Promise.resolve();` is a **context switch** — it gives other asynchronous tasks a chance to execute, which helps avoid blocking the event loop in JavaScript.
- **Token refill logic** happens after the delay and simulates a "leaky bucket" or "token bucket" algorithm, a common strategy in rate-limiting systems.

This loop ensures that all API requests respect the configured rate limits by dynamically adjusting based on real-time usage and availability.

## Prioritization and Background Work

In practice, our SDK performs a lot of **background tasks**, such as:

- Balance fetching
- Historical trade retrieval
- Status checks

These operations are necessary but can be expensive in terms of API cost. So, I implemented a **priority system**: important requests (like user-triggered actions) are associated to high priority while background tasks are associated to low priority.

This ensures faster user experiences without breaking rate limits or starving essential tasks.

## The Hard Part: Rate Limit Discovery

The most difficult aspect wasn’t writing the throttler logic itself — it was understanding and standardizing **how each exchange handles rate limits**.

- Many rate-limiting rules are hidden, sometimes undocumented and in worse cases not aligned with the official documentation
- CCXT’s built-in cost definitions were often outdated
- Some CEXs change behavior depending on the account type or endpoint

I had to manually test, validate, and correct the API cost values for several exchanges, updating the SDK accordingly.

It also took careful work to **share a throttler** across multiple CEX instances (i.e., multiple ccxt objects) since each has unique constraints, such as caching behavior or read/write permission separation.

For context:
> A CEX instance in our system is a `ccxt` object tied to a specific API key and access rights. Each instance needs to respect its rate limits but also coordinate with other instances for the same exchange.

That’s where the Request Throttler comes in — it sits above the instances and coordinates them like air traffic control.

## Looking Ahead

This was a great first challenge at Cede Labs. It pushed me to go beyond just writing code — I had to understand CEXs inside out, map inconsistencies, and design a system that works under real-world constraints.

There’s still a lot to build and while this system highly reduced our rate limit issues, there is still a lot of improvements to make to reach our goals.

I’m proud of how this first technical hurdle turned into a solid foundation for more scalable and reliable SDK behavior.

More to come soon!