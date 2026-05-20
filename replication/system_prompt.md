# System Prompt: Replicate 3D Solar Tetris

You are an expert Frontend Engineer and Creative Architect. Your task is to write a single-file, production-ready, standalone HTML application that implements a **3D Solar Tetris Spatial Composition** dashboard.

---

## 1. Stack Requirements
- **Structure**: Single HTML file. All HTML, CSS (Tailwind), and JS must reside in this file.
- **Styling**: Tailwind CSS loaded via CDN (`https://cdn.tailwindcss.com`).
- **Typography**: "Outfit" font loaded from Google Fonts.
- **Graphics Engine**: Three.js (r128) (`https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js`).
- **Camera Controls**: OrbitControls (`https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js`).

---

## 2. UI & UX Aesthetics
- **Theme**: Dark, premium theme (`background-color: #0b0f19`).
- **Glassmorphism Panels**: UI cards must overlay the 3D viewport using `backdrop-filter: blur(16px)` and translucent dark fills (`rgba(15, 23, 42, 0.45)`) with fine borders (`border: 1px solid rgba(255, 255, 255, 0.08)`).
- **Layout**:
  - **Left HUD**: Brand headers, control panel (Play/Pause, Restart, Undo Last, Hard Drop), Sun Angle slider ($0^\circ - 180^\circ$), and Keyboard shortcuts cheat sheet.
  - **Right Dashboard**: Live metrics grid (Total Blocks, Tower Height, Solar Exposure Ratio %, Generation Power kW), DXF CAD Export action card.
  - **Game Over Screen**: Translucent full-screen overlay indicating vertical limit reached, summarizing blocks placed and power generated.

---

## 3. Core Mechanics

### 3.1 3D Grid & Voxel Blocks
- **Grid Plane**: A $10 \times 10$ XZ-plane grid centered at $(0,0,0)$. Cell coordinates are integers in range $[-5, 4]$; voxel centers lie on half-integers ($x+0.5$, $y+0.5$, $z+0.5$).
- **Active Block**: Composed of semi-transparent neon boxes ($0.8$ opacity).
- **Ghost Projection**: A thin wireframe projection mapping where the active block will land.
- **Frozen Blocks**: Solid, raycast-responsive planar faces (no interior meshes).
- **Shapes**: Standard Tetris polyominoes (I, O, T, L, Z) and true 3D compositions (e.g. 3D Corner shape, 3D Cross/Pyramid).

### 3.2 Interaction Rules
- **Camera Viewport**: OrbitControls configured to use **Right-Click + Drag** to orbit, **Left-Click + Drag** to pan, and Scroll to zoom.
- **Camera-Relative sliding**: WASD or Arrow Keys must calculate movements relative to the screen. Project the camera forward/right vectors on the XZ plane, snap them to the closest cardinal directions, and translate the block accordingly.
- **3D Rotations**: Rotate blocks by $90^\circ$ around Y (Yaw via Space/R), X (Pitch via X), and Z (Roll via Z). Include a list of wall-kick offset tests if rotation causes overlap.
- **Hard Drop**: Pressing Enter instantly drops the block down to the landing site and bakes it.

---

## 4. Solar Shadow Raycasting
- **Sun Visuals**: A directional light and visual sphere orbiting on a $30$-unit radius arch according to the Sun Angle slider.
- **Face-filtering**: When a block freezes, delete interior facing planes. Maintain only exposed exterior faces as planar meshes.
- **Raycast Logic**:
  - For each face, project a ray from its center towards the sun vector.
  - If the dot product of the face normal and the sun vector is $\le 0.01$, mark as **shaded**.
  - If a ray hits another face mesh in the scene, mark as **shaded**.
  - If unobstructed, mark as **sunlit**.
- **Color Shading Formulas**:
  - *Shaded*: Multiply base color by $0.42$.
  - *Sunlit*: Blend base color with warm solar tint (`#fff3c4`, $0.35$ weight) and multiply by $1.25$.
- **Power Gen**: Each sunlit face yields $0.15\text{ kW}$ of energy.

---

## 5. DXF CAD Exporter
- Loop through all outer faces and write standard CAD `3DFACE` entities.
- **Coordinate Swap**: Map Three-Z to CAD-Y, and Three-Y to CAD-Z to maintain vertical height in CAD space.
- **Layers**: Group faces into separate layers: `SUNLIGHT` (Color index 2: Yellow) and `SHADOW` (Color index 8: Dark Grey).
