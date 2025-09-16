# claude.md

## Overview
Build a single-file webpage (`index.html`) that displays **CME E-mini S&P 500 futures (ES) active trading session time left**, along with the next open/close times.
The page should include a **progress bar** that fills across the active session, with countdowns and status messages.

---

## Requirements

### 1. Trading-hours logic
- Support two selectable modes:
  - **Globex (Electronic Session):**
    - Sun–Fri 18:00 → 17:00 (America/Chicago)
    - Daily maintenance halt: 17:00–18:00 CT
    - Weekend closure: Fri 17:00 CT → Sun 18:00 CT
  - **RTH (Regular Trading Hours):**
    - Mon–Fri 08:30 → 15:00 (America/Chicago)
- Default = Globex. Provide toggle buttons to switch.
- Support US holidays / early closes via a configurable JSON array.

### 2. Timezone handling
- Compute logic in **America/Chicago**, display in user's **local timezone**.
- Detect timezone automatically via `Intl.DateTimeFormat().resolvedOptions().timeZone`.
- Support `?tz=Zone/Name` URL override.
- Handle DST transitions correctly.

### 3. UI/UX
- Clean, responsive layout (plain HTML/CSS/vanilla JS).
- **Top status line**:
  - "Open — closes in HH:MM:SS"
  - "Closed — opens in HH:MM:SS"
  - "Maintenance Halt — reopens in HH:MM:SS"
- **Progress bar**:
  - Fills from session open → close.
  - Show open/close labels at ends.
  - Show % complete if open.
- **Details list** under the bar:
  - Session open / close
  - Next open / close
  - Mode
  - Display timezone
- Update countdowns every second.

### 4. Edge cases
- Maintenance window (Globex 17:00–18:00 CT).
- Weekend gap.
- Cross-midnight sessions in local timezones.
- Early closes from JSON config.
- Holiday closures.

### 5. Code quality
- Single `index.html`, inline CSS/JS, no external libs.
- Suggested helper functions:
  - `getNow(tz)`
  - `toZoned(date, tz)`
  - `nextGlobexSessionWindow()`
  - `nextRthSessionWindow()`
  - `isMaintenance(nowCT)`
  - `getHolidayOverride(dateCT)`
  - `formatTime(dt, tz)`
  - `computeProgress(nowCT, window)`
- Include `HOLIDAYS` JSON with sample entries.
- Accessible: semantic HTML, ARIA attributes on progress bar.
- Light/dark friendly.

---

## Acceptance Criteria
- Toggle Globex ↔ RTH updates instantly.
- Friday 18:30 CT shows **Closed**, next open = Sunday 18:00 CT.
- Monday 09:00 CT (RTH mode) shows **Open**, ~30 min progress.
- Weekday 17:15 CT (Globex mode) shows **Maintenance Halt**, reopens in 45 min.
- `?tz=Asia/Singapore` updates all times to SGT.
- Holiday in config → "Closed (holiday)", next open computed correctly.

---

## Nice-to-have
- Persist last mode toggle in `localStorage`.
- Tooltip on progress bar with exact times.
- Inline unit tests for session logic functions.