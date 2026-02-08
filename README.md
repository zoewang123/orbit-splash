# Orbit Splash

**Orbit Splash** is a fully playable, self‑contained mobile browser game delivered as a single HTML file under 15 KB. It’s a two‑ring survival challenge—tap to swap orbits, dodge hazards, and ride escalating patterns. A second mode, **Music Go!**, lets players upload a song and experience rhythm‑locked visuals.

## Highlights
- **Two modes:** Endless survival and Music Go! (audio‑reactive)
- **Dynamic geometry:** Rings morph with smooth trigonometric harmonics
- **Shield pickups:** Green orbs grant temporary bullet shields
- **Neon canvas art:** Spline rings, glow, particles, and subtle shake
- **Mobile‑first UX:** One‑tap controls, lightweight, no dependencies

## How To Play
- **Endless:** Tap to switch rings, avoid red hazards, collect green orbs for temporary shields.
- **Music Go!:** Upload a song, preview the orbit reacting to the beat, then tap to start. The run ends when the song ends.

## Technical Design
### Wave Generation (Endless)
The rings are not perfect circles. Each ring is defined by a radius function:

```
R(a) = r * (1 + waveA * waveV(a, p))
```

- `waveV(a, p)` blends two even cosine harmonics to create a symmetric, closed shape:

```
waveV(a, p) = (1-m)*cos((a+p)*k1) + m*cos((a+p)*k2)
```

- `k1, k2` are even integers (2, 4, 6, 8), producing clean “flower‑like” patterns.
- `m` smoothly interpolates between harmonics for seamless transitions.
- The curve is drawn using a quadratic spline through sampled midpoints to avoid seams and keep smooth derivatives.
- The player dot uses the same radius function, so it stays perfectly on the moving track.

### Shift Mechanism
After a set survival time, the inner and outer ring radii drift and swap. This is implemented by interpolating between two base radii over time (`ringShift`), which forces players to adjust timing as the geometry changes.

### Music Go! Beat Detection (No AI / No External Libraries)
We perform a lightweight, offline beat analysis on the first ~15 seconds:

1. **Offline filtering** with low‑pass + high‑pass to isolate kick/bass energy.
2. **Peak picking** by splitting the filtered buffer into ~0.5s windows and extracting the strongest peaks.
3. **Tempo grouping** by converting peak intervals to BPM, folding into a target range, and choosing the most frequent tempo.
4. **Beat offset alignment** to synchronize the visual phase with the first consistent beat.

Runtime visuals then lock to the detected beat period and offset, while still reacting to live energy for expressive amplitude changes.

## Project Structure
- `index.html` – The entire game (HTML + CSS + JS).

## Running
Just open `index.html` in any modern browser (desktop or mobile). No build step required.

## Credits
Beat analysis is adapted from the BeatDetect.js method (peak‑picking + interval grouping + offset), re‑implemented inline to stay within the size target.
