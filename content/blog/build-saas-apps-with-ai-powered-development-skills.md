+++
title = "Build Your SaaS App with AI-Powered Development Skills"
date = 2026-03-19T10:00:00+10:00
draft = false
tags = ["fireact", "claude-code", "ai", "saas", "developer-experience"]
categories = ["announcements"]
banner = "img/blog/claude-code-skill.png"
authors = ["Chaoming Li"]
+++

{{< figure src="/img/blog/claude-code-skill.png" alt="Build SaaS apps with AI-powered Claude Code skills" >}}

## SaaS Development Skills That Turn Claude Code into a Fireact Expert

Claude Code is powerful on its own, but it doesn't know your framework's conventions. It doesn't know that Fireact subscription routes need `ProtectedSubscriptionRoute`, that every string must go through `useTranslation()`, or that `app.config.json` must be updated when you add a page.

That's what the built-in skills change. Every Fireact project scaffolded with `create-fireact-app` now ships with **SaaS development skills** — structured playbooks that teach Claude Code exactly how Fireact works. The result: Claude doesn't just write code, it writes production-ready Fireact code that follows every convention, updates every config file, and handles every edge case. You get a SaaS app built the right way, fast.

### The Problem with Post-Installation Customization

Fireact gives you a production-ready SaaS app out of the box: authentication, subscriptions, Stripe billing, team permissions, i18n. But the real work starts after scaffolding — when you need to add your own pages, components, backend logic, and navigation. That's where things get tedious.

Adding a single subscription page in Fireact involves touching 4-5 files:

1. Create the component with the right hooks (`useSubscription`, `useConfig`, `useTranslation`)
2. Add the route path to `app.config.json`
3. Add the route to `App.tsx` wrapped in `ProtectedSubscriptionRoute`
4. Add the import to `App.tsx`
5. Add translation keys to your i18n files

Miss any step and things break silently — a page that doesn't appear in the router, a component that crashes because it's missing a loading state, or hardcoded strings that break for non-English users.

This isn't a Fireact-specific problem. It's a framework problem. Frameworks impose conventions that make apps consistent and maintainable, but those conventions create boilerplate. Developers spend more time on wiring than on building.

### How the Skill Changes This

With the built-in Claude Code skill, you tell Claude what you want and it handles all the wiring:

```
> add a reports page to my subscription
```

Claude reads your project state, then:
- Creates `src/components/Reports.tsx` with `useSubscription`, loading/error handling, and TailwindCSS
- Adds `"reports": "/subscription/:id/reports"` to `app.config.json`
- Adds the route inside `SubscriptionProvider` in `App.tsx` with `ProtectedSubscriptionRoute`
- Adds the import
- Adds i18n keys to `en.ts`

All in one shot. No missed steps.

### 8 Playbooks, Not Just a Chatbot

The skill isn't a generic AI assistant. It ships with 8 structured playbooks that encode Fireact's conventions:

| What you want | What to say |
|---|---|
| Add a subscription-scoped page | "Add a reports page" |
| Add an authenticated page | "Add an API keys page" |
| Add a public page | "Add a landing page" |
| Customize an existing component | "Customize the sign-in page" |
| Modify navigation | "Add reports to the sidebar" |
| Change branding | "Change the nav bar to blue" |
| Add backend logic | "Add a cloud function that sends welcome emails" |
| Store custom data | "Add a notes collection to subscriptions" |

Each playbook knows exactly which files to create, which files to modify, and which conventions to follow (i18n, TailwindCSS, permission checks, loading states, route config).

### Why This Matters for Open-Source Frameworks

Open-source frameworks have a documentation problem. No matter how comprehensive your docs are, developers still have to:

1. Find the right doc page
2. Understand the pattern
3. Adapt it to their specific use case
4. Remember to update all the related files

The Claude Code skill collapses all of that into a conversation. It reads the docs (shipped as reference files in `.claude/skills/fireact-builder/references/`), reads your code, and applies the right pattern.

This is the future of framework DX: **conventions encoded as AI skills, shipped with the framework itself.** The framework teaches the AI how to use it, and the AI teaches the developer by doing.

### What It Doesn't Do

The skill is specifically for post-installation customization. It won't:

- Scaffold a new project (that's what `create-fireact-app` does)
- Modify the `@fireact.dev/app` or `@fireact.dev/functions` packages
- Deploy your app
- Make architectural decisions for you

It's a power tool for the "build on top of the framework" phase — the part where you turn a generic SaaS template into your specific product.

### How It Ships

The skill lives in your project's `.claude/skills/fireact-builder/` directory. When you run `create-fireact-app`, it's copied into your project automatically — no separate installation, no configuration.

```
your-project/
├── .claude/
│   └── skills/
│       └── fireact-builder/
│           ├── SKILL.md              # Main skill with 8 playbooks
│           └── references/
│               ├── hooks-and-contexts-api.md
│               ├── routing-patterns.md
│               ├── component-patterns.md
│               ├── navigation-customization.md
│               └── cloud-functions-patterns.md
├── src/
├── functions/
└── ...
```

The reference files contain the complete API documentation for Fireact's hooks, types, routing patterns, component templates, and Cloud Functions patterns. Claude reads these when it needs to understand how the framework works.

### Try It

1. Install or update `create-fireact-app`:
   ```bash
   npm install -g create-fireact-app
   ```

2. Create a new project:
   ```bash
   create-fireact-app my-saas-app
   ```

3. Open in Claude Code and start building:
   ```
   > add a dashboard with analytics charts
   > add a settings page for notification preferences
   > add a cloud function that generates PDF invoices
   ```

For the full documentation, see the [Claude Code Integration guide](https://docs.fireact.dev/custom-development/claude-code/).

We believe this is the beginning of a new pattern: open-source frameworks that ship with AI skills to make customization effortless. We're excited to see what you build with it.
