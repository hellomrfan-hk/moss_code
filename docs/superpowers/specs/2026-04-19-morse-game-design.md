# Morse Code Digit Game — Design Spec

**Date:** 2026-04-19

## Overview

A single-file browser game (`index.html`) that helps a child practice translating Morse code into digits (0–9). No build tools, no dependencies, no server required — open the file in any browser.

---

## Screens

The app renders three screens in a single HTML page (no routing, just show/hide):

### 1. Setup Screen
- Timer selector: **15s / 45s / 60s** (radio buttons or segmented control)
- Mode selector: **Beginner / Advanced** (explained with short description)
  - Beginner: full Morse pattern shown instantly, audio plays
  - Advanced: pattern animates in sync with audio, one symbol at a time
- "Start" button — clicking Start begins the countdown immediately

### 2. Game Screen (Card Style layout)
- **Header row:** countdown timer (seconds remaining) on the left, correct count on the right
- **Card:** raised white card with subtle shadow, centered on page
  - Label: "Morse Code" in small uppercase text
  - Morse pattern: `• • — — —` style, monospace font, large and spaced
  - Input display: a single box showing the last digit pressed (or `_` when waiting)
- **Progress bar:** at the bottom of the screen, depletes as time runs out (blue → red as time gets low)
- **Feedback:** on answer, the card background flashes green (correct) or red (incorrect) for ~400ms, then immediately loads the next digit

Input is a single keypress (keyboard 0–9). No submit button needed.

### 3. Results Screen
- Headline: "X correct out of Y attempts"
- Per-digit table (only digits that appeared in the session):

  | Digit | Morse    | Result   |
  |-------|----------|----------|
  | 2     | ..---    | 2/3 ✓    |
  | 7     | --...    | 0/1 ✗    |

- "Play Again" button (returns to Setup screen)

---

## Audio

Generated via the **Web Audio API** — no audio files required.

- Dot: 100ms beep at 600 Hz
- Dash: 300ms beep at 600 Hz
- Gap between symbols: 100ms silence
- Gap between characters: not needed (one character per round)

**Beginner mode:** Audio plays immediately when the card appears. Full pattern visible from the start.

**Advanced mode:** Each symbol appears on screen as its beep plays. The player must wait for the full sequence before the input box becomes active.

---

## Digit Selection

Digits (0–9) are selected randomly each round. Weak digits (see below) appear approximately 2× more often than non-weak digits, achieved by weighted random selection.

---

## Weak Digit Tracking (localStorage)

After each completed game session, the app updates a `morseWeakDigits` key in `localStorage`.

**A digit is marked weak if:** it appeared at least once in the session AND was answered correctly less than 50% of the time.

**Data structure:**
```json
{
  "weakDigits": [2, 7, 9],
  "lastUpdated": "2026-04-19"
}
```

- Weak digits are shown 2× as often as normal digits in the next session via weighted random selection.
- A digit is removed from the weak list once the player answers it correctly ≥ 80% of the time in a single session.
- The weak digit status is shown on the Setup screen (e.g. "Practice focus: 2, 7, 9") so the player knows what to expect.

---

## Visual Style

**Clean & minimal** — light background, blue accents, no dark theme.

- Background: `#f8f9fa`
- Card background: `#ffffff` with `box-shadow`
- Primary accent: `#4dabf7` (blue)
- Correct flash: `#51cf66` (green)
- Incorrect flash: `#ff6b6b` (red)
- Font: system sans-serif for UI, monospace for Morse patterns
- Progress bar transitions from blue → orange → red as time runs out

---

## File Structure

```
index.html   ← entire app (HTML + <style> + <script>)
```

---

## Morse Code Reference (digits 0–9)

| Digit | Morse   |
|-------|---------|
| 0     | -----   |
| 1     | .----   |
| 2     | ..---   |
| 3     | ...--   |
| 4     | ....-   |
| 5     | .....   |
| 6     | -....   |
| 7     | --...   |
| 8     | ---..   |
| 9     | ----.   |

---

## Out of Scope

- Multi-player
- Letters (A–Z) — digits only
- Sound effects beyond beeps (no music, no voice)
- Leaderboards or cloud sync
