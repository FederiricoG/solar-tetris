# 3D Solar Tetris Spatial Composition

A standalone 3D spatial design, voxel-composition, and real-time solar analysis tool built on Three.js, OrbitControls, and Tailwind CSS. 

This repository was created in the context of the Sommersemester 2026 elective module **[FWP 3: Berktold / Ciganek / Cie](https://ar.hm.edu/studierende/bachelorstudiengang/wahlpflichtmodule/sommersemester_2026/26sose_ba_fwp3_berktold_ciganek_cie.de.html)** at the **Hochschule München (HM)** Faculty of Architecture.

---

## 1. Educational & Pedagogical Context

This project represents the outcome of a 90-minute workshop introducing architecture students to the practical, system-level application of AI agents in design. During the session, students set up the **[Antigravity IDE](https://antigravity.google)** agent harness, walked through the **[Presentation Slides](replication/slides.html)**, and executed hands-on experiments, injecting a structured prompt to construct a functional 3D Solar Tetris application in a single standalone HTML file.

The workshop yielded two primary reflections on the future of architectural practice and AI integration:

### 💡 The Architect's Custom Knowledge Base
AI models and agents are only as powerful as the context they operate within. To harness AI effectively, architects must transition from prompt-writers to system architects who build and maintain their own **custom knowledge bases** (comprising localized building codes, material libraries, detail templates, and historical design guidelines).

### 📂 Methodology & Framework
The structured file and prompting approach used in this workshop is based on the **Interpreted Context Methodology (ICM)**, an open-source AI alignment framework developed by **[Jake van Clief](https://github.com/RinDig/Interpreted-Context-Methdology)**.

### 🔄 AI as a Generative System Engine
During the workshop, all students executed the **exact same prompt** under a deterministic framework. Yet, the AI agents produced slightly different, localized variations of the 3D Solar Tetris application. This variance highlights a key shift: AI operates not as a static drawing tool, but as a generative system engine. By defining programmatic rules and constraints (e.g., solar exposure, grid alignments, export criteria) rather than static geometry, architects can direct AI agents to dynamically generate, iterate, and optimize within vast, performative solution spaces.

---

## 2. Application Features

1. **Workspace & 3D Viewport**:
   * A premium, glassmorphic dark-themed UI utilizing the clean *Outfit* font.
   * An interactive 3D grid helper ($10 \times 10$ units) representing the ground plane.
   * Real-time metrics dashboard tracking: *Total Blocks*, *Tower Height (m)*, *Solar Exposure Ratio (%)*, and *Estimated Solar Generation (kW)*.

2. **3D Tetris Mechanics**:
   * Falling voxel shapes (including standard Tetris polyominoes and true 3D shapes) with persistent neon colors.
   * **Camera-Relative Movement**: Active block translations automatically calculate the camera's XZ forward/right vectors, snapping block movements to the closest cardinal grid direction relative to your screen view.
   * **Full 3D Rotation**: Complete $90^\circ$ rotation capabilities around the vertical Y-axis (Yaw), X-axis (Pitch), and Z-axis (Roll) with active wall-kick tests.
   * **Ghost Projection**: Wireframe preview representing the exact landing site of the active block.
   * **Hard Drop**: Pressing `Enter` instantly drops and bakes the block.

3. **Solar Shadows & Color-Blending Engine**:
   * A dynamic, visual sun sphere that orbits the tower based on an interactive Sun Angle slider ($0^\circ - 180^\circ$).
   * **Face Filtering**: Interstitial faces between touching blocks are automatically deleted to optimize rendering and CAD outputs.
   * **Real-Time Raycasting**: The engine performs raycasting from the center of each exposed face towards the sun vector to detect obstructions.
   * **Dynamic Lighting States**:
     * *Shaded*: Faces pointing away from the sun or blocked by other blocks are darkened ($\text{Base Color} \times 0.42$).
     * *Sunlit*: Unobstructed faces pointing towards the sun blend with a warm solar tint ($\text{Hex: \#fff3c4}$) and are boosted in brightness ($\text{Base Color} \times 1.25$).
   * **Power Generation**: Each sunlit face dynamically generates $0.15\text{ kW}$ of energy, updating the live dashboard.

4. **3D DXF CAD Exporter**:
   * Instant download button exporting the spatial composition as a standard 3D DXF file.
   * **Axis Mapping**: The exporter automatically swaps coordinates to match standard CAD space ($\text{CAD-X} = X$, $\text{CAD-Y} = Z$, $\text{CAD-Z} = Y$), ensuring height is correctly mapped to the vertical axis in Rhino, AutoCAD, or SketchUp.
   * **Layer Classification**: Faces are automatically split into `SUNLIGHT` and `SHADOW` layers for immediate performance analysis in external CAD software.

---

## 3. Interactive Controls

* **Orbit Camera (Rotate)**: Right-Click + Drag
* **Pan Camera (Move)**: Left-Click + Drag
* **Zoom Viewport**: Mouse Scroll Wheel
* **Slide Active Block**: `W`, `A`, `S`, `D` or **Arrow Keys** (Camera-Relative)
* **Hard Drop**: `Enter`
* **Rotate Yaw (Y-axis)**: `Space` or `R`
* **Rotate Pitch (X-axis)**: `X`
* **Rotate Roll (Z-axis)**: `Z`

---

## 4. How to Run & Play

### 🎮 Play Directly Online
You can play and experiment with the application directly in your browser:
* **[Play Live (via GitHack)](https://raw.githack.com/FederiricoG/solar-tetris/main/index.html)**

### 💻 Run Locally
1. Ensure you have an active internet connection (to load Three.js and Tailwind via CDN).
2. Clone this repository or download the files.
3. Open `index.html` directly in any web browser.

### 🌐 Deploy Your Own Link (GitHub Pages)
If you want to host your own official link:
1. Go to the **Settings** tab of your repository on GitHub.
2. Select **Pages** from the left sidebar.
3. Under **Build and deployment > Source**, choose **Deploy from a branch**.
4. Set the branch to **`main`** and the folder to **`/ (root)`**, then click **Save**.
5. After a minute, your game will be live at `https://FederiricoG.github.io/solar-tetris/`!

---

## 5. Replication Protocol

If you want to replicate or extend the application, explore the resources in the **[`replication/`](file:///c:/Users/feder/.gemini/antigravity/vault/projects/solar_tetris/replication)** folder:
* **[`slides.html`](file:///c:/Users/feder/.gemini/antigravity/vault/projects/solar_tetris/replication/slides.html)**: The presentation slides used during the workshop (*Toward Agentic AI in Architecture*).
* **[`system_prompt.md`](file:///c:/Users/feder/.gemini/antigravity/vault/projects/solar_tetris/replication/system_prompt.md)**: The system instructions fed to the AI.
* **[`specifications.md`](file:///c:/Users/feder/.gemini/antigravity/vault/projects/solar_tetris/replication/specifications.md)**: The mathematical matrices, coordinate systems, and DXF specs.
