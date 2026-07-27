---
name: Send a signature request with Dropbox Sign
description: Send a document out for signature, poll its status, and download the signed file.
api: openapi/hellosign-openapi-original.yaml
operations: [signatureRequestSend, signatureRequestGet, signatureRequestList, signatureRequestFiles, signatureRequestRemind, signatureRequestCancel]
---

# Send a signature request

Base URL: `https://api.hellosign.com/v3`. Authenticate with your API key as the
HTTP Basic username (empty password), or an OAuth 2.0 Bearer token
(`request_signature` / `signature_request_access` scope). See
`authentication/hellosign-authentication.yml` and `scopes/hellosign-scopes.yml`.

While integrating, set `test_mode=1` so documents are non-binding and unbilled
(`sandbox/hellosign-sandbox.yml`).

## Steps

1. **Send the request** — `signatureRequestSend` (POST `/signature_request/send`).
   Submit as `multipart/form-data`: `title`, `subject`, `message`, one or more
   `signers[n][email_address]` + `signers[n][name]`, and the document via
   `file[]` (upload) or `file_url[]`. Capture `signature_request_id` from the
   response.
2. **Track status** — `signatureRequestGet` (GET `/signature_request/{signature_request_id}`)
   to read `is_complete` and per-signer `status_code`; or watch the
   `signature_request_signed` / `signature_request_all_signed` webhooks
   (`asyncapi/hellosign-events-webhooks.yml`) instead of polling.
3. **Nudge or cancel** — `signatureRequestRemind` to re-email a pending signer,
   or `signatureRequestCancel` to abort.
4. **Download the signed document** — once `is_complete` is true, call
   `signatureRequestFiles` (GET `/signature_request/files/{signature_request_id}`)
   with `file_type=pdf`.

## Rules

- Requests are **not idempotent** — do not retry a `send` blindly; check
  `signatureRequestList` first (`conventions/hellosign-conventions.yml`).
- Handle errors from the `error.error_name` envelope; `exceeded_rate` (429) is
  retryable with backoff, `bad_request` (400) is not
  (`errors/hellosign-error-codes.yml`).
- List endpoints paginate with `page` / `page_size` and return a `list_info`
  block.
