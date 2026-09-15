---
name: send-rcs-or-whatsapp-message
description: Check whether a recipient can receive RCS, then send a rich message (with SMS fallback) via the RubiConnect Messaging Platform.
api: RubiConnect Messaging Platform
generated: '2026-09-15'
method: generated
source: openapi/rubiconnect-openapi.json
operations:
  - checkCapability
  - sendMessage
---

# Send an RCS / WhatsApp message

Grounded in `openapi/rubiconnect-openapi.json`. Base URL `https://console.rubiconnect.com/api/v1`. Authenticate with header `X-API-Key: rc_live_...` (or `Authorization: Bearer <token>`).

## Steps

1. **Check capability** — `POST /capabilities/check` (`checkCapability`) with `{ "recipient": "<E.164>", "agentId": "<agentId>" }`. Read `isRcs` and `optedOut` in the response to decide the channel.
2. **Send** — `POST /messages/send` (`sendMessage`). Provide `recipient`, `agentId`, and one of `text`, `mediaUrl`, or `templateId` (+ `contentVariables`). Set `allowSmsFallback: true` when `isRcs` is false so the message still reaches the handset. Optionally set `callbackUrl` to receive `message.status_updated` webhook events.
3. **Confirm** — a `202 Accepted` returns `messageId` (and `broadcastId` for a streamed broadcast) plus `status` and `channel`.

## Rules

- **Not idempotent / not reversible**: there is no Idempotency-Key header and no cancel/recall operation. Do NOT blindly retry `sendMessage` on a timeout — a retry can send a duplicate. Verify via the `messageId` / webhook first.
- Respect `optedOut: true` — do not message a recipient who has opted out.
- Errors use a flat `{ success: false, error: "..." }` envelope: `400` invalid params, `401` bad key, `403` feature disabled for your tier, `404` agent/template not found.
