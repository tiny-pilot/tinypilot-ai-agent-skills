# TinyPilot REST API Reference

- Auth: `POST /api/v1/auth`
- Screenshot: `GET /api/v1/screenshot`
- Keystroke: `POST /api/v1/keystroke`
- Mouse: `POST /api/v1/mouseEvent`
- Paste: `POST /api/v1/paste`
- State (unofficial): `GET /state`

## Notes

- Do not send an `Origin` header when authenticating.
- Keep tokens in memory only.
- For paste, wait ~100ms per character before pressing Enter.
