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
- Send `KeyboardEvent.code` plus optional modifiers.

## Mouse

- `POST /api/v1/mouseEvent`
- Use relative coordinates from 0.0 to 1.0.
- For clicks: move -> press -> release.

## Paste

- `POST /api/v1/paste`
- Send `text` + `language` (`en-US`, `en-GB`, `de-DE`).
- Wait about 100ms per character before confirming input.

## Display State (Unofficial)

- `GET /state`
- Use `result.source.resolution.width/height` for mouse conversion.
