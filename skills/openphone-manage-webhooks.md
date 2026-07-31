---
name: Subscribe to OpenPhone/Quo events with webhooks
description: Create, list, and delete webhook subscriptions for message and call events.
api: openapi/openphone-openapi-original.json
operations: [createMessageWebhook_v1, createCallWebhook_v1, listWebhooks_v1, getWebhookById_v1, deleteWebhookById_v1]
---

# Manage webhooks

Subscribe to Quo (OpenPhone) events so your app reacts to messages and calls.

## Auth
`Authorization: <API key>` header; base URL `https://api.quo.com`.

## Steps
1. **Create a subscription.** Call `createMessageWebhook_v1` (`POST /v1/webhooks/messages`) or
   `createCallWebhook_v1` (`POST /v1/webhooks/calls`) with your HTTPS callback `url` and the event
   types you want. Companions exist for call summaries (`createCallSummaryWebhook_v1`) and transcripts
   (`createCallTranscriptWebhook_v1`). Returns `201` with the webhook `id`.
2. **List / inspect.** Use `listWebhooks_v1` (`GET /v1/webhooks`) and `getWebhookById_v1`
   (`GET /v1/webhooks/{id}`) to audit active subscriptions.
3. **Handle deliveries.** Events arrive as JSON `POST`s. Message events include `message.received`,
   `message.delivered`, `message.failed`, `message.undelivered`; call events include `call.completed`,
   `call.ringing`, `call.answered`, `call.missed`, `call.voicemail.completed`,
   `call.recording.completed`, `call.summary.completed`, `call.transcript.completed`.
4. **Remove.** Call `deleteWebhookById_v1` (`DELETE /v1/webhooks/{id}`) to unsubscribe.

## Rules
- Up to 50 webhooks per workspace (unified beta API).
- The four legacy per-type create endpoints are being superseded by a unified `POST /webhooks`; new
  integrations should prefer the beta unified endpoint where available.
- Return `2xx` quickly from your handler and process asynchronously.
