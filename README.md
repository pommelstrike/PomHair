# PomHair Add-on User Guide

## Introduction

PomHair is a Blender add-on designed to apply vertex paint values to mesh objects, specifically tailored for creating or modifying haircard effects inspired by Baldur's Gate 3 (BG3) styles. It operates on selected faces in Edit Mode and creates or modifies a vertex color attribute named "BG3Hair" on the mesh. This attribute uses RGB channels to control effects like graying (red), thickness (green), and highlights (blue).

The add-on provides several modes for generating these vertex colors, including gradients, underside effects, highlights, and randomized template-style variations. Additionally, it includes tools for cycling through scalp textures applied to meshes named with "scalp" in a specified directory.

This guide provides a detailed walkthrough of using the add-on's features. Ensure you are working on a mesh object in Blender version 4.1.1 or compatible.

## Accessing the PomHair Panel

1. Open Blender and load or create a mesh object (e.g., a haircard mesh).
2. Switch to the **3D Viewport**.
3. Open the **Sidebar** (press `N` if it's not visible).
4. Navigate to the **PomHair** tab in the Sidebar. This is where the main controls are located.

The panel includes:
- The **Apply PomHair** button, which opens a dialog for configuring and applying vertex paints.
- A **Scalp Texture** subpanel for managing scalp textures (detailed later).

## Applying PomHair Vertex Paints: Step-by-Step Walkthrough

PomHair applies vertex colors to selected faces on a mesh. It requires the object to be in **Edit Mode** with faces selected. The vertex colors are stored in a BYTE_COLOR attribute called "BG3Hair", which can be previewed in Vertex Paint mode.

### Prerequisites
- Select your mesh object.
- Enter **Edit Mode** (`Tab` key).
- Select the faces you want to paint (e.g., using Face Select mode). If no faces are selected, the operator will warn you and cancel.

### Step 1: Open the Apply PomHair Dialog
- In the PomHair panel, click **Apply PomHair**.
- This opens a popup dialog with configuration options.

### Step 2: Configure the Mode
Choose a **Mode** from the dropdown. Each mode determines how the base intensity (used for the selected effect) is calculated across the selected faces. Here's a breakdown of each:

- **Linear Gradient**: Creates a gradient based on vertex positions along a chosen axis (e.g., from bottom to top).
  - Useful for simple root-to-tip transitions on aligned geometry.
- **UV Root to Tip Gradient**: Uses the active UV map's V-coordinate (vertical axis) for the gradient (assumes 0 at root, 1 at tip).
  - Requires an active UV layer; if missing, the operator will warn and cancel.
  - Ideal for unwrapped hair strands where UVs map root-to-tip.
- **Underside Thickness**: Bases intensity on the downward-facing normal (max(0, -normal.z)).
  - Higher values on undersides, simulating thickness or shadowing.
- **Strand Highlights**: Bases intensity on the absolute vertical normal (1 - |normal.z|).
  - Higher values on side-facing surfaces, for strand-like highlights.
- **Template Style Variations**: Generates randomized per-island (connected face groups) values, simulating clustered hair variations like template hairstyles.
  - Islands are automatically detected as connected selected faces.
  - Randomizes base RGB values with biases (e.g., low for green/thickness), then applies brightness and contrast globally.
  - No gradient or axis options apply here; it's for variation across groups.

### Step 3: Configure Mode-Specific Settings
Depending on the mode, adjust these:

- **Gradient Axis** (for Linear Gradient only): Choose X, Y, or Z axis for the position-based gradient.
  - Example: Use Z for vertical hair (bottom to top).
- **Invert Gradient** (for Linear/UV Gradient): Flip the gradient direction (e.g., tip-to-root instead of root-to-tip).
- **Gradient Steps** (for Linear/UV Gradient): Number of discrete steps (1-10). 1 is smooth; higher values create banded/stepped effects.
  - Example: Set to 3 for a low-res, stylized gradient.

For **Template Style Variations**:
- **Brightness (Template Mode)**: Scales base random values (0-2.0). Higher brightens overall.
- **Contrast (Template Mode)**: Adjusts contrast around mid-tone (0-2.0). Higher increases variation.

### Step 4: Choose the Effect
Select an **Effect** to determine which RGB channel receives the intensity:
- **None (Black)**: Sets the channel to 0 (no effect, black).
- **Graying (Red)**: Applies to red channel for graying effects.
- **Thickness (Green)**: Applies to green for thickness simulation.
- **Highlights (Blue)**: Applies to blue for highlight effects.

### Step 5: Adjust Intensity Multipliers
These scale the base intensity for the chosen effect (0-4.0):
- **Graying Brightness Intensity**: Multiplies red channel.
- **Thickness Brightness Intensity**: Multiplies green channel.
- **Highlights Brightness Intensity**: Multiplies blue channel.
- Example: Set Graying to 2.0 for stronger graying at the tips in a gradient mode.

Unused channels are set to 0.

### Step 6: Preview Option
- **Preview in Vertex Paint**: Enabled by default. After applying, switches to Vertex Paint mode and sets "BG3Hair" as the active color attribute for immediate visual feedback.
  - Disable if you prefer to stay in Edit Mode.

### Step 7: Execute the Operator
- Click **OK** in the dialog.
- The add-on processes selected faces:
  - For gradients: Normalizes positions or UVs to 0-1, applies steps/invert if set.
  - For underside/highlights: Uses vertex normals.
  - For template: Detects islands, assigns random bases, applies brightness/contrast.
- If successful, you'll see the vertex colors applied. In Preview mode, switch to Vertex Paint to view (colors may appear in grayscale initially; use viewport shading to see RGB).

### Example Walkthrough: Applying a Root-to-Tip Graying Gradient
1. Select a hair mesh, enter Edit Mode, select all faces (`A`).
2. Click **Apply PomHair**.
3. Set Mode to **UV Root to Tip Gradient** (assuming UVs are set up).
4. Uncheck **Invert Gradient** (root=low, tip=high).
5. Set Gradient Steps to 1 (smooth).
6. Set Effect to **Graying (Red)**.
7. Set Graying Brightness Intensity to 1.5.
8. Ensure Preview is checked.
9. Click OK.
10. Blender switches to Vertex Paint; red increases from root to tip.

### Troubleshooting
- **No Faces Selected**: Select faces in Edit Mode.
- **No UV Layer**: Add/activate a UV map for UV Gradient.
- **Unexpected Colors**: Check normals (recalculate with `Ctrl+N`) or UV orientation.
- Undo with `Ctrl+Z` (operator supports UNDO).

## Managing Scalp Textures: Step-by-Step Walkthrough

The Scalp Texture subpanel allows cycling through texture files (e.g., _scalp.png, _scalp.dds, _scalp.tga) in a directory. It applies them to a material named "PomHair_Scalp" on meshes with "scalp" in their name (case-insensitive), within the current collection.

### Prerequisites
- Have scalp meshes in your scene (e.g., named "Scalp_Mesh").
- Assign the "PomHair_Scalp" material to them (create if missing, with an Image Texture node).
- Textures must be in the specified path.

### Step 1: Set the Scalp Path
- In the PomHair panel, under Scalp Texture, find the **Scalp Path** field.
- Enter or browse to a directory containing scalp textures (e.g., "C:\MyTextures\Scalps").
- The add-on scans for files ending in _scalp.png/dds/tga.

### Step 2: View Current Texture
- The top row displays the current texture name (e.g., "human_scalp.png") or "None" if none loaded.

### Step 3: Cycle Textures
- Use the arrow buttons:
  - **Left Arrow** (`<` icon): Cycles backward.
  - **Right Arrow** (`>` icon): Cycles forward.
- Clicking loads the next/previous texture if not already in Blender, applies it to the Image Texture node in "PomHair_Scalp", and updates all matching scalp meshes.
- The display updates to show the new texture name.

### Example Walkthrough: Cycling Scalp Textures
1. Set Scalp Path to a folder with 3 textures: scalp1.png, scalp2.dds, scalp3.tga.
2. Ensure scalp meshes use "PomHair_Scalp" material with an Image Texture node.
3. Click Right Arrow: Loads and applies scalp1.png, displays "scalp1.png".
4. Click Right Arrow again: Switches to scalp2.dds.
5. Click Left Arrow: Returns to scalp1.png.

### Troubleshooting
- **No Textures Found**: Check path validity and file extensions.
- **No Scalp Meshes**: Name meshes with "scalp" and assign "PomHair_Scalp".
- **Material Missing**: Create "PomHair_Scalp" with a Principled BSDF and Image Texture node connected to Base Color.
- Textures wrap around at list ends.

## Best Practices
- Work on duplicated meshes to test non-destructively.
- Combine modes by running the operator multiple times with different effects (e.g., graying gradient + thickness underside).
- For template mode, select disconnected islands for natural clustering.
- Use Vertex Paint mode to manually tweak "BG3Hair" after applying.
- Export painted meshes for use in BG3 or other engines supporting vertex colors.

This guide covers core usage. Experiment with settings for custom hair effects!
 
