## Execution Task — Permission Posture: execute

READ-WRITE. You are authorized to carry out the request below, including the external-state changes it entails, within
its scope and nothing beyond it:

- the GitHub repository `Nousix-LLC/nousix-base` (code, branches, pull requests, reviews, issues, labels, milestones,
  GitHub Actions workflows) and its GitHub Project, `Nousix-Base Delivery` (Nousix-LLC project #3);
- the project's k3s cluster (kubectl context `k3d-nswe`): the `nousix-base*` namespaces you create and this project's
  own ArgoCD Applications. Do not touch other projects' namespaces or applications on the cluster.

Apart from issues and pull requests on `Nousix-LLC/ferric` (see "Ferric, your upstream"), do not touch other
repositories. Never force-push to `main`, and never rewrite merged history. The repository is
public: never commit secrets, tokens or credentials, and do not change its visibility, its interaction limits, or its
branch protection.

## Taskflow & Output Location

Author your taskflow (thread iterations, every forked DAG's `_FORK.md`, and all lifecycle and synthesis records) under
`~/nousix-runtime/nousix-base/taskflow/`. The product's source of truth is the GitHub repository; the taskflow is the
record of how the work was run, and it stays out of the public repository.

## The Request

Build Nousix-Base: a Supabase-compatible backend platform, as a clean-room implementation in Rust, developed as a real
software project in public. Build, run and deploy it on k3s.

### Why people would choose it

Supabase is already open source and self-hostable, so "open source" is not a reason to switch. Nousix-Base has to earn
its users. Its identity, which every design decision should serve:

> **The Supabase API your app already uses, correctness-first, light enough to self-host anywhere.**

- **Drop-in.** Existing supabase-js applications work unchanged; the public conformance scoreboard is the proof, not a
  claim.
- **Correctness first.** Behavior is defined by the conformance suite and by tests, never by approximation: security
  policies (row-level security, auth flows, storage policies) are tested as first-class behavior, divergences from the
  reference API are bugs filed on the board, and types are generated end to end from the database schema for Rust and
  TypeScript clients.
- **Light enough to self-host anywhere.** Self-hosting Supabase today means running a dozen or more containers. The
  services Nousix-Base builds should ship as one Rust binary (or as few as the design truly needs) beside Postgres: small
  enough for a modest VPS, one Helm install on a cluster. Judge every integrate-or-choose decision against this: prefer
  embeddable and in-process options, and decide the REST layer (PostgREST alongside, or the same behavior in-process)
  against this goal, recording why.
- **AI-first.** The platform is easy for coding agents to use correctly: schema-to-type generation, documentation that
  is a precise contract (with an `llms.txt`), and an MCP server for the platform. The dashboard is built on Ferric.

What gets built and what gets integrated: recreate the services and extensions Supabase itself authors (for example
its auth server, realtime, storage API, postgres-meta, Studio, edge runtime, pg_graphql, its CLI, and its smaller
Postgres extensions such as pg_net and vault). Integrate the third-party projects Supabase depends on, or choose
alternatives that fit the design better: Postgres itself, PostgREST, the API gateway (Kong or another ingress), the
connection pooler (for example PgBouncer instead of recreating Supavisor), pgvector, pg_cron, pgmq, imgproxy, and the
logging and metrics stack. Record each integrate-or-choose decision where the public can read it.

The product, in phases:

| Phase | Scope |
|---|---|
| P1 — core | Postgres; the REST API over the database schema (PostgREST, integrated); authentication with the full auth-server scope: email/password, magic links, phone/SMS OTP, MFA, OAuth and SSO, anonymous sign-in, JWT issuance and verification, sessions/refresh; an API gateway in front of the services; Helm charts and a working build → deploy → test loop |
| P2 — live data & files | Realtime: Postgres changes, Broadcast and Presence over websockets; Storage: buckets and objects with access policies, resumable uploads, the S3-compatible protocol, and image transformations |
| P3 — compute & UI | Edge functions: the function server (routing, auth context, secrets, deploy and invoke) on a WASM runtime; the dashboard, built on Ferric (below), to manage projects, tables, SQL, auth users, storage and functions |
| P4 — platform services | GraphQL (pg_graphql), postgres-meta, connection pooling, database webhooks and async HTTP (pg_net), secrets (vault), vectors, cron and queues, and logs |
| P5 — tooling | CLI compatibility: local development, migrations and diffs, type generation; the management API; backups |

Rust for all the services you build. The only JavaScript in the system is the conformance harness, which uses the official Supabase
client SDK (supabase-js) exactly as a real customer would.

The yardstick is the official supabase-js SDK and its tests, run against a Nousix-Base deployment. Report the pass rate
for each SDK module (database/REST, auth, realtime, storage, functions) where the public can see it, and keep it current.
P4 and P5 have no SDK test suite; define and publish an equally objective yardstick for each (for example the CLI's own
behavior against a Nousix-Base project, GraphQL queries, standard Postgres clients through the pooler).

Don't try to do the whole build in one DAG. Use the repository's GitHub Project to plan the work: design work first (the
overall architecture against the identity above, then each service's design, written up and reviewed before it is
built), then implementation. Seed the backlog from the conformance suite's failing tests and the build-out each phase needs. Changes land
as pull requests, and you schedule DAGs to review those pull requests with the code-review methodologies. Deploy the
software on k3s, with GitHub Actions for CI and ArgoCD on k3s for delivery; the devops work is DAGs too. Report bugs as
issues and spawn DAGs to fix them.

Treat the GitHub Project and Issues like a real Jira environment: they are where you manage and schedule the work and the
code reviews.

## Ferric, your upstream

The dashboard is built on Ferric (`Nousix-LLC/ferric`), a Rust/WebAssembly web framework that another N-SWE team is
taking to 1.0 at the same time. Depend on it as a pinned upstream dependency, the way you would on any open-source
framework. When you hit a Ferric bug or need a feature it lacks, file an issue on `Nousix-LLC/ferric` with the
`from:nousix-base` label and a first line saying it comes from Nousix-Base; you may also open a pull request there with
a proposed fix. Never review or merge anything in the Ferric repository: its own team does that. Work around the gap,
or schedule around it, until upstream resolves it.

## Constraints

- Clean room: build from Supabase's public documentation, public API behavior and the official client SDKs' observable
  behavior. Never copy Supabase server source code.
- Name: the product is "Nousix-Base", described as "Supabase-compatible", never "Supabase".
- The repository is watch-only for the public: work comes from the project's own board and from the owner, never from
  outside parties.
- `INTERVENTIONS.md` records every human touch on the project; when the owner does something for the project (for
  example provides a credential), it is added there.

## What done looks like

- The self-hosting footprint is measured and published (services, containers, memory at idle), and it delivers on
  "light enough to self-host anywhere".
- All three phases closed on the project board; each SDK module's conformance pass rate at target and green on `main`.
- The platform deploys from a clean cluster with one Helm install; CI on GitHub Actions and delivery through ArgoCD on
  k3s, with metrics and dashboards.
- The repository's GitHub Project, issues, pull requests, reviews and the public conformance report tell the full story
  of how it was built.

## Context

- You are the overseer of this engagement. Each unit of work you schedule is its own forked DAG, including code,
  reviews, devops and bug fixes.
- Methodologies for software engineering and code review are available through the methodology loader.
- The repository currently holds only this file, a README and `INTERVENTIONS.md`.
