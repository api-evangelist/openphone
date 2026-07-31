---
name: Review a call with AI summary and transcript
description: Retrieve a call, its recordings, voicemail, AI summary, and transcript for review workflows.
api: openapi/openphone-openapi-original.json
operations: [listCalls_v1, getCallById_v1, getCallRecordings_v1, getCallVoicemails_v1, getCallSummary_v1, getCallTranscript_v1]
---

# Review a call with AI

Pull a call and its AI-generated artifacts for QA, coaching, or logging.

## Auth
`Authorization: <API key>` header; base URL `https://api.quo.com`.

## Steps
1. **Find the call.** Call `listCalls_v1` (`GET /v1/calls`) filtered by `phoneNumberId`, `participants`,
   or a `createdAfter`/`createdBefore` window; page with `pageToken`/`maxResults`. Grab a `callId`.
2. **Get details.** Call `getCallById_v1` (`GET /v1/calls/{callId}`).
3. **Media.** Call `getCallRecordings_v1` (`GET /v1/call-recordings/{callId}`) for recording segments
   (oldest first) and `getCallVoicemails_v1` (`GET /v1/call-voicemails/{callId}`) for voicemail —
   voicemail fields return `null` until processing completes.
4. **AI artifacts.** Call `getCallSummary_v1` (`GET /v1/call-summaries/{callId}`) and
   `getCallTranscript_v1` (`GET /v1/call-transcripts/{id}`).

## Rules
- Call summaries and transcripts are only available on **business and scale** plans; expect `403` or
  empty data otherwise.
- To react in real time instead of polling, subscribe to `call.completed`,
  `call.recording.completed`, `call.summary.completed`, and `call.transcript.completed` webhooks.
