---
name: Send a text message with OpenPhone/Quo
description: Send an SMS from a Quo workspace phone number to one or more recipients and confirm delivery.
api: openapi/openphone-openapi-original.json
operations: [listPhoneNumbers_v1, sendMessage_v1, listMessages_v1, getMessageById_v1]
---

# Send a text message

Use the Quo (OpenPhone) API to send an SMS from a workspace number.

## Auth
Send your API key in the `Authorization` header verbatim (NOT a Bearer token).
Base URL: `https://api.quo.com`. Rate limit: 10 requests/second.

## Steps
1. **Pick a sending number.** Call `listPhoneNumbers_v1` (`GET /v1/phone-numbers`) and choose a
   `phoneNumberId` (or the E.164 `from` number) owned by your workspace.
2. **Send the message.** Call `sendMessage_v1` (`POST /v1/messages`) with `phoneNumberId` (or `from`),
   `to` (an array of up to 10 E.164 numbers for a group message), and `content`. The call returns
   `202 Accepted`; the response includes the message `id`, `conversationId`, and `status`.
3. **Confirm.** Poll `getMessageById_v1` (`GET /v1/messages/{id}`) or `listMessages_v1`
   (`GET /v1/messages`) to observe the `status` transition, or subscribe to the `message.delivered` /
   `message.failed` webhook (see the manage-webhooks skill).

## Rules
- Sending to US numbers requires completed US carrier registration.
- MMS is not supported; messages are billed per segment (see pricing).
- There is **no idempotency key** — do not blindly retry a `202`/timeout; you may double-send.
- A `402` means insufficient credit balance; top up before retrying.
- On `429`, back off exponentially.
