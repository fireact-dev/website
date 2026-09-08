+++
title = "Why Firebase, Not Supabase?"
date = 2026-09-09T09:00:00+10:00
draft = false
tags = ["fireact", "firebase", "supabase", "saas", "architecture"]
categories = ["engineering"]
banner = "img/blog/why-firebase-not-supabase.png"
authors = ["Chaoming Li"]
+++

{{< figure src="/img/blog/why-firebase-not-supabase.png" alt="A fast managed backend versus a self-hosted database stack" >}}

## The default answer today is Postgres

If you've looked at a SaaS boilerplate comparison list recently, you've noticed the pattern: almost all of them are built on Next.js + Supabase, with Postgres as the database of record. That's a reasonable default. Postgres is powerful, portable, and Supabase wraps it in a genuinely good developer experience.

Fireact goes the other way: Firebase Auth, Firestore, and Cloud Functions. That's not an oversight or a stack we haven't gotten around to modernizing. It's a deliberate bet, and it's worth explaining why, because we haven't made this case explicitly before.

## What you're actually optimizing for

Every backend choice is a trade of one kind of effort for another. The question isn't "which is more powerful" (Postgres wins that one outright). It's "which effort do you want to take on right now, as a founder trying to ship."

**Firebase's bet: minimize operational surface area.**

- No database server to provision, patch, or scale. Firestore scales automatically; you don't reason about connection pools or query planners.
- Auth is a solved problem on day one: email/password, OAuth providers, session management, none of it hand-rolled.
- Cloud Functions deploy as part of the same project, no separate hosting account, no Docker image, no cold-start tuning beyond what Firebase already does for you.
- The free tier is genuinely usable for a pre-revenue product, not a 14-day trial.

**Supabase's bet: maximize what you can do with the data once it's there.**

- Real SQL: joins, transactions, constraints, the query patterns every backend engineer already knows.
- Row-level security policies that live in the database itself, not scattered across application code.
- An ecosystem of ORMs, migration tools, and analytics that assume a relational schema.

Neither of these is "the modern choice." They're different answers to "where do you want to spend your first three months."

## Where the Firebase bet pays off

Fireact is aimed squarely at solo founders and small teams shipping a first SaaS product, not teams that already have a backend engineer and a data model they need to enforce at the database layer. For that audience:

- **Time-to-first-customer matters more than query flexibility you don't need yet.** Firestore's document model is a worse fit for complex relational reporting, and a better fit for "I need auth, teams, and billing working this week."
- **You're not managing infrastructure you don't have a team for.** No connection pool to tune under load, no database migrations to write and rehearse, no separate hosting bill to reconcile against usage.
- **Stripe billing, team invites, and role-based access are already wired to this model.** Building the SaaS foundation Fireact ships isn't a rewrite away from Firestore's document shape; it's the natural shape for it.

## The scaling story people don't mention

Postgres itself scales fine. That's not in question. What's less talked about is that a self-hosted-feeling Postgres backend hands you its scaling problems earlier than a managed document store does, and at a point where you're least equipped to deal with them.

The pattern shows up repeatedly once a project crosses a few thousand users: default connection limits get hit (Supabase's own docs point people to Supavisor, its connection pooler, for exactly this reason), and row-level security policies that were instant at a hundred thousand rows start timing out at a million, because a policy that calls a function per row scales with both row count and policy complexity. One developer's public account of this describes a project that started seeing 8+ second queries, connection-limit errors, and dropped realtime subscriptions once it crossed roughly 5,000 users. Not because Postgres is bad at its job, but because the failure mode of a relational, connection-oriented database under load looks like *your* problem to fix: tune the pooler, rewrite the RLS policy, add an index, watch the connection count. Firestore's document model and Cloud Functions' auto-scaling don't remove all scaling concerns, but they remove this specific category of "we're now doing part-time database administration" surprise.

**Here's why that timing matters.** Say you're at 10,000 users on a freemium SaaS, a very reasonable place for connection-pool problems to start showing up. A generous free-to-paid conversion rate for early-stage SaaS is a couple of percent. Call it 2%: 200 paying users at $10/month is $2,000 in monthly revenue. That doesn't cover a part-time contractor to firefight database performance, let alone justify hiring the backend engineer who'd actually fix it properly. This is exactly the stage where "fully managed and auto-scaling by default" stops being a nice-to-have and becomes the difference between shipping features and doing unplanned ops work you can't yet afford to pay someone else to do.

## Vendor lock-in is a deferred cost, not a permanent one

The other objection to Firebase is vendor lock-in, and it's a real cost, but it's usually presented as more final than it is. Fireact's Stripe integration talks to Stripe directly, not through a Firebase-specific billing layer, so your subscription and customer data was never locked in to begin with. Firebase Auth supports bulk user export. Firestore data can be exported and reshaped into a relational schema once you actually know what your query patterns look like in production, which, honestly, you often don't yet at the point you're choosing your first backend.

The realistic sequence for a startup that succeeds is: ship fast on Firebase while $10/month users are still a rounding error, hit real product-market fit, then replatform the parts that need it once you have both the revenue and the team to do that migration properly. Paying a lock-in cost *later*, once you can afford to pay it, is a better trade than paying an operations cost *now*, while you can't.

## Where you should pick something else

We'd rather say this plainly than pretend Firebase is the right call for everyone. If you already know your data is deeply relational (multi-table reporting, complex joins across tenant data, analytics that benefit from SQL), Postgres-based stacks like Supabase are the better foundation, and you should start there instead of fighting Firestore's document model later.

Fireact is a bet on speed for the specific shape of problem most SaaS founders start with: auth, subscriptions, teams, billing. If that's your shape, Firebase's reduced operational surface is the advantage. If it's not, we'd rather you know that before you commit a codebase to it.
