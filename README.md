# Mkraftman Marantz Volume

Custom Home Assistant Lovelace card for controlling Marantz (and Denon) AV receiver volume. Visual twin of [Mkraftman Trinnov Volume](https://github.com/mkraftman/mkraftman-trinnov-volume) and [Mkraftman Samsung Volume](https://github.com/mkraftman/mkraftman-samsung-volume), driven by a `media_player` entity from the core **Denon AVR Network Receivers** (`denonavr`) integration.

## Features

- Pill-shaped slider matching mushroom card aesthetics
- Volume shown on the receiver's own absolute 0–98 scale (or in dB, see `volume_display`)
- Mute toggle button (via `media_player.volume_mute`)
- Volume +/- buttons (1 step on the absolute scale = 1 dB)
- Optional `max` cap for the slider and buttons
- Optimistic UI updates — slider responds instantly during drag
- Throttled service calls (max 1 per 200ms) to avoid overloading HA
- Graceful handling of standby / unavailable entities

## Volume scale

The `denonavr` integration reports `volume_level = (dB + 80) / 100`, so `volume_level × 100` is exactly the number on the receiver's front panel in its default absolute mode:

| Absolute | dB     |
| -------- | ------ |
| 0        | −80 dB |
| 40       | −40 dB |
| 80       | 0 dB   |
| 98       | +18 dB |

With the integration's Telnet option enabled, the library sends volume as a whole absolute step, so half-steps can't be set from HA. The card therefore sets whole steps only, but still displays a half-step (e.g. `40.5`) if one is set from the receiver's own remote.

## Installation

### HACS

1. Add this repository as a custom repository in HACS (category: Lovelace)
2. Install "Mkraftman Marantz Volume"
3. Add the resource in your Lovelace configuration

### Manual

1. Copy `dist/mkraftman-marantz-volume.js` to `www/community/mkraftman-marantz-volume/`
2. Add the resource:
   ```yaml
   resources:
     - url: /hacsfiles/mkraftman-marantz-volume/mkraftman-marantz-volume.js
       type: module
   ```

## Configuration

```yaml
type: custom:mkraftman-marantz-volume
entity: media_player.marantz_cinema_60_series_2
```

| Option           | Required | Default    | Description                                                                 |
| ---------------- | -------- | ---------- | --------------------------------------------------------------------------- |
| `entity`         | Yes      |            | `denonavr` media player entity (volume and mute are read from its attributes) |
| `volume_display` | No       | `absolute` | `absolute` shows `40`; `relative` shows `-40.0 dB`                          |
| `max`            | No       | `98`       | Highest absolute value the slider and + button will set (1–98). The receiver's own Volume Limit setting still applies on top. |

## Layout

```
[Mute Icon]  40                  [Vol-] [Vol+]
[============= SLIDER (full width) =============]
```

## Releasing

Bump the version in `VERSION` (e.g. `1.0.1`) and push to `main`. The release workflow creates the matching `vX.Y.Z` tag and GitHub release with `dist/mkraftman-marantz-volume.js` attached. Pushing a `vX.Y.Z` tag directly also still works.
