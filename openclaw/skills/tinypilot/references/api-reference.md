# TinyPilot REST API Reference

- `POST /api/v1/auth` -> bearer token
- `GET /api/v1/screenshot` -> current display image
- `POST /api/v1/keystroke` -> one key press/release
- `POST /api/v1/mouseEvent` -> cursor move/click/wheel
- `POST /api/v1/paste` -> text input
- `GET /state` -> display resolution (unofficial)

## Guardrails

- Do not include an `Origin` header on auth.
- Keep token in memory only.
- Wait after paste before submit actions.
