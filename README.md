# Nousix-Base

**The Supabase API your app already uses, correctness-first, light enough to self-host anywhere.**

A Supabase-compatible backend platform, being built autonomously by an AI engineering team, in public.

Nousix-Base is a clean-room, Rust implementation of a backend platform that the official Supabase client SDK can
talk to as if it were Supabase: Postgres, an auto-generated REST API, authentication, realtime, storage, edge
functions and a dashboard. It is not affiliated with Supabase; "Supabase-compatible" describes the API it targets.

The point is not only the product. Everything in this repository is planned, written, reviewed, merged and deployed
by **N-SWE**, an autonomous software-engineering team running on the Nousix runtime:

- the **project board** is the backlog: tickets are filed, prioritized and scheduled by the team's overseer;
- every ticket is implemented on a branch and lands as a **pull request**;
- every pull request gets an **independent code review** (security, design, quality, tests) before it merges;
- the platform is **deployed to Kubernetes (k3s) with ArgoCD**, and CI runs on GitHub Actions.

## The prompt

The whole project starts from one task prompt: [TASK.md](TASK.md). Everything after it (the plan, the
tickets, the code, the reviews and the deployments) is the team's own work.

## Why Nousix-Base

- **Drop-in:** existing supabase-js apps work unchanged, and the public conformance scoreboard proves it.
- **Correctness first:** behavior is defined by the conformance suite and tests; security policies (row-level
  security, auth, storage) are tested as first-class behavior; types are generated end to end from your schema.
- **Light enough to self-host anywhere:** the services Nousix-Base builds aim to ship as one Rust binary (or as few as
  truly needed) beside Postgres, instead of a dozen containers, small enough for a modest VPS, one Helm install on a
  cluster. The footprint will be measured and published.
- **AI-first:** schema-to-type generation, docs written as precise contracts (with `llms.txt`), an MCP server, and a
  dashboard built on [Ferric](https://github.com/Nousix-LLC/ferric).

## How progress is measured

The yardstick is external and objective: the **official Supabase client SDK and its tests**, run against a
Nousix-Base deployment. Each phase reports the pass rate for the SDK modules it covers.

| Phase | Scope |
|---|---|
| P1 — core | Postgres, auto-generated REST API, auth (email/password, JWT, sessions, OAuth), API gateway, Helm + CI/CD |
| P2 — live data & files | Realtime (database changes over websockets), Storage (buckets, objects, access policies) |
| P3 — compute & UI | Edge functions (WASM), web dashboard |

## Watching, not contributing

This repository is **watch-only**. Issues and pull requests are limited to the project's own team; outside issues,
pull requests and comments are disabled. Feedback is welcome by email (mattsmcknight@gmail.com) or on the
announcement thread.

## Human interventions

Every time a human touches the project, it is recorded in [INTERVENTIONS.md](INTERVENTIONS.md).

## License

Licensed under either of [Apache License, Version 2.0](LICENSE-APACHE) or [MIT license](LICENSE-MIT), at your option.
