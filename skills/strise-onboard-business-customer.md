---
name: Onboard a business customer with Strise
description: Screen a company by organization number, review its PEP/sanctions/ownership exposure, and add it to your monitoring portfolio via the Strise Connect (GraphQL) API.
api: Strise Connect API (GraphQL)
endpoint: https://graphql.strise.ai/connect/graphql
operations: [companyIdentifierSearch, companyAddToMonitoring, companiesAddToMonitoring]
source: https://docs.strise.ai/api/onboarding-a-business-customer/
generated: '2026-07-21'
method: generated
---

# Onboard a business customer

Use this skill to onboard a business customer through the Strise Connect GraphQL API. Onboarding is two steps: **screen the company**, then **add it to monitoring**.

## Auth
- POST GraphQL to `https://graphql.strise.ai/connect/graphql`.
- Header: `Authorization: Bearer <token>` (request access via tech@strise.ai).

## Step 1 — Screen the company
Look the company up by national organization number + ISO country code with `companyIdentifierSearch`. This resolves the Strise global entity id and returns screening exposure.

```graphql
query {
  companyIdentifierSearch(input: { nationalId: "123456789", country: NO }) {
    sanctioned
    relationships {
      edges { node {
        person {
          name
          roles { title beneficialOwner }
          pep { isPep pepRoles { role country } }
          sanctions { isSanctioned sanctionLists }
        }
      } }
    }
  }
}
```

Inspect `sanctioned` (is the company itself sanctioned) and each related person's `pep`/`sanctions` and `beneficialOwner` flags. Beneficial owners and roles are screened for PEP and sanctions per Strise's screening rules.

## Step 2 — Add to your monitoring portfolio
Once reviewed, add the company with `companyAddToMonitoring` (single) or `companiesAddToMonitoring` (bulk):

```graphql
mutation {
  companyAddToMonitoring(input: { companyId: "company-global-id" }) {
    company { name monitoringStatus }
  }
}
```

After this the company is screened daily as part of your portfolio.

## Rules
- Always resolve the Strise `companyId` from `companyIdentifierSearch` first — never guess ids.
- Errors surface in the GraphQL top-level `errors[]` array alongside partial `data`.
- Removing later uses `CompanyRemoveFromMonitoring`; removal does not delete screening/review history.
