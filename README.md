# Imagerry CLI (Beta)

The official command-line interface for [Imagerry](https://imagerry.com), built for automating image processing and styling directly from your terminal.

> [!NOTE]
> **Beta Release**
> 
> Imagerry CLI is currently in beta. At this time, it only supports the **image customizer** mode with basic styling capabilities.

> [!IMPORTANT]
> **Privacy-first processing**
>
> Imagerry CLI processes images **locally on your computer** using a blazing-fast native graphics engine. Your images are **never uploaded to Imagerry or any external image-processing server**.
>
> A network connection is only used when required to verify your Imagerry Pro license.

## Features

* 🖥️ Process images directly from the terminal
* 🌐 **New:** Host a self-hosted API server with `serve`
* 🔒 Keep image processing completely local
* 🎨 Apply built-in styling presets
* ⚙️ Create and reuse custom JSON presets
* 🤖 Use in scripts, Docker, and CI/CD workflows
* 📦 Automate workflows with GitHub Actions

## Installation

Install Imagerry CLI globally using npm:

```bash
npm install -g @imagerry/cli
```

Verify the installation:

```bash
imagerry -h
```

## Licensing

Imagerry CLI requires an **Imagerry Pro** license.

You can provide your license key in either of the following ways.

### Command-line option

```bash
imagerry --input=input.png --output=output.png --license-key=YOUR_KEY
```

### Environment variable

For automation and CI/CD environments, using an environment variable is recommended:

```bash
export IMAGERRY_LICENSE_KEY="YOUR_KEY"
```

> [!TIP]
> The CLI verifies your license with the payment server, but CLI usage does **not count against your device limit**. This makes it suitable for automated environments such as GitHub Actions, Docker, and CI/CD pipelines.

## Usage

### Basic usage

Process an image using the default settings:

```bash
imagerry --input=input.png --output=output.png
```

You can also use the shorter argument aliases:

```bash
imagerry -i input.png -o output.png
```

## Built-in Presets

To view a full list of available built-in styles and their descriptions, run:

```bash
imagerry --list-presets
```

Once you've found a style you like, you can apply it using the `--preset` flag:

```bash
imagerry --input=input.png --output=output.png --preset=mesh
```

## Custom JSON Presets

For more control over the output, Imagerry supports custom JSON presets.

### 1. Export a preset template

Generate a starting configuration:

```bash
imagerry --export-preset-template=my-style.json
```

Edit `my-style.json` with your preferred settings.

### 2. Apply a preset file

```bash
imagerry \
  -i input.png \
  -o output.png \
  -m image-customizer \
  --preset my-style.json
```

### 3. Pass JSON directly

For simple configurations, you can provide the preset directly from the terminal:

```bash
imagerry \
  -i input.png \
  -o output.png \
  -m image-customizer \
  --preset '{"padding": 64, "bgColor": "#000000"}'
```

This can be useful for scripts where creating a separate preset file would add unnecessary complexity.

## Command Reference

To view all available commands, options, and examples:

```bash
imagerry -h
```

## API Server

Imagerry CLI includes a built-in Express server that allows you to self-host the engine as a REST API. You can host this on Railway, Render, or any VPS.

To start the API server:

```bash
imagerry serve --port 5273
```
*(Note: A valid `IMAGERRY_LICENSE_KEY` environment variable or `--license-key` flag is required to start the server).*

### API Example

Send a `multipart/form-data` POST request to process an image instantly over HTTP:

```bash
curl -f -X POST http://localhost:5273/api/v1/process \
  -F "image=@/path/to/your/input.png" \
  -F 'preset={"padding": 64, "bgColor": "#18181b"}' \
  --output result.png
```

> [!WARNING]
> We strongly recommend including the `-f` (or `--fail-with-body`) flag when using `curl` with `--output`. If the API returns an error, this flag prevents `curl` from saving the JSON error response directly into your output image file, which would result in a corrupted image.

### Built-in API Reference

Once the server is running, visit **`http://localhost:5273/docs`** in your browser to view the API reference.

The server also exposes a few helpful informational routes:

- `GET /presets` — Returns a JSON array of all built-in named presets.
- `GET /template` — Returns a JSON object of all available settings keys and their defaults.
- `GET /health` — Returns server health status and engine version.

## Automation

Imagerry CLI is designed to work well in automated environments.

For example, you can provide your license through an environment variable:

```bash
export IMAGERRY_LICENSE_KEY="YOUR_KEY"

imagerry \
  -i input.png \
  -o output.png \
  --preset=gradient
```

When using a CI provider, store `IMAGERRY_LICENSE_KEY` as a secret rather than committing the license key to your repository.

> [!WARNING]
> Never commit your Imagerry license key to Git or include it directly in a public workflow configuration.

## Troubleshooting

### License issues

If your license cannot be verified, double-check that your key is valid and it belongs to Imagerry. If you lost your key or need help, please contact us at **support@imagerry.com**.

## Privacy

Your source images stay on your machine or your own hosted server.

Imagerry CLI processes and renders all images locally. Images do not need to be uploaded to Imagerry or any external image-processing server.

The CLI only communicates with the licensing service when verifying your Imagerry Pro license.

## Links

* [Imagerry](https://imagerry.com)
* [Legal & Terms](https://imagerry.com/legal)
