# Imagerry CLI (v0.2.0-beta)

The official high-performance command-line interface for [Imagerry](https://imagerry.com), built for automating image processing, styling, format conversion, and mockups directly from your terminal.

> [!IMPORTANT]
> **Privacy-first processing**
>
> Imagerry CLI processes images **locally on your computer** using a blazing-fast native graphics engine. Your images are **never uploaded to Imagerry or any external image-processing server**.
>
> A network connection is only used when required to verify your Imagerry Pro license.

## Features

* 🖥️ **Terminal Processing**: Style single images or batch process entire directories
* 🖼️ **Multi-Format Output**: Native WebP, JPEG, and PNG encoding with configurable quality
* 🎨 **Built-in Presets**: Stunning modern aesthetics (`mesh`, `gradient`, `cyberpunk`, `sunset`, `studio`, `aurora`)
* ⚡ **Direct CLI Overrides**: Fine-tune `--padding`, `--radius`, `--shadow`, `--bg-color`, and `--ratio` directly from the command line without writing JSON
* 📁 **Batch Processing**: Parallel multi-image folder and wildcard conversion (`--batch`) with live progress
* ⚙️ **Project Config**: Define team-wide default styles in `imagerry.config.json` with `imagerry init`
* 🌐 **Self-Hosted API Server**: Run a local or VPS microservice with `imagerry serve`
* 🧠 **Model Context Protocol (MCP)**: Native tools for Claude Desktop, Cursor, and Zed AI assistants
* 🤖 **CI/CD & Automation**: Run in GitHub Actions, Docker, and shell pipelines

---

## Installation

Install Imagerry CLI globally using npm:

```bash
npm install -g @imagerry/cli
```

Verify the installation:

```bash
imagerry -v
imagerry -h
```

---

## Free Tier & Licensing

Imagerry CLI includes a **Free Tier** allowing you to process up to **15 free images per day** without a license key.

For unlimited daily processing, high-volume automated batch scripts, or hosting the self-hosted `serve` API and `mcp` server, an **[Imagerry Pro](https://imagerry.com/pro)** license is required.

### Providing Pro License

#### Option A: Command-line flag
```bash
imagerry -i input.png -o output.png --license-key=YOUR_KEY
```

#### Option B: Environment variable (Recommended for CI/CD & Docker)
```bash
export IMAGERRY_LICENSE_KEY="YOUR_KEY"
```

> [!TIP]
> CLI usage does **not count against your desktop device limit**. You can use your Pro license freely across terminals, servers, Docker containers, and CI/CD pipelines.

---

## Usage Guide

### 1. Basic Usage & Formats

Process an image with default styles:
```bash
imagerry -i input.png -o output.png
```

Convert and compress to **WebP** or **JPEG** with custom quality:
```bash
# Auto-detected format from file extension
imagerry -i photo.png -o photo.webp -q 85

# Explicit format flag
imagerry -i photo.png -o result.jpg --format jpeg --quality 80
```

### 2. Built-in Presets

Imagerry includes curated visual presets (e.g. `mesh`, `gradient`, `cyberpunk`, `default`).

To list all available presets from your terminal:
```bash
imagerry --list-presets
```

All presets and configuration properties are documented in the **[JSON Schema](https://imagerry.com/schema/config.json)**.

Apply a preset:
```bash
imagerry -i app.png -o app-styled.webp --preset mesh
```

### 3. Direct CLI Styling Flags (No JSON Required)

Easily adjust individual visual parameters without touching preset files:

```bash
imagerry -i screenshot.png -o screenshot.webp \
  --preset mesh \
  --padding 40 \
  --radius 20 \
  --shadow 60 \
  --bg-color "#0a0a0a" \
  --ratio 16:9 \
  --watermark "© 2026 Acme Corp"
```

Supported direct flags:
- `--padding <number>`: Canvas padding percent (e.g. `24`, `40`, `50`)
- `--radius <number>`: Inner image corner radius (e.g. `12`, `16`, `24`)
- `--shadow <number>`: Drop shadow strength (e.g. `30`, `50`, `70`)
- `--bg-color <hex>`: Solid background color (e.g. `"#000000"`, `"#18181b"`)
- `--ratio <ratio>`: Aspect ratio (`"16:9"`, `"1:1"`, `"4:3"`, `"auto"`)
- `--watermark <text>`: Watermark text overlay
- `--title <text>` & `--subtitle <text>`: Header overlay text
- `--blur <number>`, `--contrast <number>`, `--brightness <number>`, `--saturation <number>`

### 4. Batch & Directory Processing

Process an entire folder of screenshots or images in one command:

```bash
# Process all images in a directory to ./dist
imagerry -i ./screenshots -o ./dist --preset mesh --format webp

# Batch process with wildcard pattern
imagerry --batch "assets/*.png" -o ./dist --preset gradient --quality 85

# Adjust parallel worker concurrency
imagerry -i ./raw-photos -o ./compressed --preset studio --concurrency 6
```

### 5. Project Configuration (`imagerry.config.json`)

Standardize visual branding across team members and repositories. Initialize a configuration file in your project root:

```bash
imagerry init --preset mesh
```

This creates an `imagerry.config.json`:
```json
{
  "$schema": "https://imagerry.com/schema/config.json",
  "preset": "mesh",
  "format": "webp",
  "quality": 90,
  "padding": 36,
  "borderRadius": 16,
  "shadowSize": 50,
  "canvasRatio": "auto"
}
```

Whenever you run `imagerry -i input.png -o output.webp`, the CLI automatically detects and applies your project configuration!

### 6. Custom JSON Presets

For full control over every setting, export and customize template JSON files:

```bash
# Export template
imagerry --export-preset-template=my-style.json

# Apply template
imagerry -i input.png -o output.png --preset my-style.json
```

---

## API Server

Self-host the Imagerry engine as a high-performance REST API:

```bash
imagerry serve --port 5273
```
*(Requires `IMAGERRY_LICENSE_KEY`)*

### Processing via HTTP:

```bash
curl -f -X POST http://localhost:5273/api/v1/process \
  -F "image=@/path/to/screenshot.png" \
  -F "preset=mesh" \
  -F "format=webp" \
  -F "quality=85" \
  -F "padding=40" \
  --output result.webp
```

Helpful metadata endpoints:
- `GET /presets` — List built-in presets
- `GET /template` — Full JSON template structure
- `GET /health` — Server health status and version

---

## Model Context Protocol (MCP) Server

Imagerry CLI includes a built-in MCP server enabling AI coding assistants (Claude Desktop, Cursor, Zed) to style, format, and batch-process local images autonomously.

### Starting MCP:

```bash
imagerry mcp
```

### Configuration (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "imagerry": {
      "command": "imagerry",
      "args": ["mcp"],
      "env": {
        "IMAGERRY_LICENSE_KEY": "YOUR_PRO_LICENSE_KEY"
      }
    }
  }
}
```

### Available AI Tools:
1. **`customize_image`**: Format and style single images with preset, padding, radius, format (`webp`, `jpeg`, `png`), and quality.
2. **`batch_customize_images`**: Process entire directories in bulk for agents generating assets.
3. **`list_presets`**: Query descriptions of all available styling presets.

---

## Troubleshooting & Privacy

- **Image Security**: All image manipulation is performed in-memory on your local CPU/GPU using native Skia bindings (`@napi-rs/canvas`). Your images are **never transmitted over the network**.
- **Licensing**: For support or key inquiries, contact **support@imagerry.com**.

## Links
* [Imagerry Official Website](https://imagerry.com)
* [Imagerry Pro](https://imagerry.com/pro)
* [Legal & Privacy Terms](https://imagerry.com/legal)
