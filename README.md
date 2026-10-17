# md-web-renderer

A zero-build, single-file documentation engine that turns folders of Markdown files into an interactive, modern documentation website.

---

## What Is It?

`md-web-renderer` is a standalone `index.html` file that you drop into any directory containing Markdown (`.md`) files. When served by any basic static web server or hosted on GitHub Pages, it dynamically fetches, parses, and renders your documents directly in the browser—with no compilers, build pipelines, or server-side runtimes required.

## Problems It Solves

- **Eliminates complex build toolchains:** Traditional documentation generators (e.g., Docusaurus, VitePress, MkDocs) require Node.js/Python setups, package managers, dependency trees, and build steps. `md-web-renderer` requires zero setup and zero dependencies—just a single HTML file.
- **Prevents doc & code drift:** Documentation remains pure, standard GitHub Markdown files (`.md`). They stay readable in plain text editors, GitHub repos, and IDEs without proprietary templates or component syntax.
- **Provides rich features out of the box:** You get modern docs features—syntax highlighting, interactive vector diagrams, command palette search, and table of contents—without configuring plugins or maintaining a web app.
- **Works offline & loads instantly:** Uses in-browser caching (Stale-While-Revalidate) so navigating between documents is instant and previously viewed documents remain available offline.

## Key Features

- **Zero-Build & Single File:** Entire engine is contained in `index.html` via lightweight CDN scripts.
- **GitHub Flavored Markdown:** Supports tables, task lists, and GitHub callouts (`[!NOTE]`, `[!TIP]`, `[!WARNING]`, etc.).
- **Interactive Mermaid Diagrams:** Automatically renders Mermaid diagrams with pan, zoom, reset, and one-click SVG/PNG exports.
- **Fast In-Memory Search (`⌘K` / `Ctrl+K`):** Full-text search and command palette with weighted ranking across titles, headings, and content.
- **Syntax Highlighting & One-Click Copy:** Automatic language detection and syntax highlighting with PrismJS.
- **Dynamic Table of Contents:** Scroll-spy sidebar that tracks your position and links to section anchors.
- **Document Topology Map:** Interactive 2D canvas spring graph visualizing heading hierarchies and document links.
- **Stale-While-Revalidate (SWR) Caching:** Instant transitions from local cache with background updates.

## Quick Start

1. Place [`index.html`](index.html) into your documentation folder alongside your `.md` files (such as `index.md`).
2. Start any local static web server in that folder:

   ```bash
   # Using Python 3
   python3 -m http.server 8000

   # Or using npx
   npx serve .
   ```

3. Open `http://localhost:8000` in your browser.

### Running the Included Demo

A ready-to-run demo website is provided in the [`demo/`](demo/) directory:

```bash
# Serve from repo root
python3 -m http.server 8000
# Visit http://localhost:8000/demo/

# Or serve directly inside the demo folder
cd demo && python3 -m http.server 8000
# Visit http://localhost:8000/
```

## Deploying to GitHub Pages via GitHub Actions

This repository includes an automated workflow in [`.github/workflows/deploy-pages.yml`](.github/workflows/deploy-pages.yml) that automatically deploys the demo site to GitHub Pages using `md-web-renderer` as the engine.

### Enable GitHub Pages in your repository:
1. Navigate to **Settings** > **Pages** in your GitHub repository.
2. Under **Build and deployment** > **Source**, select **GitHub Actions**.
3. Push to `main` (or run the workflow manually via the **Actions** tab).
4. GitHub Pages will build and publish your site at `https://<owner>.github.io/<repo>/`.

> **Note:** Browsers restrict asynchronous `fetch()` requests when opening files directly via `file://`. A local static HTTP server or static web host (like GitHub Pages or Netlify) is required.