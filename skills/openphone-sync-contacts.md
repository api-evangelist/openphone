---
name: Sync contacts into OpenPhone/Quo
description: Create, look up, update, and de-duplicate workspace contacts, including custom fields.
api: openapi/openphone-openapi-original.json
operations: [getContactCustomFields_v1, createContact_v1, listContacts_v1, getContactById_v1, updateContactById_v1, deleteContact_v1]
---

# Sync contacts

Keep an external system's contacts in sync with a Quo (OpenPhone) workspace.

## Auth
`Authorization: <API key>` header; base URL `https://api.quo.com`.

## Steps
1. **Discover custom fields.** Call `getContactCustomFields_v1` (`GET /v1/contact-custom-fields`) to
   learn the workspace's user-defined fields (they can only be created inside Quo, not via the API).
2. **Upsert by external id.** For each source record, call `listContacts_v1` (`GET /v1/contacts`) with
   `externalIds` and `sources` to check for an existing match.
   - If none, call `createContact_v1` (`POST /v1/contacts`) with `defaultFields` (firstName, lastName,
     company, role, emails, phoneNumbers), any `customFields`, and your `externalId`/`source`.
   - If found, call `updateContactById_v1` (`PATCH /v1/contacts/{id}`) to reconcile changes.
3. **Read back.** Use `getContactById_v1` (`GET /v1/contacts/{id}`) to confirm.
4. **Remove.** Call `deleteContact_v1` (`DELETE /v1/contacts/{id}`) for tombstoned source records.

## Rules
- Always set `externalId` + `source` so future syncs can match idempotently (the API has no
  Idempotency-Key; external-id matching is your dedupe mechanism).
- Paginate `listContacts_v1` with `pageToken`/`maxResults`, following `nextPageToken`.
- A `409` indicates a conflict (e.g. duplicate); resolve before retrying.
