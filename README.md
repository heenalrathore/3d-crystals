# LUMINA — AI Light to Crystal Generator

Lumina is a browser-based interactive 3D experience that turns a real-world light source into a glowing crystal you can shape and control with your hands — no mouse, no keyboard. Point a bright light at your webcam, and a swirling particle cloud condenses into a colored crystal whose hue is sampled from the light itself. From there, you steer the crystal entirely through hand gestures.

The entire app lives in a single `index.html` file with no build step.

## Demo flow

The experience runs through three workflow steps shown at the bottom of the screen:

1. **Provide Light Source** — A cloud of 15,000 particles swirls in space while the camera scans for a bright light. Shine a phone flashlight, lamp, or any strong light toward the webcam.
2. **Crystal Formation** — Once a strong light is detected, the particles tween into a crystalline shape. The crystal's base color is sampled and saturated from the detected light, and a glass-like core and wireframe shell fade in.
3. **Interact & Rotate** — Use your hand to rotate the crystal in 3D, pinch to recolor it, and hover over the touchless **Save** button to capture a PNG.

## Controls

All interaction is touchless, powered by webcam hand tracking:

| Gesture | Action |
| --- | --- |
| Move hand left/right/up/down | Rotate the crystal |
| Pinch (thumb + index finger) | Randomize the crystal color |
| Hover over the 📸 **Save** button (~1.5s) | Download the current crystal as a PNG image |

The on-screen status panel reports **Camera Status**, **Hand Tracking**, and **Light Source** intensity in real time. A small preview canvas in the bottom-left corner shows the camera feed used for light detection (hover to make it fully visible).

## How it works

- **3D rendering** — [Three.js](https://threejs.org) (r128) renders the particle system, a physical glass-like `MeshPhysicalMaterial` core, and a wireframe shell, with ACES filmic tone mapping and additive-blended glow particles.
- **Animation** — [Tween.js](https://github.com/tweenjs/tween.js) drives the particle-to-crystal formation and color transitions.
- **Hand tracking** — [MediaPipe Hands](https://google.github.io/mediapipe/solutions/hands.html) detects a single hand and its 21 landmarks each frame. Palm position maps to rotation; thumb/index distance detects a pinch.
- **Light detection** — Each camera frame is drawn to a small analysis canvas, where per-pixel perceptual brightness is computed. When a sufficiently bright region is found, its average color seeds the crystal's palette.
- **Image export** — The WebGL canvas uses `preserveDrawingBuffer` so a frame can be exported to a downloadable PNG via `toDataURL`.

## Running locally

Because the app requires camera access, browsers will only grant it over `https://` or `http://localhost` (not `file://`). Serve the folder with any static server:

```bash
# Python 3
python -m http.server 8000

# or Node
npx serve .
```

Then open `http://localhost:8000` and grant camera permission when prompted.

> **Tip:** A dark room with a single strong, focused light source (e.g. a phone flashlight) produces the cleanest detection and most vivid crystal color.

## Requirements

- A modern desktop browser with **WebGL** and **getUserMedia** (camera) support — Chrome or Edge recommended.
- A webcam.
- An internet connection — Three.js, Tween.js, and MediaPipe are loaded from CDNs at runtime.

## Project structure

```
3d-crystals/
└── index.html   # Entire app: markup, styles, and logic
```

## Tech stack

- Three.js r128 — 3D scene, particles, materials
- Tween.js 18.6.4 — animation/easing
- MediaPipe Hands — webcam hand-landmark tracking
- Vanilla HTML / CSS / JavaScript — no build tooling
