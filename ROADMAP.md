# Roadmap

Orbital is on a multi-year trajectory from event infrastructure to Stellar's programmable runtime. This document describes the planned work in concrete terms. Dates are targets, not guarantees.

---

## Phase 0 — Foundation (now, `v0.x`)

**Goal:** a working, self-hostable event stack that any Stellar developer can run today.

| Area | Status |
|---|---|
| Classic payment event streaming via Horizon SSE | ✅ Done |
| HMAC-signed webhook delivery with retry | ✅ Done |
| React hooks (`useStellarEvent`, `useStellarPayment`, `useStellarActivity`) | ✅ Done |
| Reference Express server (`apps/server`) | ✅ Done |
| Public documentation site (`apps/web`) | ✅ Done |
| Testnet + mainnet support | ✅ Done |
| CI, CodeQL, and Dependabot | ✅ Done |

---

## Phase 1 — Production-grade Core (Q2–Q3 2026, `v1.0`)

**Goal:** a stability-pledged `v1.0` that teams can build on without worrying about breaking changes.

- **Full Stellar operation taxonomy** — account creation, trustline changes, offer operations, clawback, and all remaining Horizon operation types normalized into typed events.
- **Soroban event subscription** — subscribe to smart contract events by contract ID and topic filter via Stellar RPC.
- **PostgreSQL event registry** — durable storage for event history and replay, replacing the in-memory watcher state.
- **SSRF hardening pass** — audit and harden the webhook delivery pipeline against all known SSRF vectors.
- **v1.0 stability pledge** — `@orbital/pulse-core`, `@orbital/pulse-webhooks`, and `@orbital/pulse-notify` adopt semver with a documented stability contract. No breaking changes without a major version bump.
- **npm publish** — all three packages published to the npm registry under `@orbital/`.

---

## Phase 2 — Programmable Runtime (Q4 2026)

**Goal:** move from event delivery to event-driven computation.

- **ABI Registry** — on-chain contract ABI storage so pulse-core can decode Soroban event data automatically without requiring consumers to hold ABIs locally.
- **Reactor contracts** — an SDK for writing Soroban contracts that react to events from other contracts, creating composable on-chain automation.
- **Replay store** — deterministic replay of any event window from the PostgreSQL store, enabling backfill, testing, and audit workflows.
- **First SEP submission** — propose a Stellar Ecosystem Proposal formalizing the event normalization format so other implementations can interoperate.

---

## Phase 3 — Intent Layer (2027+)

**Goal:** turn event subscriptions into programmable intent pipelines.

- **Intent compiler** — declare "when X happens, do Y" as a typed intent; the compiler compiles it to a webhook + reactor contract + replay policy.
- **x402 middleware** — payment-gated API access via the HTTP 402 / x402 protocol, so event streams can be monetized natively on Stellar.
- **ZK-attested delivery** — zero-knowledge proofs that a webhook was delivered and acknowledged, enabling trustless SLA verification between event producers and consumers.

---

## What's Not on This Roadmap

- A hosted/managed cloud version of Orbital (this is and will remain a self-hostable OSS project)
- Support for non-Stellar networks
- A GUI dashboard (the web app is documentation only)

---

## Contributing to the Roadmap

If you have a feature request or want to propose a change to the roadmap, open a GitHub Discussion in the [Ideas category](https://github.com/orbital/orbital/discussions/categories/ideas). Roadmap items that attract significant community interest move up in priority.
