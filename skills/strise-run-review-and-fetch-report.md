---
name: Run a review and fetch its PDF report
description: Create a company review through the Strise Connect API, then fetch the generated PDF report using the caller-supplied review id.
api: Strise Connect API (GraphQL)
endpoint: https://graphql.strise.ai/connect/graphql
operations: [reviewCompanyCreate, review, reviewTriggerEventGenerate]
source: https://docs.strise.ai/api/api-faq/
generated: '2026-07-21'
method: generated
---

# Run a review and fetch its report

Create a compliance review for a company and retrieve the generated PDF.

## Auth
- POST GraphQL to `https://graphql.strise.ai/connect/graphql` with `Authorization: Bearer <token>`.

## Step 1 — Create the review
Call `reviewCompanyCreate` and pass a **caller-supplied unique `id`**. This id is bound to the review and is how you fetch its report later, so store it in your system.

## Step 2 — Fetch the PDF
Query `review` by that id; `pdf` returns a Base64-encoded string you decode into a PDF file.

```graphql
query ReviewPDF {
  review(where: { id: "ea1b3bd8-00bf-4714-b4f7-581d84e278e5" }) {
    pdf
  }
}
```

The report remains retrievable for as long as you keep the review id.

## Step 3 (optional) — Trigger a test event
`reviewTriggerEventGenerate` exercises your configured trigger rules. Note the sandbox only evaluates the **first** trigger rule; production evaluates all of them. A `success: false` return usually means no trigger rule is configured (Settings → Risks and Monitoring → Add trigger).

## Rules
- The `id` on `reviewCompanyCreate` is your correlation/idempotency key — reuse it to re-fetch rather than creating duplicate reviews.
- Report email recipients are configured in the Strise app (Team settings), not per API call.
- The lightweight sidepanel PDF is **not** available via the API — use this review flow.
