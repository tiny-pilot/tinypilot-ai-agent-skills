# TinyPilot REST API Reference

## Authentication

- `POST /api/v1/auth` returns `{"token":"<uuid>"}`.
- Do not include an `Origin` header.
- Keep token in memory only; do not write to disk.

## Screenshot

- `GET /api/v1/screenshot`
- `200` returns JPEG, `204` means no video signal.

## Keystroke

- `POST /api/v1/keystroke`
- Required field: `code` — a [KeyboardEvent.code](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code) string (e.g. `"KeyT"`, `"Space"`, `"Enter"`, `"Delete"`).
- Modifier fields (all optional booleans, default `false`):
  - `ctrlLeft`, `ctrlRight`
  - `altLeft`, `altRight`
  - `shiftLeft`, `shiftRight`
  - `metaLeft`, `metaRight` (Win key / Cmd key)
- Returns `200` on success, `400` if malformed, `500` if HID forwarding fails.
- Common key codes: `Enter`, `Space`, `Escape`, `Tab`, `Backspace`, `Delete`, `ArrowUp/Down/Left/Right`, `KeyA`–`KeyZ`, `Digit0`–`Digit9`, `F1`–`F12`.

## Mouse

- `POST /api/v1/mouseEvent`
- Required fields: `buttons` (int), `relativeX` (0.0–1.0), `relativeY` (0.0–1.0), `verticalWheelDelta` (-1/0/1), `horizontalWheelDelta` (-1/0/1).
- `buttons`: 0 = none, 1 = left, 2 = right (matches [MouseEvent.buttons](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/buttons)).
- For clicks: send move (buttons=0) → press (buttons=1) → release (buttons=0).

## Paste

- `POST /api/v1/paste`
- Required fields: `text` (string) and `language` (`en-US`, `en-GB`, `de-DE`, etc.).
- ⚠️ **After the API call returns, wait `(100ms × len(text)) + 1000ms` before sending any follow-up keystroke.** The API returns immediately but characters are still being replayed into the remote terminal. Sending Enter early splits the command mid-paste, causing the first fragment to execute and the rest to be typed as a new command.
- Keep pasted text short and simple — avoid semicolons or special shell syntax in a single paste if the target shell may misinterpret them. Chain commands with `&&` rather than `;` where possible, or paste and execute one command at a time.

## Display State (Unofficial)

- `GET /state`
- Use `result.source.resolution.width/height` for mouse conversion.

## Full official API documentation

See [official-api-docs.md](official-api-docs.md) for the complete TinyPilot REST API reference including full HTTP request/response examples and a working Python sample script.
