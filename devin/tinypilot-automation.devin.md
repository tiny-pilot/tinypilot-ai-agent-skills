# TinyPilot REST API Automation Playbook

## Overview

Control a remote machine through the TinyPilot REST API with screenshot-based verification after each action. Supports keyboard, mouse, paste, and screenshot operations via a KVM device.

## What's Needed From User

- TinyPilot URL (default `https://tinypilot.local` if not provided).
- Desired task outcome on the target machine.
- Clarification if multiple TinyPilot devices are in scope.

## Procedure

1. Confirm TinyPilot URL and objective.
2. Authenticate using `POST /api/v1/auth` and capture bearer token. Do not include an `Origin` header.
3. Take an initial screenshot with `GET /api/v1/screenshot` (`200` = JPEG image, `204` = no video signal).
4. Choose one action (keystroke, paste, or mouse event) based on current screen. Prefer keyboard/terminal over mouse/GUI when possible.
5. Execute the action:
   - **Keystroke:** `POST /api/v1/keystroke` with `code` field ([KeyboardEvent.code](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code) string, e.g. `"Enter"`, `"KeyT"`, `"Space"`). Optional modifiers: `ctrlLeft`, `altLeft`, `shiftLeft`, `metaLeft`, etc.
   - **Paste:** `POST /api/v1/paste` with `text` and `language` (e.g. `"en-US"`). After the call returns, **wait `(100ms × character count) + 1000ms`** before sending any keystroke — the API returns immediately but characters are still being replayed.
   - **Mouse:** `POST /api/v1/mouseEvent` with `buttons` (0=none, 1=left, 2=right), `relativeX`/`relativeY` (0.0–1.0), `verticalWheelDelta`, `horizontalWheelDelta`. For clicks: send move (buttons=0) → press (buttons=1) → release (buttons=0).
6. Wait for UI response (1-2s standard, 3-8s app launch).
7. Take a verification screenshot.
8. If the action did not produce the expected result, **do not retry the same approach** — try something meaningfully different (a shorter command, a different tool, or mouse/GUI navigation as a fallback).
9. Repeat steps 4-8 until objective is complete.
10. Return a concise action log and final verification evidence.

## Specifications

- Every control action is followed by a verification screenshot — **Screenshot → Act → Verify**, no exceptions.
- One paste OR one keystroke per step; never batch paste + keystroke + screenshot together.
- Token is kept in memory and never written to disk.
- Mouse coordinates use current display size from `GET /state` (`result.source.resolution.width/height`) before click actions.
- In PowerShell use `curl.exe` (not `curl`), and write JSON bodies to a temp file (`-d @path`).
- Common key codes: `Enter`, `Space`, `Escape`, `Tab`, `Backspace`, `Delete`, `ArrowUp/Down/Left/Right`, `KeyA`–`KeyZ`, `Digit0`–`Digit9`, `F1`–`F12`.

## Advice and Pointers

- Prefer keyboard navigation and paste over mouse when practical.
- Use paste for full text entry; use keystrokes for modifiers and navigation.
- Keep paste payloads short — one command at a time. Chaining can cause multi-line input mode.
- Chain commands with `&&` rather than `;` where possible, or paste and execute one command at a time.
- For click reliability, always use the three-event sequence: move → press → release.
- Add delays after actions: 1-2s for normal UI, 3-8s for app launches or heavy operations.
- For the paste timing wait: `(100ms × character count) + 1000ms`. Sending Enter early splits the command mid-paste.

## Forbidden Actions

- Do not store auth tokens in files or write them to disk.
- Do not assume screen state without a fresh screenshot.
- Do not send Enter immediately after a long paste — always wait for the paste timing delay.
- Do not retry the same failing approach — reassess and try a different strategy.
- Do not batch multiple actions into a single step without verification screenshots between them.

## Constraints

Automation requires a valid TinyPilot Automation License.

## Endpoint Quick Reference

| Method | Path | Purpose |
|--------|------|---------|
| POST | `/api/v1/auth` | Returns `{"token":"<uuid>"}` |
| GET | `/api/v1/screenshot` | Current display as JPEG |
| POST | `/api/v1/keystroke` | Single key press/release |
| POST | `/api/v1/mouseEvent` | Cursor move/click/wheel |
| POST | `/api/v1/paste` | Text input to remote |
| GET | `/state` | Display resolution (unofficial) |
