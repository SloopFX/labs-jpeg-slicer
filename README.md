# JPEG Layers - Interactive Comparison & Analysis Tool

A web-based JPEG frequency domain analyzer that visualizes how DCT coefficients contribute to image reconstruction.

Labs experiment by [Sloop FX](https://sloop.ai)

## Motivation

- slice jpeg layers to see each actual layer contribution
- been something wanted few times over years, libraries don't do
- intuitive visual for our hypertokens work (ai vs image signatures)
- testing github pages, public repos ahead of next main app, etc.
- MIT so fork away, file PR, etc.

## AI
- 1 of many tools Sloop has on our "when can AI k-shot this idea"
- see how far we can push AI in single code and single chat session
- init was 90% Claude Opus 4.1 in single Cursor chat plus minor tweaks by Gemini 2.5 and GPT 5 at the end for the initial release
- only hand-holding was bug pasting from browser &/or js console, prompts, and tweaking the gif.js and gif.worker.js downloads
- had tried 2x each about year ago give or take forget models
- total time about 2 focused hours + 1 hour on walkabout

## Todos
- other lossy image formats?
- some sort of relative entropy vis
- use image slider lib instead of diy cuz ai struggled on lib call
- add init chat after redacting any security things 
- not much else atm this version does most what wanted

## UI Modes

Images persist when switching between modes - no need to reload when toggling.

### GIF Mode
- **Single K-value slider** - Explore layers 0-63 interactively  
- **Cumulative/Individual toggle** - See full reconstruction or isolated frequencies
- **Color Visualizations**:
  - **RGB** - Original color reconstruction
  - **Grayscale** - Luminance-only view
  - **Thermal** - Infrared visualization (ROYGBIV spectrum)
  - **Inverted** - Negative/inverted colors
- **Animation Features**:
  - **Play/Pause** - Animate through all 64 layers
  - **Direction** - Forward (0→63) or reverse (63→0)
  - **Speed Control** - 50-500ms per frame
  - **Export GIF** - Creates actual animated GIF file (requires HTTP server)
- **Quick Access Grid** - Jump to key layers with one click

### Compare Mode (Default)
The default view with advanced comparison tools. Toggle the checkbox to switch to GIF mode.

## Features

### Advanced Comparison Interface
- **Sliding Before/After Viewer**: Interactive slider comparison using image-compare-viewer library
- **Flexible K-Value Selection**: Choose any two K values (0-63) to compare
- **Three-Panel Layout**: 
  - Left panel: Selected K-value preview
  - Center: Interactive sliding comparison
  - Right panel: Second K-value or composite result

### Layer Display Modes
- **Cumulative (≤K)**: Shows all frequencies from K=0 up to K (traditional JPEG reconstruction)
- **Individual (=K)**: Shows only the specific K frequency contribution in isolation

### Visualization Modes (per panel)
- **RGB**: Standard YCbCr to RGB conversion
- **Gray**: Luminance channel only for structural analysis
- **Heat**: Energy heatmap visualizing frequency activity
- **Δ from K=0**: Shows what this layer adds compared to DC-only
- **Δ from K-1**: Shows incremental changes from previous layer

### Layer Management
- **All 64 Layers Displayed**: Complete frequency layer list with thumbnails
- **Quick Selection**: "Left" and "Right" buttons to instantly set comparison values
- **Text Input**: Type layer ranges like "0-5,10,15-20" for quick selection
- **Multiple Build Options**:
  - **L→**: Build from left K setting (respects cumulative/individual mode)
  - **R→**: Build from right K setting (respects cumulative/individual mode)
  - **☑→**: Build from checked layers
  - **T→**: Build from text input
  - **✕**: Clear all selections
- **Auto-sync**: Checkboxes and text input stay synchronized
- **Real-time Updates**: Instant preview as you adjust K values

## Understanding JPEG Frequency Layers

Every baseline JPEG image contains exactly 64 frequency layers (K=0 to K=63):
- **K=0**: DC component (average color of each 8×8 block)
- **K=1-5**: Low frequencies (basic shapes and gradients)
- **K=6-14**: Mid frequencies (edges and patterns)
- **K=15-27**: Higher frequencies (textures)
- **K=28-63**: Highest frequencies (fine details and noise)

## How It Works

1. **Drop a JPEG**: Upload any baseline JPEG file (progressive JPEGs will be rejected)
2. **Select Layers**: Use sliders or click buttons in the layer list to choose comparison points
3. **Compare**: The center panel shows an interactive slider between your selected layers
4. **Build Composites**: Check multiple layers and click "Build Composite" to see custom reconstructions

## Technical Details

- Parses JPEG markers (DQT/DHT/SOF0/SOS)
- Huffman-decodes quantized DCT coefficients
- Reconstructs images by selectively enabling zig-zag frequency indices
- Supports YCbCr color spaces with 4:4:4, 4:2:2, and 4:2:0 sampling
- Handles restart intervals and byte-stuffing

## Browser Requirements

- Modern browser with Canvas support
- ES6+ JavaScript features
- ~100MB RAM for typical images

## Usage

Simply open `index.html` in a web browser. No build process required.

**For best results:**
- Serve via HTTP for GIF export: `python -m http.server 8000`
- Use baseline JPEGs for accurate frequency analysis
- Test image included: `mandril_color.jpg`

## Implementation Notes

- **Single file application** - Everything in one `index.html` for easy deployment
- Built-in sliding comparison viewer with drag-and-drop interaction
- No build process or external dependencies (except GIF.js loaded from CDN)
- Touch-enabled for mobile devices
- Performance optimizations:
  - RequestAnimationFrame for smooth slider movement
  - Debounced text input parsing
  - Cached layer renderings
  - Transitions disabled during drag for responsiveness
  - Canvas contexts use `willReadFrequently` flag for better performance

## Accessibility Features

- **ARIA Labels**: All interactive elements have descriptive labels
- **Keyboard Navigation**: Full keyboard support (Tab, Enter, Space)
- **Screen Reader Support**: Status updates announced via aria-live regions
- **Focus Indicators**: Clear visual feedback for keyboard users
- **Progress Indicators**: Real-time progress updates with ARIA attributes

## Progressive JPEG Support

Progressive JPEGs are now supported with automatic conversion:
- Files are automatically detected as progressive (SOF2 marker or progressive SOS)
- First attempts to convert to baseline JPEG for accurate analysis
- Falls back to approximation if conversion fails
- All visualization modes work with converted images
- **Note**: Some progressive JPEGs may still show artifacts if conversion fails
- Enhanced error handling for malformed or incomplete coefficient data

For exact frequency analysis, convert to baseline JPEG:
```bash
# Convert using ImageMagick
convert input.jpg -interlace none output.jpg
```
