---
name: Tag data source objects for tag-based governance
description: >-
  Create data-source tags and associate them with database objects/columns in
  TrustLogix so tag-based (ABAC) access and masking policies can target them.
api: https://docs.trustlogix.io/trustlogix-api-documentation.md
auth: API key in the Authorization header; tenantId header required on tenant-scoped calls.
operations:
  - createDataSourceTags
  - validateTags
  - createTagAssociation
  - getTagReferences
  - getTagAssociationObjectTypes
---

# Tag data source objects for tag-based governance

Use this skill to build the tag catalog that tag-based access, row-access, and
masking policies depend on.

## Preconditions
- A valid **Authorization** API key, the **tenantId** header, and the target
  **accountId** (registered data source).

## Steps
1. **Create tags.** `POST /api/account_tag/tags` with an array of tag objects
   (`name`, `tagDatabase`, `tagSchema`, `tagValues`, `workspace`). Returns the
   `{ code, data, message }` envelope.
2. **Validate before use (optional).** `POST /api/account_tag/validate_tags`
   returns `{ success, message, remediationMsg, warningMessage }`.
3. **Discover taggable object types.**
   `GET /api/account_tag/object_types?accountId=...&tagKey=...` to list the object
   types available for a tag key.
4. **Associate tags with objects.** `POST /api/account_tag/tag_association` with
   `domain` (e.g. `SCHEMA`, `TABLE`), `objectDatabase[]`, `objectSchema[]`,
   `objectName[]`, `columnName[]`, and the `tagKey`/`tagValue`.
5. **Verify.** `GET /api/account_tag/tag_references` (paginated) or
   `/api/account_tag/tag_reference` to confirm the associations exist.

## Conventions and error handling
- Offset pagination: `page_no`, `page_size`, `sort_by`, `sort_order`.
- Tag creation/association are `POST` (create) and `PUT` (update); deletes use
  `PUT /api/account_tag/delete_tag` and `/delete_tag_association`.
- Handle `401`/`403`/`404` per `errors/trustlogix-problem-types.yml`.
