# TinyPilot REST API Examples

```bash
TOKEN=$(curl -sk -X POST https://tinypilot.local/api/v1/auth | jq -r '.token')
```

```bash
curl -sk -H "Authorization: Bearer $TOKEN" \
  https://tinypilot.local/api/v1/screenshot -o screenshot.jpg
```

```bash
curl -sk -X POST https://tinypilot.local/api/v1/keystroke \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"code":"Enter"}'
```
