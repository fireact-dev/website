+++
title = "Built With Fireact"
date = 2026-09-09T08:00:00+10:00
draft = false
tags = ["fireact", "showcase", "saas", "case-study"]
categories = ["announcements"]
banner = "img/blog/built-with-fireact.png"
authors = ["Chaoming Li"]
+++

{{< figure src="/img/blog/built-with-fireact.png" alt="Three different SaaS products built on one shared foundation" >}}

## Three products, one foundation

It's one thing to list what Fireact ships — auth, subscriptions, team invites, billing. It's another to see it holding up real products in production. Here are three SaaS businesses currently built on Fireact, each solving a completely different problem for a completely different buyer.

### EquiRound — equity management for startups

EquiRound handles cap-table tracking, ESOP and equity grant management, and funding round dilution simulation for startups and SMBs. It's the kind of product where the underlying data — ownership percentages, vesting schedules, round terms — has to be right, because founders and investors are making real decisions off it.

Fireact's role here is exactly the boring-but-critical layer: who's logged in, who's on the team, who's allowed to see which cap table, and how the subscription is billed. None of that is EquiRound's differentiation — the dilution modeling is — so none of it needed to be built from scratch.

### Omniroo — Stripe to HubSpot, reliably

Omniroo receives Stripe webhook events from connected accounts, transforms them, and syncs them to HubSpot through rate-limit-aware queues with event filtering. It's an integration platform, which means its hard problems are all about correctness and throughput on the data pipeline — not about reinventing account management for the umpteenth time.

Team invites and role-based permissions matter here too: Omniroo customers are often small ops teams who need to share access to their integration configuration without everyone having full admin rights.

### InAppAI — AI agents that operate your UI

InAppAI lets consumer apps install an open-source React component, register tools, and let end users execute in-app actions through natural language — agents that drive your interface instead of just chatting about it. It's the newest and most experimental of the three, which is exactly when you don't want to be debugging your own auth system.

## The pattern

None of these three products compete with each other, and none of them would describe their core value as "we have really solid team invites." That's the point. Fireact exists so that the parts of a SaaS app that are genuinely undifferentiated — sign-up, billing, permissions — don't have to be rebuilt by every founder who has a real idea for the other 90% of the product.

If you're building something in that other 90% and want to skip the first few weeks, `npx create-fireact-app` is the fastest way to see if the shape fits.
