---
name: Create and publish a data access policy
description: >-
  Create a data access policy on a TrustLogix-registered data source account,
  preview the objects it will impact, then review and publish it so the grants
  are enforced on the underlying platform (Snowflake, Databricks, Dremio, etc.).
api: https://docs.trustlogix.io/trustlogix-api-documentation.md
auth: API key in the Authorization header; tenantId header required on tenant-scoped calls.
operations:
  - createAccessPolicy
  - getImpactedObject
  - getReviewAndPublishablePolicies
  - reviewPublishPolicy
  - publishPolicy
  - listAccessPolicies
---

# Create and publish a data access policy

Use this skill to author a data access policy for a registered data source
account and roll it out to the underlying data platform.

## Preconditions
- A valid **Authorization** API key.
- The **tenantId** header for your tenant, and the target **accountId** (the
  registered data source account).

## Steps
1. **Create the policy.** `POST /api/account/:accountId/access_policies`
   (`createAccessPolicy`) with the policy body — `name`, `objectType`,
   `objectNameList`/`securedObjects`, `rules[]`, and `principals[]`. The response
   returns the standard `{ code, data, message }` envelope.
2. **Preview impact (recommended).**
   `POST /api/account/:accountId/access_policies/impacted_objects?policyId=...`
   (`getImpactedObject`) to see the `dapImpactedObjects[]` the policy will affect
   before it is enforced.
3. **Find publishable policies.**
   `GET /api/account/:accountId/access_policies/review_publish_policies`
   (`getReviewAndPublishablePolicies`) to confirm the policy is ready.
4. **Review and publish.**
   `POST /api/account/:accountId/access_policies/:id/review_publish_policy`
   (`reviewPublishPolicy`) — or `.../:id/publish_policy` (`publishPolicy`) — to
   deploy the grants. Pass `includeViewAccessGrants` as needed.
5. **Verify.** `GET /api/account/:accountId/access_policies`
   (`listAccessPolicies`, paginated with `page_no`/`page_size`/`sort_by`/
   `sort_order`) and confirm the policy `status` is published.

## Conventions and error handling
- Pagination is offset-based: `page_no`, `page_size`, `sort_by`
  (default `lastModifiedDate`), `sort_order` (default `DESC`).
- Handle `401` (bad/missing key), `403` (insufficient permissions), and `404`
  (account/policy not found). See `errors/trustlogix-problem-types.yml`.
- No idempotency-key contract is documented; do not blindly retry mutating
  publish calls.
