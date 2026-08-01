---
name: Consume monitoring alerts over the Connect API
description: List, inspect, and resolve Strise monitoring alerts programmatically to feed your own case-management or risk engine.
api: Strise Connect API (GraphQL)
endpoint: https://graphql.strise.ai/connect/graphql
operations: [alerts, alertData, alertUpdate]
source: https://docs.strise.ai/api/monitoring-alerts-api/
generated: '2026-07-21'
method: generated
---

# Consume monitoring alerts

Pull monitoring alerts into your own system. The surface is marked **beta** — pin to the documented fields and expect additive evolution. Three operations cover the loop.

## Auth
- POST GraphQL to `https://graphql.strise.ai/connect/graphql` with `Authorization: Bearer <token>`.

## Step 1 — List alerts (`alerts`)
An alert is a lightweight envelope: *something changed on this entity in this dataset*.

```graphql
query {
  alerts(where: { states: [UNRESOLVED], kinds: [PEP, SANCTIONS], page: { limit: 50, offset: 0 } }) {
    edges { node {
      kind          # PEP | SANCTIONS | RELATIONS | COMPANY_INFORMATION
      state         # UNRESOLVED | RESOLVED
      insertedAt
      computedAt
      resolvedBy
      resolvedAt
    } }
  }
}
```

`where` filters: `states`, `kinds` (the four dataset kinds), `entity`, `entityKind` (defaults `COMPANY`), `period`, and `page` (limit/offset).

## Step 2 — Fetch what changed (`alertData`)
The before/after change lives behind `alertData(alert: "<alert-id>")`; its shape depends on the alert's `kind`.

## Step 3 — Resolve (`alertUpdate`)
Use `alertUpdate` to resolve (or un-resolve) alerts by id once your workflow has handled them.

## Rules
- Paginate with `page: { limit, offset }`; iterate until `edges` is empty.
- Treat `kind`/`state` as enums exactly as documented.
- Field names may evolve additively (beta) — do not depend on undocumented fields.
