# Aether — A Hand-Reactive Particle Sphere

A 3000-particle sphere you can move, rotate, scale, and blow apart with your bare hands. No mouse, no keyboard — just a webcam and your fingers.

Built in a single HTML file. No frameworks. No build step. Open it in a browser and it just works.
Live demo: https://portfolio-webiste-fawn.vercel.app
![demo gif or screenshot here](./preview.gif)
---
## Why I built this
One Sunday i was fed up of playing routine video games, and felt like to make something out of which i can play and interact with my device and this thought came up from the Film- Iron Man where he builds a new element from his fathers global design and i felt like to build something like that
I wanted to see how far I could push a browser without reaching for React, Three.js scene graphs, or a bundler. The goal was something that *feels* alive — where the boundary between yme and the screen dissolves a little. Hand tracking felt like the right way in.
It also became an excuse to learn things I'd been putting off: real-time computer vision pipelines, particle physics, and writing performant Canvas code that doesn't melt a laptop.
---
## What it does
| Gesture | Effect |
|---|---|
| One hand moving | Sphere follows your palm and rotates with it |
| Pinch (thumb + index) and hold | A charge ring fills over ~1.2s |
| Release the pinch | Particles explode outward, then spring back into place |
| Two hands, spread apart | Sphere grows |
| Two hands, brought together | Sphere shrinks |
| Number of fingers extended | Particle color shifts across an 11-stop palette |
| "Camera Background" button | Toggles the mirrored webcam as a fullscreen backdrop
## How it works
Every frame:
1. MediaPipe reads the video and returns up to 2 hands, each with 21 3D landmarks.
2. A small interpretation layer figures out *what the hands are doing* — pinching, spreading, how many fingers are up.
3. That drives a state object (position, radius, rotation, hue, charge level).
4. The renderer projects 3000 3D points onto 2D canvas with depth-aware size and alpha.
### Particle distribution — why Fibonacci
Random points on a sphere clump. They look noisy. The **Fibonacci sphere** algorithm distributes points using the golden angle, giving you an even, organic-looking spread with a single closed-form loop — no rejection sampling, no relaxation passes.

```js
const phi = Math.PI * (Math.sqrt(5) - 1); // golden angle
for (let i = 0; i < N; i++) {
  const y = 1 - (i / (N - 1)) * 2;
  const r = Math.sqrt(1 - y*y);
  const t = phi * i;
  particles.push({ x: Math.cos(t)*r, y, z: Math.sin(t)*r });
}
### The pipeline
