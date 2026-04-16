# RoadPass 🚧

**A crowdsourced road hazard warning system built on [openpilot](https://github.com/commaai/openpilot) and the comma four device.**

Built at **Comma Hack 6** by [Swetha Shankar](https://github.com/SwethaShankarV) & [John Adams](https://github.com/jpaoad31).

---

## The Problem

Road hazards — potholes, debris, surface damage — appear without warning. GPS maps have no awareness of them, every driver encounters them fresh, and there's no mechanism for one driver's experience to protect the next. ~$3B in annual vehicle damage in the US alone is attributable to potholes.

## What RoadPass Does

RoadPass turns every openpilot-equipped car into a hazard sensor and a hazard receiver.

**Detect → Share → Warn**

1. **Detect**: The device monitors accelerometer data for sudden jolts (|>4 m/s², >10 m/s² jerk|). When a bump is detected, a popup asks the driver to confirm. Confidence builds automatically as more cars report the same location.

2. **Share**: Confirmed hazard events are POSTed to the RoadPass backend with GPS coordinates, speed, bearing, and openpilot vehicle state. A follow-up PATCH updates confidence as additional reports come in. Returning cars silently auto-confirm without requiring driver input.

3. **Warn**: When an openpilot car approaches a known hazard location, an amber alert card appears on the comma four screen: *"Hazard ahead — Xm away"* with a confidence level (High / Medium / Low). Longitudinal slow-down on approach is planned (WIP).

---

## What We Shipped

- Bump detection from accelerometer/gyro sensor fusion (device side)
- Two-phase hazard reporting: `POST` (new hazard) + `PATCH` (confidence update) to live server
- Background GPS-aware hazard fetcher with smart refresh
- Amber "Hazard ahead" warning card rendered on comma 4 mini screen
- Crowd confidence scoring: High / Medium / Low tier based on report count
- Returning car auto-confirm (silent re-reports for repeat passes)
- Live hazard map at [roadpass.jpadam.xyz](http://roadpass.jpadam.xyz)

---

## Architecture

```
comma four device (openpilot fork)
    │
    ├── Bump detector (sorzSane/algo sensor fusion)
    ├── Hazard reporter → POST/PATCH → RoadPass API
    ├── Hazard fetcher ← GET ← RoadPass API
    └── UI: amber warning card on comma 4 screen

RoadPass Backend (github.com/jpaoad31/RoadPass)
    ├── REST API: roadpass.jpadam.xyz
    ├── Stores hazard events (GPS, speed, bearing, openpilot state)
    ├── Computes crowd confidence score
    └── Serves live hazard map
```

---

## What's Next

- Voice responses to hazard prompts (comma 4 has a speaker)
- Tuning & UI improvements — confidence sensitivity is too high and needs a calibration pass
- Expanded hazard types: cones, logs, obstacles in adjacent lanes (visual model)
- Server-side hazard expiry: auto-evict hazards older than N days without fresh confirms

---

## Based On

- [openpilot](https://github.com/commaai/openpilot) by Comma AI — MIT License
- comma four device
