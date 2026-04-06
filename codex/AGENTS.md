# TinyPilot REST API Skill Guidance

Use this guidance when users ask to control remote systems through TinyPilot.

## Trigger conditions

- User mentions TinyPilot.
- User asks for remote keyboard/mouse/screenshot automation.
- User asks for BIOS or boot-menu control through KVM.

## Constraints

- Automation requires a valid TinyPilot Automation License.
- Never store TinyPilot auth tokens on disk.
- Use `https://tinypilot.local` as default host unless user provides another.

## Standard procedure

1. Authenticate with `POST /api/v1/auth`.
2. Take a screenshot (`GET /api/v1/screenshot`) to inspect current state.
3. Perform exactly one action (keystroke, paste, or mouse event).
4. Wait for UI response.
5. Take another screenshot to verify result.
6. Repeat until user objective is complete.

## Endpoint reminders

- `POST /api/v1/auth` -> token
- `GET /api/v1/screenshot` -> image (or `204` no signal)
- `POST /api/v1/keystroke`
- `POST /api/v1/mouseEvent`
- `POST /api/v1/paste`
- `GET /state` (unofficial, for resolution)

## Input reliability rules

- Prefer paste for text, keystroke for shortcuts/navigation.
- Wait ~100ms per character after paste before pressing Enter.
- For mouse clicks use move -> press -> release at same coordinates.
