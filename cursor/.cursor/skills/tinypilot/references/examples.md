# TinyPilot REST API Examples

## Complete session example — PowerShell (Windows)

This is the canonical pattern. Every step is a separate Shell call — one API call, then
read the result before proceeding. Never batch paste + keystroke + screenshot together.

**Step 1 — API key**
```powershell
# Create a key under System → Automation (TinyPilot Pro 3.2.0+)
$API_KEY = $env:TINYPILOT_API_KEY
if (-not $API_KEY) { throw "Set TINYPILOT_API_KEY" }
```

**Step 2 — Screenshot to verify current state before acting**
```powershell
curl.exe -sk -H "Authorization: Bearer $API_KEY" `
  https://tinypilot.local/api/v1/screenshot -o "$env:TEMP\screen.jpg"
```
→ Read the screenshot. Confirm the terminal is ready before continuing.

**Step 3 — Paste a command (with correct timing)**
```powershell
$text = "uptime"
'{"text":"' + $text + '","language":"en-US"}' | Out-File -Encoding ASCII "$env:TEMP\paste.json"
curl.exe -sk -X POST https://tinypilot.local/api/v1/paste `
  -H "Authorization: Bearer $API_KEY" `
  -H "Content-Type: application/json" `
  -d "@$env:TEMP\paste.json"
Start-Sleep -Milliseconds ($text.Length * 100 + 1000)
```
→ Wait completes here. Do NOT send Enter in this same block.

**Step 4 — Screenshot to verify the text appeared correctly**
```powershell
curl.exe -sk -H "Authorization: Bearer $API_KEY" `
  https://tinypilot.local/api/v1/screenshot -o "$env:TEMP\screen.jpg"
```
→ Read and confirm "uptime" is visible at the prompt. Only proceed if it looks right.

**Step 5 — Send Enter**
```powershell
'{"code":"Enter"}' | Out-File -Encoding ASCII "$env:TEMP\ks.json"
curl.exe -sk -X POST https://tinypilot.local/api/v1/keystroke `
  -H "Authorization: Bearer $API_KEY" `
  -H "Content-Type: application/json" `
  -d "@$env:TEMP\ks.json"
Start-Sleep -Seconds 1
```

**Step 6 — Screenshot to read the output**
```powershell
curl.exe -sk -H "Authorization: Bearer $API_KEY" `
  https://tinypilot.local/api/v1/screenshot -o "$env:TEMP\screen.jpg"
```
→ Read the result. The command output is now visible on screen.

---

## Complete session example — bash/zsh (Mac/Linux)

Same pattern, same rules. One API call per shell invocation.

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

**PowerShell:**
```powershell
$API_KEY = $env:TINYPILOT_API_KEY
```

**bash/zsh:**
```bash
API_KEY="$TINYPILOT_API_KEY"
```

### Screenshot

**PowerShell:**
```powershell
curl.exe -sk -H "Authorization: Bearer $API_KEY" `
  https://tinypilot.local/api/v1/screenshot -o "$env:TEMP\screen.jpg"
```

**bash/zsh:**
```bash
curl -sk -H "Authorization: Bearer $API_KEY" \
  https://tinypilot.local/api/v1/screenshot -o /tmp/screen.jpg
```

### Keystroke — common keys

**PowerShell:**
```powershell
# Enter
'{"code":"Enter"}' | Out-File -Encoding ASCII "$env:TEMP\ks.json"

# Ctrl+C
'{"code":"KeyC","ctrlLeft":true}' | Out-File -Encoding ASCII "$env:TEMP\ks.json"

# Cmd+Space (macOS Spotlight)
'{"code":"Space","metaLeft":true}' | Out-File -Encoding ASCII "$env:TEMP\ks.json"

# Win+R (Windows Run dialog)
'{"code":"KeyR","metaLeft":true}' | Out-File -Encoding ASCII "$env:TEMP\ks.json"

# Ctrl+Alt+T (open terminal on Linux)
'{"code":"KeyT","ctrlLeft":true,"altLeft":true}' | Out-File -Encoding ASCII "$env:TEMP\ks.json"

curl.exe -sk -X POST https://tinypilot.local/api/v1/keystroke `
  -H "Authorization: Bearer $API_KEY" `
  -H "Content-Type: application/json" `
  -d "@$env:TEMP\ks.json"
```

**bash/zsh:**
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

**PowerShell:**
```powershell
# Move → Press → Release at (x=0.5, y=0.5) — coordinates are relative (0.0–1.0)
foreach ($buttons in @(0, 1, 0)) {
    '{"buttons":' + $buttons + ',"relativeX":0.5,"relativeY":0.5,"verticalWheelDelta":0,"horizontalWheelDelta":0}' `
      | Out-File -Encoding ASCII "$env:TEMP\mouse.json"
    curl.exe -sk -X POST https://tinypilot.local/api/v1/mouseEvent `
      -H "Authorization: Bearer $API_KEY" `
      -H "Content-Type: application/json" `
      -d "@$env:TEMP\mouse.json"
    Start-Sleep -Milliseconds 50
}
```

**bash/zsh:**
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

**PowerShell:**
```powershell
curl.exe -sk -H "Authorization: Bearer $API_KEY" https://tinypilot.local/state `
  | ConvertFrom-Json | Select-Object -ExpandProperty result `
  | Select-Object -ExpandProperty source `
  | Select-Object -ExpandProperty resolution
```

**bash/zsh:**
```bash
curl -sk -H "Authorization: Bearer $API_KEY" https://tinypilot.local/state \
  | jq '.result.source.resolution'
```
