# TinyPilot REST API Reference

## Authentication

- `POST /api/v1/auth` -> `{"token":"<uuid>"}`.
- Do not include an `Origin` header.
- Keep tokens in memory only.

## Screenshot

- `GET /api/v1/screenshot`
- `200` returns JPEG, `204` means no video signal.

## Keystroke

- `POST /api/v1/keystroke`
- Send `KeyboardEvent.code` and optional modifiers.

## Mouse

- `POST /api/v1/mouseEvent`
- Use relative coordinates from `0.0` to `1.0`.
- Click flow: move -> press -> release.

## Paste

- `POST /api/v1/paste`
- Required fields: `text`, `language`.
- Wait ~100ms per character before confirming.

## Display State (Unofficial)

- `GET /state`
- Use `result.source.resolution.width/height` for coordinate conversion.
