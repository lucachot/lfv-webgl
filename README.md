# WebGL Light Field Renderer

A browser-based light field renderer built with WebGL2. It takes a grid of real camera images (data cameras) and synthesises a virtual camera view with controllable position, aperture, and focal plane — simulating depth of field effects in real time.

## Project Structure

| File | Purpose |
|---|---|
| `index.html` | HTML layout and canvas |
| `index.js` | WebGL setup, shader, camera matrices, render loop |
| `input.js` | Keyboard, mouse, and UI event handlers |
| `components.js` | Dynamic HTML component creation and display updates |
| `parse-imgs.py` | Pre-processing script — reads the image grid and generates `imgs.js` |
| `imgs.js` | Generated image data consumed by the shader (not checked in) |
| `gl-matrix.js` | Vendored [glMatrix](https://glmatrix.net/) library |

## Setup

1. Populate the `imgs/` directory with images named `out_U_V_u_v.png` (Stanford light field format).
2. Run the parser, specifying how many images out from centre to include:
   ```bash
   python3 parse-imgs.py
   # Edit the width/height variables inside the script — e.g. width=height=2 gives a 3×3 grid
   ```
   This produces `imgs.js`.
3. Set `imgWidth` and `imgHeight` in `index.js` to match your image dimensions (default: `1024`).
4. Serve the directory with any static file server, for example:
   ```bash
   npx servez .
   # or: python3 -m http.server
   ```
5. Open the served page in a browser that supports WebGL2.

## Controls

### Camera Modes

Toggle between **XYZ** (default) and **Orbital** mode using the **Orbital** checkbox.

#### XYZ Mode

Move the virtual camera along each axis:

| Key | Action |
|---|---|
| `←` / `→` | Move along X |
| `Space` / `Enter` | Move along Y (up / down) |
| `↑` / `↓` | Move along Z (forward / back) |

You can also type exact XYZ coordinates into the input fields.

#### Orbital Mode

| Input | Action |
|---|---|
| Drag mouse left/right | Rotate camera in the XZ plane |
| Drag mouse up/down | Rotate camera in the YZ plane |
| `↑` / `↓` | Decrease / increase orbital radius |

You can also set orbital coordinates and the Z position of the orbital centre directly.

### Focal Plane

| Input | Action |
|---|---|
| Scroll up | Move focal plane further from camera |
| Scroll down | Move focal plane closer to camera |
| Input field | Set focal plane Z directly |

### Aperture

Use the slider to control aperture size. Larger aperture = stronger depth-of-field blur for objects off the focal plane.

## Recommended Settings (Stanford Tarot Cards & Crystal Ball)

For a quick visual demo with the Stanford dataset:

1. Run `parse-imgs.py` with `width = height = 6` (produces a 13×13 grid).
2. Start the server and open the page.
3. Set **Aperture** to `12`.
4. Set **Focal Plane Z** to `-600`.
5. Scroll or use arrow keys to explore the depth-of-field effect.
6. Enable **Orbital** mode, set orbital centre Z to `600`, then drag the mouse to orbit around the scene.

## Known Issues

- **Shader texture memory limit** — the WebGL2 shader has a fixed maximum number of samplers; very large grids may exceed this.
- **Artefacts at large aperture** — with `width = height = 7` and maximum aperture, artefacts appear as the focal plane moves far from the camera.
