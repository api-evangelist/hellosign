---
name: Reuse a template and bulk-send with Dropbox Sign
description: Create a reusable template and send it to many signers in one bulk job.
api: openapi/hellosign-openapi-original.yaml
operations: [templateCreate, templateGet, templateList, signatureRequestSendWithTemplate, signatureRequestBulkSendWithTemplate, bulkSendJobGet]
---

# Reuse a template and bulk-send

Authenticate as in `hellosign-send-signature-request.md`. Templates need
`template_access`; bulk send needs `request_signature`.

## Steps

1. **Find or create a template** — list existing templates with `templateList`
   (GET `/template/list`), or create one via `templateCreate`
   (POST `/template/create`). Read a template's roles and merge fields with
   `templateGet` (GET `/template/{template_id}`).
2. **Send to one signer** — `signatureRequestSendWithTemplate`
   (POST `/signature_request/send_with_template`) with `template_ids[]`, the
   `signers[n][role]` + `email_address`, and any `custom_fields`.
3. **Bulk send to many** — `signatureRequestBulkSendWithTemplate`
   (POST `/signature_request/bulk_send_with_template`) with `template_ids[]` and a
   `signer_file` CSV (or inline `signer_list`). Capture `bulk_send_job_id`.
4. **Monitor the job** — `bulkSendJobGet`
   (GET `/bulk_send_job/{bulk_send_job_id}`) to see per-request status.

## Rules

- Match `signers[n][role]` names exactly to the roles defined on the template or
  the call returns `bad_request`.
- Use `test_mode=1` during development (`sandbox/`).
- Prefer the `signature_request_all_signed` webhook over polling large bulk jobs.
