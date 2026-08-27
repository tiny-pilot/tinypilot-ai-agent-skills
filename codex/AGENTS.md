# TinyPilot REST API Skill Guidance

Use this guidance when users ask to control remote systems through TinyPilot.

Requires TinyPilot Pro **3.2.0+**, an Automation License, and an API key from **System → Automation**.

## Trigger conditions

- User mentions TinyPilot.
- User asks for remote keyboard/mouse/screenshot automation.
- User asks for BIOS or boot-menu control through KVM.

## Constraints

- Do not commit API keys; prefer `TINYPILOT_API_KEY` in the environment for curl examples.
- Use `https://tinypilot.local` as default host unless user provides another.
- In PowerShell use `curl.exe` (not `curl`), and write JSON bodies to a temp file (`-d @path`).

## Standard procedure

1. Load an API key (`TINYPILOT_API_KEY` / System → Automation).
2. Take a screenshot (`GET /api/v1/screenshot`) to inspect current state.
3. Perform exactly one action (keystroke, paste, or mouse event).
4. Wait for UI response (1-2s normal, 3-8s app launch).
5. Take another screenshot to verify result.
6. Repeat until user objective is complete.

Use **Screenshot → Act → Verify** for every action — no exceptions. One paste OR one keystroke per step; screenshot before and after. Keep paste payloads short — one command at a time.

### ⚠️ Critical: paste timing

**Wait `(100ms × character count) + 1000ms` after paste before sending any keystroke.** The API returns immediately but characters are still being replayed. Sending Enter early splits the command mid-paste.

### If something isn't working

**Do not retry the same approach.** Take a screenshot, reassess, and try something meaningfully different — a shorter command, a different tool, or mouse/GUI navigation as a fallback.

## Endpoints

| Method | Path | Purpose |
|--------|------|---------|
| GET | `/api/v1/screenshot` | Returns JPEG (`200`) or no signal (`204`). |
| POST | `/api/v1/keystroke` | Send a key. Required: `code` ([KeyboardEvent.code](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code)). Optional modifiers: `ctrlLeft`, `ctrlRight`, `altLeft`, `altRight`, `shiftLeft`, `shiftRight`, `metaLeft`, `metaRight`. |
| POST | `/api/v1/mouseEvent` | Required: `buttons` (0=none, 1=left, 2=right), `relativeX`/`relativeY` (0.0–1.0), `verticalWheelDelta`, `horizontalWheelDelta`. Click = move→press→release. |
| POST | `/api/v1/paste` | Required: `text`, `language` (`en-US`, `en-GB`, `de-DE`, etc.). Wait after call — see paste timing above. |
| GET | `/state` | Unofficial. Use `result.source.resolution.width/height` for mouse coordinate conversion. |

Common key codes: `Enter`, `Space`, `Escape`, `Tab`, `Backspace`, `Delete`, `ArrowUp/Down/Left/Right`, `KeyA`–`KeyZ`, `Digit0`–`Digit9`, `F1`–`F12`.

## Input reliability rules

- Prefer paste for text, keystroke for shortcuts/navigation.
- For mouse clicks use move (buttons=0) → press (buttons=1) → release (buttons=0) at same coordinates.
- Chain commands with `&&` rather than `;` where possible, or paste and execute one command at a time.

## Example — bash/zsh

Each step is a **separate** shell call. Never batch paste + keystroke + screenshot.

```bash
# 1. API key (System → Automation, TinyPilot Pro 3.2.0+)
: "${TINYPILOT_API_KEY:?set TINYPILOT_API_KEY}"
API_KEY="$TINYPILOT_API_KEY"

# 2. Screenshot
curl -sk -H "Authorization: Bearer $API_KEY" \
  https://tinypilot.local/api/v1/screenshot -o /tmp/screen.jpg

# 3. Paste (then wait)
TEXT="uptime"
printf '{"text":"%s","language":"en-US"}' "$TEXT" > /tmp/paste.json
curl -sk -X POST https://tinypilot.local/api/v1/paste \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d @/tmp/paste.json
sleep $(echo "$TEXT" | awk '{print length/10 + 1}')

# 4. Screenshot to verify paste appeared
curl -sk -H "Authorization: Bearer $API_KEY" \
  https://tinypilot.local/api/v1/screenshot -o /tmp/screen.jpg

# 5. Send Enter
curl -sk -X POST https://tinypilot.local/api/v1/keystroke \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"code":"Enter"}'
sleep 1

# 6. Screenshot to read result
curl -sk -H "Authorization: Bearer $API_KEY" \
  https://tinypilot.local/api/v1/screenshot -o /tmp/screen.jpg
```

## Example — PowerShell

```powershell
# 1. API key (System → Automation, TinyPilot Pro 3.2.0+)
$API_KEY = $env:TINYPILOT_API_KEY
if (-not $API_KEY) { throw "Set TINYPILOT_API_KEY" }

# 2. Screenshot
curl.exe -sk -H "Authorization: Bearer $API_KEY" `
  https://tinypilot.local/api/v1/screenshot -o "$env:TEMP\screen.jpg"

# 3. Paste (then wait)
$text = "uptime"
'{"text":"' + $text + '","language":"en-US"}' | Out-File -Encoding ASCII "$env:TEMP\paste.json"
curl.exe -sk -X POST https://tinypilot.local/api/v1/paste `
  -H "Authorization: Bearer $API_KEY" `
  -H "Content-Type: application/json" `
  -d "@$env:TEMP\paste.json"
Start-Sleep -Milliseconds ($text.Length * 100 + 1000)

# 4. Screenshot to verify paste appeared
curl.exe -sk -H "Authorization: Bearer $API_KEY" `
  https://tinypilot.local/api/v1/screenshot -o "$env:TEMP\screen.jpg"

# 5. Send Enter
'{"code":"Enter"}' | Out-File -Encoding ASCII "$env:TEMP\ks.json"
curl.exe -sk -X POST https://tinypilot.local/api/v1/keystroke `
  -H "Authorization: Bearer $API_KEY" `
  -H "Content-Type: application/json" `
  -d "@$env:TEMP\ks.json"
Start-Sleep -Seconds 1

# 6. Screenshot to read result
curl.exe -sk -H "Authorization: Bearer $API_KEY" `
  https://tinypilot.local/api/v1/screenshot -o "$env:TEMP\screen.jpg"
```

## Mouse click example

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

## Keystroke examples

```bash
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

# Win+R (Windows Run dialog)
curl -sk -X POST https://tinypilot.local/api/v1/keystroke \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"code":"KeyR","metaLeft":true}'
```
