# TinyPilot REST API Automation Playbook

## Overview
Control a remote machine through the TinyPilot REST API with screenshot-based verification after each action.

## What's Needed From User
- TinyPilot URL (default `https://tinypilot.local` if not provided)
- Desired task outcome on the target machine

## Procedure
1. Confirm TinyPilot URL and objective.
2. Authenticate using `POST /api/v1/auth` and capture bearer token.
3. Take an initial screenshot with `GET /api/v1/screenshot`.
4. Choose one action (keystroke, paste, or mouse event) based on current screen.
5. Execute the action.
6. Wait long enough for UI response (1-2s standard, 3-8s app launch).
7. Take a verification screenshot.
8. Repeat steps 4-7 until objective is complete.
9. Return a concise action log and final verification evidence.

## Specifications
- Every control action is followed by a verification screenshot.
- Token is kept in memory and never written to disk.
- Mouse coordinates use current display size (`GET /state`) before click actions.

## Advice and Pointers
- Prefer keyboard navigation and paste over mouse when practical.
- Use paste for full text entry; use keystrokes for modifiers and navigation.
- For click reliability, use move -> press -> release.

## Forbidden Actions
- Do not store auth tokens in files.
- Do not assume screen state without a fresh screenshot.

## Required from User
- Clarification if multiple TinyPilot devices are in scope.

## Constraints
Automation requires a valid TinyPilot Automation License.
