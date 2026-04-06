---
name: tinypilot
description: >-
  Control a remote computer using the TinyPilot REST API for keyboard, mouse,
  screenshot, and paste operations. Use when the user mentions "TinyPilot",
  needs remote machine control, or asks for KVM automation.
metadata:
  author: TinyPilot
  version: 1.0.0
---

# TinyPilot Automation (Cursor)

Automation requires a valid TinyPilot Automation License.

Use the TinyPilot REST API to control remote systems.

## Session setup

1. Confirm TinyPilot URL (default `https://tinypilot.local`).
2. Authenticate (`POST /api/v1/auth`).
3. Capture screenshot to verify connection.

## Operational loop

Use **Screenshot -> Act -> Verify** for every action.

- Prefer keyboard navigation and paste over mouse when possible.
- Add delays after actions (1-2s normal, 3-8s app launch).
- Keep auth tokens in memory only.

## Endpoints

- `POST /api/v1/auth`
- `GET /api/v1/screenshot`
- `POST /api/v1/keystroke`
- `POST /api/v1/mouseEvent`
- `POST /api/v1/paste`
- `GET /state` (unofficial)

## References

- [API reference](references/api-reference.md)
- [Examples](references/examples.md)
