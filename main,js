/* ============================================================
   Gesture Controlled 3D Particles — Main Script
   Depends on: Three.js r128, MediaPipe Hands, MediaPipe Camera Utils
   ============================================================ */

'use strict';

// ── Config ──────────────────────────────────────────────────
const PARTICLE_COUNT = 15000;
const MORPH_SPEED    = 0.07;   // Lerp factor per frame toward target shape

// ── Three.js Setup ──────────────────────────────────────────
const container = document.getElementById('canvas-container');

const scene = new THREE.Scene();
scene.fog = new THREE.FogExp2(0x000000, 0.018);

const camera = new THREE.PerspectiveCamera(75, innerWidth / innerHeight, 0.1, 1000);
camera.position.z = 30;

const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
renderer.setSize(innerWidth, innerHeight);
renderer.setPixelRatio(devicePixelRatio);
container.appendChild(renderer.domElement);

// ── Particle Geometry ────────────────────────────────────────
const geometry       = new THREE.BufferGeometry();
const positions      = new Float32Array(PARTICLE_COUNT * 3);
const targetPositions = new Float32Array(PARTICLE_COUNT * 3);
const colors         = new Float32Array(PARTICLE_COUNT * 3);
const colorObj       = new THREE.Color();

for (let i = 0; i < PARTICLE_COUNT; i++) {
  positions[i * 3]     = (Math.random() - 0.5) * 50;
  positions[i * 3 + 1] = (Math.random() - 0.5) * 50;
  positions[i * 3 + 2] = (Math.random() - 0.5) * 50;
  targetPositions[i * 3]     = positions[i * 3];
  targetPositions[i * 3 + 1] = positions[i * 3 + 1];
  targetPositions[i * 3 + 2] = positions[i * 3 + 2];
  colorObj.setHSL(Math.random(), 0.8, 0.5);
  colors[i * 3]     = colorObj.r;
  colors[i * 3 + 1] = colorObj.g;
  colors[i * 3 + 2] = colorObj.b;
}

geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
geometry.setAttribute('color',    new THREE.BufferAttribute(colors, 3));

const material = new THREE.PointsMaterial({
  size: 0.14,
  vertexColors: true,
  blending: THREE.AdditiveBlending,
  depthWrite: false,
  transparent: true,
  opacity: 0.85,
});
const particles = new THREE.Points(geometry, material);
scene.add(particles);

// ── Shape Functions (Parametric) ─────────────────────────────
const shapes = {
  sphere: i => {
    const r   = 10;
    const phi = Math.acos(-1 + (2 * i) / PARTICLE_COUNT);
    const theta = Math.sqrt(PARTICLE_COUNT * Math.PI) * phi;
    return {
      x: r * Math.cos(theta) * Math.sin(phi),
      y: r * Math.sin(theta) * Math.sin(phi),
      z: r * Math.cos(phi),
    };
  },

  heart: i => {
    const t = (i / PARTICLE_COUNT) * Math.PI * 40;
    const x = 16 * Math.pow(Math.sin(t), 3);
    const y = 13 * Math.cos(t) - 5 * Math.cos(2 * t) - 2 * Math.cos(3 * t) - Math.cos(4 * t);
    return { x: x * 0.5, y: y * 0.5, z: (Math.random() - 0.5) * 4 };
  },

  flower: i => {
    const u = (i / PARTICLE_COUNT) * Math.PI * 2;
    const r = 8 * Math.cos(4 * u);
    return { x: r * Math.cos(u), y: r * Math.sin(u), z: (Math.random() - 0.5) * 5 };
  },

  saturn: i => {
    if (i < PARTICLE_COUNT * 0.7) {
      // Planet body
      const r     = 6;
      const phi   = Math.acos(-1 + (2 * i) / (PARTICLE_COUNT * 0.7));
      const theta = Math.sqrt(PARTICLE_COUNT * 0.7 * Math.PI) * phi;
      return {
        x: r * Math.cos(theta) * Math.sin(phi),
        y: r * Math.sin(theta) * Math.sin(phi),
        z: r * Math.cos(phi),
      };
    } else {
      // Rings
      const angle = (i / (PARTICLE_COUNT * 0.3)) * Math.PI * 20;
      const r     = 9 + Math.random() * 5;
      return { x: r * Math.cos(angle), y: (Math.random() - 0.5) * 0.6, z: r * Math.sin(angle) };
    }
  },

  galaxy: i => {
    const idx    = i / PARTICLE_COUNT;
    const angle  = idx * Math.PI * 10;
    const radius = idx * 15;
    const spread = (Math.random() - 0.5) * 2.5;
    const arm    = i % 3;
    const offset = (Math.PI * 2 / 3) * arm;
    return {
      x: (radius + spread) * Math.cos(angle + offset),
      y: (Math.random() - 0.5) * (radius * 0.25),
      z: (radius + spread) * Math.sin(angle + offset),
    };
  },

  dna: i => {
    const t      = (i / PARTICLE_COUNT) * Math.PI * 16;
    const r      = 5;
    const offset = (i % 2) * Math.PI; // Two strands, 180° apart
    return {
      x: r * Math.cos(t + offset),
      y: (i / PARTICLE_COUNT) * 20 - 10,
      z: r * Math.sin(t + offset),
    };
  },

  torusKnot: i => {
    const t = (i / PARTICLE_COUNT) * Math.PI * 2;
    const p = 2, q = 3;
    const r = Math.cos(q * t) + 2;
    return {
      x: r * Math.cos(p * t) * 4,
      y: r * Math.sin(p * t) * 4,
      z: -Math.sin(q * t) * 4,
    };
  },

  butterfly: i => {
    const t = (i / PARTICLE_COUNT) * Math.PI * 40;
    const r = Math.exp(Math.cos(t)) - 2 * Math.cos(4 * t) - Math.pow(Math.sin(t / 12), 5);
    return {
      x: r * Math.cos(t) * 6,
      y: r * Math.sin(t) * 6,
      z: (Math.random() - 0.5) * 3,
    };
  },

  mobius: i => {
    const u = (i / PARTICLE_COUNT) * Math.PI * 4;
    const v = ((i % 100) / 100 - 0.5) * 2;
    const r = 8 + v * Math.cos(u / 2);
    return {
      x: r * Math.cos(u),
      y: r * Math.sin(u),
      z: v * Math.sin(u / 2),
    };
  },

  supernova: i => {
    const phi   = Math.acos(-1 + (2 * i) / PARTICLE_COUNT);
    const theta = Math.sqrt(PARTICLE_COUNT * Math.PI) * phi;
    const burst = 0.5 + Math.random() * 9.5;
    const wave  = Math.sin(phi * 8 + theta * 0.5) * 2;
    const r     = burst + wave;
    return {
      x: r * Math.cos(theta) * Math.sin(phi),
      y: r * Math.sin(theta) * Math.sin(phi),
      z: r * Math.cos(phi),
    };
  },

  klein: i => {
    const u = (i / PARTICLE_COUNT) * Math.PI * 2;
    const v = ((i * 7) / PARTICLE_COUNT) * Math.PI * 2;
    let x, z;
    if (u < Math.PI) {
      x = 3 * Math.cos(u) * (1 + Math.sin(u)) + (2 * (1 - Math.cos(u) / 2)) * Math.cos(u) * Math.cos(v);
      z = -2 * (1 - Math.cos(u) / 2) * Math.sin(v);
    } else {
      x = 3 * Math.cos(u) * (1 + Math.sin(u)) + (2 * (1 - Math.cos(u) / 2)) * Math.cos(v + Math.PI);
      z =  2 * (1 - Math.cos(u) / 2) * Math.sin(v);
    }
    const y = -2 * (1 - Math.cos(u) / 2) * Math.sin(u);
    return { x: x * 0.8, y: y * 0.8, z: z * 0.8 };
  },
};

const shapeNames  = ['sphere','heart','flower','saturn','galaxy','dna','torusKnot','butterfly','mobius','supernova','klein'];
const shapeLabels = ['Sphere','Heart','Flower','Saturn','Galaxy','DNA Helix','Torus Knot','Butterfly','Möbius Ring','Supernova','Klein Bottle'];

// ── Shape State ──────────────────────────────────────────────
let currentShapeIdx = 0;

function setTargetShape(idx) {
  if (idx < 0 || idx >= shapeNames.length) return;
  currentShapeIdx = idx;
  const formula = shapes[shapeNames[idx]];
  for (let i = 0; i < PARTICLE_COUNT; i++) {
    const pos = formula(i);
    targetPositions[i * 3]     = pos.x;
    targetPositions[i * 3 + 1] = pos.y;
    targetPositions[i * 3 + 2] = pos.z;
  }
  document.getElementById('s-shape').textContent = shapeLabels[idx];
}

setTargetShape(0);

// ── Scene Control State ──────────────────────────────────────
// Right hand  → shape (finger count)
// Left hand X → hue, Y → rotation speed, pinch → camera zoom

let currentHue = 0.5;
let targetHue  = 0.5;
let rotSpeed   = 0.002;   // radians per frame
let cameraZoom = 30;      // current camera Z (smoothed)
let targetZoom = 30;      // desired camera Z

// ── Animation Loop ───────────────────────────────────────────
function animate() {
  requestAnimationFrame(animate);

  const pos = particles.geometry.attributes.position.array;
  const col = particles.geometry.attributes.color.array;

  // Smooth hue toward target
  currentHue += (targetHue - currentHue) * 0.05;

  // Rotate particle system
  particles.rotation.y += rotSpeed;
  particles.rotation.z += rotSpeed * 0.4;

  // Smooth camera zoom
  cameraZoom += (targetZoom - cameraZoom) * 0.08;
  camera.position.z = cameraZoom;

  const t = Date.now() * 0.002;

  for (let i = 0; i < PARTICLE_COUNT; i++) {
    const px = i * 3, py = i * 3 + 1, pz = i * 3 + 2;

    // Lerp toward target shape
    pos[px] += (targetPositions[px] - pos[px]) * MORPH_SPEED;
    pos[py] += (targetPositions[py] - pos[py]) * MORPH_SPEED;
    pos[pz] += (targetPositions[pz] - pos[pz]) * MORPH_SPEED;

    // Pulsing colour
    const pulse = 0.55 + 0.45 * Math.sin(t + i * 0.001);
    colorObj.setHSL((currentHue + (i / PARTICLE_COUNT) * 0.12) % 1, 0.85, 0.5);
    col[px] = colorObj.r * pulse;
    col[py] = colorObj.g * pulse;
    col[pz] = colorObj.b * pulse;
  }

  particles.geometry.attributes.position.needsUpdate = true;
  particles.geometry.attributes.color.needsUpdate    = true;
  renderer.render(scene, camera);
}

animate();

// ── MediaPipe Hand Tracking ───────────────────────────────────
const videoElement = document.getElementById('video-element');

/**
 * Count extended fingers on a hand.
 * Checks 4 fingers by tip-vs-pip Y, thumb by X spread.
 * @param {Array} hand - MediaPipe landmark array
 * @returns {number} 0–5
 */
function countFingers(hand) {
  const tips = [8, 12, 16, 20];
  const pips = [6, 10, 14, 18];
  let count  = 0;
  for (let f = 0; f < 4; f++) {
    if (hand[tips[f]].y < hand[pips[f]].y) count++;
  }
  // Thumb: extended when tip is far from base knuckle on X axis
  if (Math.abs(hand[4].x - hand[2].x) > 0.06) count++;
  return count;
}

/**
 * Get normalised pinch distance between thumb tip and index tip.
 * @param {Array} hand - MediaPipe landmark array
 * @returns {number} roughly 0.02 (closed) to 0.2 (open)
 */
function getPinchDistance(hand) {
  const t   = hand[4];
  const idx = hand[8];
  return Math.sqrt((t.x - idx.x) ** 2 + (t.y - idx.y) ** 2);
}

/**
 * MediaPipe results callback — called every camera frame.
 * Assigns left/right hand roles and updates control state.
 */
function onResults(results) {
  document.getElementById('loading').style.display = 'none';

  const handsData = results.multiHandLandmarks || [];
  const labels    = results.multiHandedness    || [];

  // MediaPipe flips handedness because the feed is mirrored
  let leftHand  = null;
  let rightHand = null;
  for (let i = 0; i < handsData.length; i++) {
    const label = labels[i]?.label;
    if (label === 'Left')  rightHand = handsData[i];
    if (label === 'Right') leftHand  = handsData[i];
  }

  // Right hand → shape (finger count maps to shape index)
  if (rightHand) {
    const fingers = countFingers(rightHand);
    document.getElementById('s-fingers').textContent = fingers;
    setTargetShape(Math.min(fingers, shapeNames.length - 1));
  } else {
    document.getElementById('s-fingers').textContent = '—';
  }

  // Left hand → colour, rotation speed, zoom
  if (leftHand) {
    // X position (0→1 left-to-right on screen) → hue (inverted for mirrored feed)
    const hx = 1.0 - leftHand[9].x;
    targetHue = hx;

    // Y position (0 = top, 1 = bottom) → rotation speed
    const hy = leftHand[9].y;
    rotSpeed = 0.0005 + (1 - hy) * 0.008;

    // Pinch distance → camera zoom
    const pinch = getPinchDistance(leftHand);
    targetZoom = Math.max(10, Math.min(60, 30 + (0.12 - pinch) * 250));

    document.getElementById('s-hue').textContent  = Math.round(hx * 360) + '°';
    document.getElementById('s-rot').textContent  = (rotSpeed * 1000).toFixed(1) + 'x';
    document.getElementById('s-zoom').textContent = Math.round(targetZoom) + 'u';
  } else {
    rotSpeed = 0.002; // Reset to default when left hand leaves frame
    document.getElementById('s-hue').textContent  = '—';
    document.getElementById('s-rot').textContent  = '—';
    document.getElementById('s-zoom').textContent = '—';
  }
}

// MediaPipe Hands instance
const hands = new Hands({
  locateFile: f => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${f}`,
});
hands.setOptions({
  maxNumHands:          2,
  modelComplexity:      1,
  minDetectionConfidence: 0.6,
  minTrackingConfidence:  0.5,
});
hands.onResults(onResults);

// Camera feed
const cam = new Camera(videoElement, {
  onFrame: async () => { await hands.send({ image: videoElement }); },
  width:  640,
  height: 480,
});
cam.start();

// ── Resize Handler ────────────────────────────────────────────
window.addEventListener('resize', () => {
  camera.aspect = innerWidth / innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth, innerHeight);
});
