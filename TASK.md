## Execution Task — Permission Posture: execute

READ-WRITE. You are authorized to carry out the request below, including the external-state changes it entails, within
its scope and nothing beyond it:

- the GitHub repository `Nousix-LLC/nousix-base` (code, branches, pull requests, reviews, issues, labels, milestones,
  GitHub Actions workflows) and its GitHub Project, `Nousix-Base Delivery` (Nousix-LLC project #3);
- the project's k3s cluster (kubectl context `k3d-nswe`): the `nousix-base*` namespaces you create and this project's
  own ArgoCD Applications. Do not touch other projects' namespaces or applications on the cluster.

Do not touch other repositories. Never force-push to `main`, and never rewrite merged history. The repository is
public: never commit secrets, tokens or credentials, and do not change its visibility, its interaction limits, or its
branch protection.

## Taskflow & Output Location

Author your taskflow (thread iterations, every forked DAG's `_FORK.md`, and all lifecycle and synthesis records) under
`~/nousix-runtime/nousix-base/taskflow/`. The product's source of truth is the GitHub repository; the taskflow is the
record of how the work was run, and it stays out of the public repository.

## The Request

Build Nousix-Base: a Supabase-compatible backend platform, as a clean-room implementation in Rust, developed as a real
software project in public. Build, run and deploy it on k3s.

The product, in phases:

| Phase | Scope |
|---|---|
| P1 — core | Postgres (used, not reimplemented); an auto-generated REST API over the database schema (PostgREST-compatible behavior); authentication: email/password, JWT issuance and verification, sessions/refresh, OAuth; an API gateway in front of the services; Helm charts and a working build → deploy → test loop |
| P2 — live data & files | Realtime: clients subscribe to database changes over websockets (Postgres change data capture); Storage: S3-style buckets and objects with access policies |
| P3 — compute & UI | Edge functions: HTTP-invoked functions on a WASM runtime; a web dashboard (Rust → WASM) to manage projects, tables, auth users, storage and functions |

Rust for all services. The only JavaScript in the system is the conformance harness, which uses the official Supabase
client SDK (supabase-js) exactly as a real customer would.

The yardstick is the official supabase-js SDK and its tests, run against a Nousix-Base deployment. Report the pass rate
for each SDK module (database/REST, auth, realtime, storage, functions) where the public can see it, and keep it current.

Don't try to do the whole build in one DAG. Use the repository's GitHub Project to define what each DAG does in terms of
new code; seed the backlog from the conformance suite's failing tests and the build-out each phase needs. Changes land
as pull requests, and you schedule DAGs to review those pull requests with the code-review methodologies. Deploy the
software on k3s, with GitHub Actions for CI and ArgoCD on k3s for delivery; the devops work is DAGs too. Report bugs as
issues and spawn DAGs to fix them.

Treat the GitHub Project and Issues like a real Jira environment: they are where you manage and schedule the work and the
code reviews.

## Constraints

- Clean room: build from Supabase's public documentation, public API behavior and the official client SDKs' observable
  behavior. Never copy Supabase server source code.
- Name: the product is "Nousix-Base", described as "Supabase-compatible", never "Supabase".
- The repository is watch-only for the public: work comes from the project's own board and from the owner, never from
  outside parties.
- `INTERVENTIONS.md` records every human touch on the project; when the owner does something for the project (for
  example provides a credential), it is added there.

## What done looks like

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
