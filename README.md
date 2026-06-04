# PomHair XL

A Blender addon that applies Baldur's Gate 3–style vertex color painting to hair card and fur meshes, plus tools for selecting, weighting, and shaping hair cards.

**Version:** 1.9.8 · **Blender:** 4.1.1+ · **Authors:** pommelstrike, percy_verence

Special thanks to percy_verence for the Unreal Engine Hair Rendering workflow and the Moonglasses, InZOI, CursedForge, Hogwarts Legacy & WWE modding scene for contributions and welcoming kindness.

---

# 🚧 Addon Status Update 🚧

🔒 **Public Alpha Tester Phase:**
The addon is currently in a **PUBLIC** Alphatest! Thank you KaNut and PseudoKociara!
https://github.com/pommelstrike/PomHair/releases

🧪 **Quality Assurance in Progress:**
Please fill out issue if any problems

---

[![POMHair Preview](https://img.youtube.com/vi/mSBCoAbjg8s/0.jpg)](https://www.youtube.com/watch?v=mSBCoAbjg8s)

## Installation

1. Download the addon `.zip` file (or clone this repository).
2. Open Blender and go to **Edit → Preferences → Add-ons**.
3. Click **Install…** and select the downloaded `.zip` file.
4. Enable the addon by checking the checkbox next to **Object: PomHair**.
5. In the 3D Viewport, open the sidebar (press **N**) and look for the **PomHair v1.9.8** tab.

## Accessing the PomHair Panel

1. Open Blender and load or create a mesh object (e.g., a hair card mesh).
2. Switch to the **3D Viewport**.
3. Open the **Sidebar** (press `N` if it's not visible).
4. Navigate to the **PomHair v1.9.8** tab in the Sidebar.

---

## Apply PomHair (Vertex Paint)

Paints vertex colors onto selected hair card faces. The colors are stored in a color attribute layer called `POMHAIR` be sure to have that active in your blend file a export.

**Requires:** Edit Mode with faces selected to begin activation.

### Modes

| Mode | Description |
|------|-------------|
| **Linear Gradient** | Paints a gradient along a chosen world axis (X, Y, or Z). |
| **UV Root to Tip Gradient** | Paints a gradient based on the UV V coordinate — root at the bottom, tip at the top. Most common mode for hair cards. |
| **Underside Thickness** | Paints based on how much a vertex normal faces downward. Adds thickness to the underside of hair. |
| **Strand Highlights** | Paints based on how much a vertex normal faces sideways. Great for edge highlights along strands. |
| **PomCluster Variations** | Randomizes vertex color per connected island of faces, giving each hair card a unique color — mimics the dynamic look of in-game BG3 hair. Recommended to apply this first before targeting specific areas. |

### Effect Channels

Each effect paints into a specific color channel:

| Effect                | Channel | Purpose                                            |
| -----------------------| ---------| ----------------------------------------------------|
| **None (Black)**      | —       | Clears / paints black.                             |
| **Graying (Red)**     | R       | Controls where overhair influence on slider in CC. |
| **Thickness (Green)** | G       | Controls hair strand thickness variation.          |
| **Highlights (Blue)** | B       | Controls where highlight appearance.               |

### Settings

- **Gradient Axis** — Which world axis to use for Linear Gradient mode (X, Y, or Z).
- **Invert Gradient** — Flips the gradient direction.
- **Gradient Steps** — Quantizes the gradient into discrete steps (1 = smooth, higher = banded). Only for Linear and UV Gradient modes.
- **Graying / Thickness / Highlights Brightness Intensity** — Multipliers for each color channel (0–4.0).
- **Brightness / Contrast** (PomCluster) — Overall brightness and contrast of the random colors per island.
- **Preview in Vertex Paint** — Automatically switches to Vertex Paint mode after applying so you can see the result.

### Quick Start: Root-to-Tip Graying Gradient

1. Select a hair mesh, enter Edit Mode, select faces (`A`).
2. Click **Apply PomHair**.
3. Set Mode to **UV Root to Tip Gradient**.
4. Set Effect to **Graying (Red)**.
5. Set Graying Brightness Intensity to 1.5.
6. Click **OK**.

### Troubleshooting

- **No Faces Selected** — Select faces in Edit Mode first.
- **No UV Layer** — Add/activate a UV map for UV Gradient mode.
- **Unexpected Colors** — Recalculate normals (`Ctrl+N`) or fix UV orientation.
- Undo with `Ctrl+Z`.

---

## Scalp Texture Cycling
> ⚠️ **This operator does not work yet.** It requires a material library that has not been added to the addon at this time.
Browse and apply scalp texture files from disk to scalp meshes in your scene.

- **Scalp Path** — Set the folder containing scalp textures. Looks for files ending in `_scalp.png`, `_scalp.dds`, or `_scalp.tga`.
- **◀ / ▶ arrows** — Cycle backward and forward through textures.

### Prerequisites

- Scalp meshes must have "scalp" in their name (case-insensitive).
- They must use a material named **PomHair_Scalp** with an Image Texture node.

### Troubleshooting

- **No Textures Found** — Check the path and file extensions.
- **No Scalp Meshes** — Name meshes with "scalp" and assign the PomHair_Scalp material.
- **Material Missing** — Create a PomHair_Scalp material with an Image Texture node connected to Base Color.

---

## Scalp Proximity Select

Selects faces on your hair card mesh that are close to a separate scalp mesh object — useful for quickly grabbing the root portions of hair cards.

- **Scalp Object** — Pick the mesh that represents the scalp/head.
- **Distance Threshold** — How close a face must be to the scalp to get selected.
- **Mode** — **Proximity** selects only faces within the distance threshold. **Whole Island** selects the entire connected hair card if any vertex is near the scalp.

---

## UV Island Selection

Expands a partial face selection to complete UV islands. Select a few faces of a hair card and this operator selects the whole thing.

**Requires:** Edit Mode with some faces selected

### Expand Modes

After completing the initially selected islands, optionally find and select additional nearby islands:

| Mode | Description |
|------|-------------|
| **None** | Only completes partially selected UV islands. |
| **+X / +Y / +Z Axis** | Also selects nearby islands aligned along the chosen world axis, facing the same direction, within a distance. |
| **UV BBox + 3D Proximity** | Also selects islands with overlapping UV bounding boxes that are within a 3D distance. |

### Settings

- **Expand Distance** — Maximum 3D distance for expansion.
- **Angle Threshold** — (Axis modes) Maximum angle between island normals.
- **UV Overlap Margin** — (UV Overlap mode) Extra padding on UV bounding boxes before checking overlap.

---

## Weight Gradient

Generates a vertex weight gradient along hair cards based on UV coordinates. Weights go from full (1.0) at the root to zero (0.0) at the tip (or vice versa). Useful for driving armature deformations, shape keys, or shader effects.

**Requires:** Weight Paint Mode

- **UV Axis** — **V (Root→Tip)** for standard hair cards, or **U (Across)** for across the card width.
- **Gradient Falloff** — Controls the curve shape. Negative = ease-in, positive = ease-out, zero = linear.
- **Invert** — Flips the gradient (tip = 1.0, root = 0.0).
- **Create New Group** — Creates a new vertex group instead of painting into the active one.
- **Group Name** — Name for the new vertex group.

---

## Auto Weight from Armature

Parents the hair card mesh to an armature with automatic weights, then optionally cleans up the result with post-processing.

**Requires:** Object Mode

- **Armature** — Pick the armature to parent to.

### Post-Processing

| Option | Description |
|--------|-------------|
| **Clear Roots** | Removes weights from vertices near the scalp so roots stay attached to the head. Uses the Scalp Object if set, otherwise falls back to UV V coordinate. |
| **Smooth Weights** | Averages vertex weights for smoother deformations. Adjustable factor, iterations, and expand/contract. |
| **Tweak Levels** | Adjusts all weights with offset (add) and gain (multiply), like an image levels adjustment. |

---

## Sample Weights from Target

Transfers vertex weights from a target mesh (e.g., a character body) onto the hair card mesh.

**Requires:** Object Mode

- **Target Mesh** — The mesh to copy weights from (must have vertex groups).

### Methods

| Method | Description |
|--------|-------------|
| **Nearest Vertex** | For each hair vertex, finds the closest point on the target and copies weights. Per-vertex precision. |
| **Root Center** | Samples weights at each hair card's root center and applies uniformly to the whole card. Good for rigid cards. |
| **Projected (Data Transfer)** | Uses Blender's Data Transfer modifier for projection-based sampling. Best for meshes that wrap around the target. |

---

## Hair Card Shaping

Tools for bending, straightening, and randomizing hair card geometry. All work on selected faces in Edit Mode.

### Straighten Edge Loops

Straightens the long edge loops of hair cards. Each UV island is analyzed and vertices along each column (root to tip) are aligned.

- **Method** — **Line** projects vertices onto a straight line from first to last. **Smooth** applies Laplacian smoothing over multiple passes.
- **Strength** — How much to straighten (0 = no change, 1 = fully straight).
- **Iterations** — (Smooth only) Number of smoothing passes.

### Auto S-Curve

Applies S-curve bend to hair cards for natural wavy shapes.

- **Strength** — How far to displace vertices.
- **Number of Curves** — How many S-curves along the hair card length (1 = gentle wave, higher = tighter ripples).
- **Bend Direction** — **In-Plane (Snake)** bends sideways within the card's own plane. **Out-of-Plane (Wave)** bends perpendicular to the card surface.
- **Profile** — **Sine (Smooth)** for rounded curves, **Sharp (Triangle)** for angular bends.

### Jitter Hair Cards

Randomizes duplicated hair cards with position offsets and per-vertex strand noise for natural volume and variation.

- **Seed** — Random seed for reproducible results.
- **Position Offset** — Moves each card as a whole. Set the **Amount** and direction (**Normal Only** for volume/lift, **Width Only** for spread, **Normal + Width** for both).
- **Strand Noise** — Adds wavy per-vertex displacement. Control **Amount** and **Frequency** (how many bumps along the strand).
- **Pin Roots** — Keeps root vertices in place so hair stays attached to the scalp. **Pin Ratio** controls what fraction of the strand is pinned.

---

## Best Practices

- Work on duplicated meshes to test non-destructively.
- Combine modes by running Apply PomHair multiple times with different effects (e.g., graying gradient + thickness underside).
- Apply PomCluster Variations first, then target specific areas with gradient or directional modes.
