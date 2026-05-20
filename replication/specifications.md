# Technical Specifications: 3D Solar Tetris

This document contains the exact math models, algorithms, matrices, and data formats required to replicate the **3D Solar Tetris** spatial composition engine.

---

## 1. Grid & Coordinates Specification

- **Workspace Area**: $10 \text{ units} \times 10 \text{ units}$ on XZ plane.
- **Coordinate System**:
  - $X \in [-5, 4]$ (Integer indices)
  - $Z \in [-5, 4]$ (Integer indices)
  - $Y \ge 0$ (Height index)
- **Cell Center Offset**:
  $$\text{Position}_{\text{ThreeJS}} = (x + 0.5, y + 0.5, z + 0.5)$$

---

## 2. Block Offsets & Rotations

Each block is represented as a coordinates offset array: `[dx, dy, dz]`.

### 2.1 Rotation Matrices ($90^\circ$ steps)

- **Yaw (Y-axis / Horizontal Rotation)**:
  $$\begin{bmatrix} dx' \\ dy' \\ dz' \end{bmatrix} = \begin{bmatrix} -dz \\ dy \\ dx \end{bmatrix}$$
  *JavaScript Implementation*: `rotated = offsets.map(o => [-o[2], o[1], o[0]]);`

- **Pitch (X-axis Rotation)**:
  $$\begin{bmatrix} dx' \\ dy' \\ dz' \end{bmatrix} = \begin{bmatrix} dx \\ -dz \\ dy \end{bmatrix}$$
  *JavaScript Implementation*: `rotated = offsets.map(o => [o[0], -o[2], o[1]]);`

- **Roll (Z-axis Rotation)**:
  $$\begin{bmatrix} dx' \\ dy' \\ dz' \end{bmatrix} = \begin{bmatrix} -dy \\ dx \\ dz \end{bmatrix}$$
  *JavaScript Implementation*: `rotated = offsets.map(o => [-o[1], o[0], o[2]]);`

### 2.2 Wall-Kick Tests
If a rotation collides, test these offset translations sequentially. The first translation that returns no collision is applied:
```javascript
const KICK_TESTS = [
    [1, 0, 0], [-1, 0, 0], [0, 0, 1], [0, 0, -1], // 1-step horizontal kicks
    [0, 1, 0],                                    // 1-step vertical kick
    [2, 0, 0], [-2, 0, 0], [0, 0, 2], [0, 0, -2]  // 2-step horizontal kicks
];
```

---

## 3. Camera-Relative Snapping Algorithm

Let $V_{\text{cam}}$ be the camera world direction vector projected onto the XZ plane:
```javascript
const camDir = new THREE.Vector3();
camera.getWorldDirection(camDir);
camDir.y = 0;
camDir.normalize();
```

Let $R_{\text{cam}}$ be the camera's local right vector projected onto the XZ plane:
```javascript
const camRight = new THREE.Vector3().crossVectors(camDir, new THREE.Vector3(0, 1, 0));
camRight.y = 0;
camRight.normalize();
```

Define the 4 grid cardinal directions:
- $C_1 = (1, 0, 0) \quad [+\text{X}]$
- $C_2 = (-1, 0, 0) \quad [-\text{X}]$
- $C_3 = (0, 0, 1) \quad [+\text{Z}]$
- $C_4 = (0, 0, -1) \quad [-\text{Z}]$

Select the snapped cardinal direction $D_{\text{fwd}}$ and $D_{\text{right}}$ by maximizing the dot product:
$$D_{\text{fwd}} = \arg\max_{C_i} (V_{\text{cam}} \cdot C_i)$$
$$D_{\text{right}} = \arg\max_{C_i} (R_{\text{cam}} \cdot C_i)$$

For key inputs, translate the active block coordinates by:
- `W` / `Arrow Up`: $+D_{\text{fwd}}$ (Forward)
- `S` / `Arrow Down`: $-D_{\text{fwd}}$ (Backward)
- `A` / `Arrow Left`: $-D_{\text{right}}$ (Left)
- `D` / `Arrow Right`: $+D_{\text{right}}$ (Right)

---

## 4. Solar Shading & Colors

### 4.1 Face plane details
Each face geometry is a `PlaneGeometry(1, 1)` positioned at the center of the voxel's boundary side:
- **$+X$ Face**: Center $(x+1.0, y+0.5, z+0.5)$, normal $(1, 0, 0)$, rotation $(0, \pi/2, 0)$
- **$-X$ Face**: Center $(x, y+0.5, z+0.5)$, normal $(-1, 0, 0)$, rotation $(0, -\pi/2, 0)$
- **$+Y$ Face**: Center $(x+0.5, y+1.0, z+0.5)$, normal $(0, 1, 0)$, rotation $(-\pi/2, 0, 0)$
- **$-Y$ Face**: Center $(x+0.5, y, z+0.5)$, normal $(0, -1, 0)$, rotation $(\pi/2, 0, 0)$
- **$+Z$ Face**: Center $(x+0.5, y+0.5, z+1.0)$, normal $(0, 0, 1)$, rotation $(0, 0, 0)$
- **$-Z$ Face**: Center $(x+0.5, y+0.5, z)$, normal $(0, 0, -1)$, rotation $(0, \pi, 0)$

### 4.2 Solar Color Engine
Let $C_{\text{base}}$ be the hex color of the voxel. Let $T_{\text{solar}} = (255, 243, 196) \leftrightarrow \texttt{\#fff3c4}$ be the warm solar tint.

- **Shaded color formula**:
  $$C_{\text{shaded}} = C_{\text{base}} \times 0.42$$
- **Sunlit color formula**:
  $$C_{\text{sunlit}} = \text{Lerp}(C_{\text{base}}, T_{\text{solar}}, 0.35) \times 1.25$$

---

## 5. 3D DXF Format & Syntax

The exporter builds a raw ASCII DXF string:

### 5.1 DXF Entities Section (3DFACE)
For each face, extract the 4 corner world coordinates ($V_0, V_1, V_2, V_3$) from its matrix:
```javascript
const localV = [
    new THREE.Vector3(-0.5, -0.5, 0),
    new THREE.Vector3(0.5, -0.5, 0),
    new THREE.Vector3(0.5, 0.5, 0),
    new THREE.Vector3(-0.5, 0.5, 0)
];
const worldV = localV.map(lp => lp.applyMatrix4(faceMesh.matrixWorld));
```

Map coordinates with swapped Y and Z axes:
- $\text{CAD-X}_i = \text{worldV}[i].x$
- $\text{CAD-Y}_i = \text{worldV}[i].z$
- $\text{CAD-Z}_i = \text{worldV}[i].y$

Write each face using standard group codes:
```text
0
3DFACE
8
[SUNLIGHT | SHADOW]
10
[CAD-X_0]
20
[CAD-Y_0]
30
[CAD-Z_0]
11
[CAD-X_1]
21
[CAD-Y_1]
31
[CAD-Z_1]
12
[CAD-X_2]
22
[CAD-Y_2]
32
[CAD-Z_2]
13
[CAD-X_3]
23
[CAD-Y_3]
33
[CAD-Z_3]
```
Ensure the DXF string terminates with:
```text
0
ENDSEC
0
EOF
```
This syntax complies with AutoCAD, Rhino, and SketchUp import layouts.
