# TinyPilot REST API Examples

## Complete session example — bash/zsh (Mac/Linux)

This is the canonical pattern. Every step is a separate shell call — one API call, then
read the result before proceeding. Never batch paste + keystroke + screenshot together.

**Step 1 — API key**
```bash
# Create a key under System → Automation (TinyPilot Pro 3.2.0+)
: "${TINYPILOT_API_KEY:?set TINYPILOT_API_KEY}"
API_KEY="$TINYPILOT_API_KEY"
```

**Step 2 — Screenshot to verify current state before acting**
```bash
curl -sk -H "Authorization: Bearer $API_KEY" \
  https://tinypilot.local/api/v1/screenshot -o /tmp/screen.jpg
```
→ Read the screenshot. Confirm the terminal is ready before continuing.

**Step 3 — Paste a command (with correct timing)**
```bash
TEXT="uptime"
printf '{"text":"%s","language":"en-US"}' "$TEXT" > /tmp/paste.json
curl -sk -X POST https://tinypilot.local/api/v1/paste \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d @/tmp/paste.json
sleep $(echo "$TEXT" | awk '{print length/10 + 1}')
```
→ Wait completes here. Do NOT send Enter in this same block.

**Step 4 — Screenshot to verify the text appeared correctly**
```bash
curl -sk -H "Authorization: Bearer $API_KEY" \
  https://tinypilot.local/api/v1/screenshot -o /tmp/screen.jpg
```
→ Read and confirm "uptime" is visible at the prompt. Only proceed if it looks right.

**Step 5 — Send Enter**
```bash
curl -sk -X POST https://tinypilot.local/api/v1/keystroke \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"code":"Enter"}'
sleep 1
```

**Step 6 — Screenshot to read the output**
```bash
curl -sk -H "Authorization: Bearer $API_KEY" \
  https://tinypilot.local/api/v1/screenshot -o /tmp/screen.jpg
```
→ Read the result. The command output is now visible on screen.

---

## Quick reference

### API key

```bash
API_KEY="$TINYPILOT_API_KEY"
```

### Screenshot

```bash
curl -sk -H "Authorization: Bearer $API_KEY" \
  https://tinypilot.local/api/v1/screenshot -o /tmp/screen.jpg
```

### Keystroke — common keys

```bash
# Enter
curl -sk -X POST https://tinypilot.local/api/v1/keystroke \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"code":"Enter"}'

# Ctrl+C
curl -sk -X POST https://tinypilot.local/api/v1/keystroke \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"code":"KeyC","ctrlLeft":true}'

# Cmd+Space (macOS Spotlight)
curl -sk -X POST https://tinypilot.local/api/v1/keystroke \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"code":"Space","metaLeft":true}'
```

### Mouse click (left-click at given position)

```bash
# Move → Press → Release at (x=0.5, y=0.5)
for BUTTONS in 0 1 0; do
  curl -sk -X POST https://tinypilot.local/api/v1/mouseEvent \
    -H "Authorization: Bearer $API_KEY" \
    -H "Content-Type: application/json" \
    -d "{\"buttons\":$BUTTONS,\"relativeX\":0.5,\"relativeY\":0.5,\"verticalWheelDelta\":0,\"horizontalWheelDelta\":0}"
  sleep 0.05
done
```

### Get display resolution

```bash
curl -sk -H "Authorization: Bearer $API_KEY" https://tinypilot.local/state \
  | jq '.result.source.resolution'
```
