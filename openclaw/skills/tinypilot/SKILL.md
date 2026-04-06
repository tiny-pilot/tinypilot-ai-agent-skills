---
name: tinypilot
description: Control a remote computer using the TinyPilot REST API for screenshots, keyboard input, mouse actions, and paste automation.
metadata:
  {"openclaw":{"requires":{"bins":["curl"]},"os":["darwin","linux"]}}
---

# TinyPilot Automation (OpenClaw)

Automation requires a valid TinyPilot Automation License.

## Trigger

Use this skill when a user requests TinyPilot-driven remote control or KVM automation.

## Procedure

1. Determine TinyPilot host URL (default `https://tinypilot.local`).
2. Authenticate via `POST /api/v1/auth`.
3. Capture screenshot before each action.
4. Perform one action (keystroke, mouse, or paste).
5. Wait and verify with a new screenshot.

## Reliability rules

- Do not write tokens to disk.
- Use paste for text and keystrokes for shortcuts.
- Wait for paste completion before Enter.
- For mouse clicks, use move -> press -> release.

## References

- [API reference](references/api-reference.md)
- [Examples](references/examples.md)
