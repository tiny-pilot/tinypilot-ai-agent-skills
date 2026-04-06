# TinyPilot REST API Examples

## Authenticate

```bash
TOKEN=$(curl -sk -X POST https://tinypilot.local/api/v1/auth | jq -r '.token')
```

## Screenshot

```bash
curl -sk -H "Authorization: Bearer $TOKEN" \
  https://tinypilot.local/api/v1/screenshot -o screenshot.jpg
```

## Keystroke

```bash
curl -sk -X POST https://tinypilot.local/api/v1/keystroke \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"code":"Enter"}'
```

## Paste

```bash
curl -sk -X POST https://tinypilot.local/api/v1/paste \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"text":"echo hello","language":"en-US"}'
```
