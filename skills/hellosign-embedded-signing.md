---
name: Embedded signing with Dropbox Sign
description: Create an embedded signature request and open the signing UI inside your own app.
api: openapi/hellosign-openapi-original.yaml
operations: [signatureRequestCreateEmbedded, embeddedSignUrl, signatureRequestGet]
---

# Embedded signing

Embed the signing experience directly in your web app using the
`hellosign-embedded` client library (`components/hellosign-components.yml`). Your
API app must supply a `client_id`; production embedded apps require app approval
(`sandbox/hellosign-sandbox.yml`).

## Steps

1. **Create the embedded request** — `signatureRequestCreateEmbedded`
   (POST `/signature_request/create_embedded`) with `client_id`, the `signers[]`,
   and the document (`file[]` or `file_url[]`). Capture the
   `signature_request_id` and each signature's `signature_id`.
2. **Get a sign URL** — `embeddedSignUrl`
   (GET `/embedded/sign_url/{signature_id}`) to obtain the short-lived
   `sign_url` for a signer.
3. **Open the UI** — pass `sign_url` to `HelloSign.open()` from the
   `hellosign-embedded` library, using the same `client_id`. Listen for the
   `sign` event client-side.
4. **Confirm server-side** — do not trust the client event alone; verify via
   `signatureRequestGet` or the `signature_request_signed` webhook.

## Rules

- `sign_url`s expire quickly — fetch them just-in-time, never cache.
- The `client_id` in `HelloSign.open()` must match the one used to create the
  request or the frame shows `sign_url_invalid`.
- Test with `test_mode=1` and the Embedded Testing Tool before going live.
