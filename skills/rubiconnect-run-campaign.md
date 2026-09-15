---
name: launch-broadcast-campaign
description: Launch a RubiConnect broadcast campaign to a contact list and poll its delivery status and performance.
api: RubiConnect Messaging Platform
generated: '2026-09-15'
method: generated
source: openapi/rubiconnect-openapi.json
operations:
  - createCampaign
  - getCampaign
---

# Launch and track a broadcast campaign

Grounded in `openapi/rubiconnect-openapi.json`. Base URL `https://console.rubiconnect.com/api/v1`. Authenticate with header `X-API-Key: rc_live_...`.

## Steps

1. **Create** — `POST /campaigns` (`createCampaign`) with `name`, `agentId`, `templateId` (and optional `flowId`), a `recipientSource` plus either `contactsUrl` (+ `urlFormat`) or `contactListName`, and `sendNow`. Set `allowSmsFallback` to reach non-RCS handsets. A `201 Created` returns `campaignId` and `status`.
2. **Track** — `GET /campaigns` (`getCampaign`) to retrieve campaign status and stats. Poll until the campaign reaches a terminal state.

## Rules

- **Not idempotent**: `createCampaign` has no idempotency key. Do not re-POST on a timeout without first checking whether a campaign was created, or you may broadcast twice.
- Provide a valid `templateId` (pre-approved) and `agentId`; a `404` means one was not found. `400` means invalid parameters, `401` means a missing/invalid key.
- Use the MCP tools `get_campaign_status` and `get_campaign_performance` for richer live metrics (delivery, read rates, CTR) than the REST `getCampaign` exposes.
