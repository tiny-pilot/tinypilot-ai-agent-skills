---
name: tinypilot
description: Control a remote computer through TinyPilot REST API endpoints for screenshots, keyboard, mouse, and paste actions. Use when users request TinyPilot-based remote control or KVM automation.
allowed-tools: Bash
user-invocable: true
disable-model-invocation: false
---

# TinyPilot Automation (Claude)

Use this skill to drive a remote machine via the TinyPilot REST API.

## Workflow

1. Confirm TinyPilot host URL (default `https://tinypilot.local`).
2. Authenticate: `POST /api/v1/auth`.
3. Run a screenshot-first loop: screenshot -> action -> screenshot verification.
4. Prefer keyboard navigation and paste for text over mouse-heavy navigation.
5. Use short delays between actions (1-2s normal UI, 3-8s app launches).

## Safety and reliability rules

- Never persist auth tokens to disk.
- Treat `204` screenshot responses as no-signal/sleep, then wake with a safe keystroke.
- Never send Enter immediately after a long paste; wait for text to arrive first.
- For mouse clicks, use move -> press -> release at the same coordinates.

## Endpoints

- `POST /api/v1/auth`
- `GET /api/v1/screenshot`
- `POST /api/v1/keystroke`
- `POST /api/v1/mouseEvent`
- `POST /api/v1/paste`
- `GET /state` (unofficial)

## Supporting docs

- See `references/api-reference.md` for payload/response details.
- See `references/examples.md` for `curl` command patterns.
