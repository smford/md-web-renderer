# md-web-renderer

A zero-build, single-file documentation engine that turns folders of Markdown files into an interactive, modern documentation website.

---

## What Is It?

`md-web-renderer` is a standalone `index.html` file that you drop into any directory containing Markdown (`.md`) files. When served by any basic static web server or hosted on GitHub Pages, it dynamically fetches, parses, and renders your documents directly in the browser—with no compilers, build pipelines, or server-side runtimes required.

## Problems It Solves

- **Eliminates complex build toolchains:** Traditional documentation generators (e.g., Docusaurus, VitePress, MkDocs) require Node.js/Python setups, package managers, dependency trees, and build steps. `md-web-renderer` requires zero setup and zero dependencies—just a single HTML file.
- **Prevents doc & code drift:** Documentation remains pure, standard GitHub Markdown files (`.md`). They stay readable in plain text editors, GitHub repos, and IDEs without proprietary templates or component syntax.
- **Provides rich features out of the box:** You get modern docs features—syntax highlighting, interactive vector diagrams, command palette search, table of contents, and sidebar navigation—without configuring plugins or maintaining a web app.
- **Works offline & loads instantly:** Uses in-browser caching (Stale-While-Revalidate) so navigating between documents is instant and previously viewed documents remain available offline.

## Key Features

- **Zero-Build & Single File:** Entire engine is contained in `index.html` via lightweight CDN scripts.
- **Accessibility & WCAG 2.2 Standards:** Designed using guidance from the GOV.UK (GDS) Design System—features keyboard skip links, high-contrast focus rings, underlined links, readable measure (75ch), keyboard-scrollable tables, reduced-motion honor, and screen-reader live announcements.
- **YAML Frontmatter Support:** Automatically strips frontmatter (`title:`, `description:`) and uses it for browser tab titles and breadcrumbs.
- **Navigation Sidebar (`_sidebar.md` / `SUMMARY.md`):** Optional left navigation tree (Docsify/GitBook style) with active link tracking and mobile drawer.
- **Dark / Light Mode Switcher:** One-click header toggle with local persistence and adaptive Mermaid diagrams.
- **GitHub Flavored Markdown:** Supports tables, task lists, and GitHub callouts (`[!NOTE]`, `[!TIP]`, `[!WARNING]`, etc.).
- **LaTeX Mathematical Formulas (KaTeX):** Full support for inline TeX formulas (`$formula$`) and display equation blocks (`$$formula$$`) with accessible MathML typography.
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

## Using in Any GitHub Repository

You can easily publish interactive documentation for any repository using `md-web-renderer` via GitHub Actions without installing or building anything.

### Option 1: Reusable Workflow (Zero Configuration - Recommended)

Create `.github/workflows/docs.yml` in your repository:

```yaml
name: Deploy Documentation

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  deploy:
    uses: smford/md-web-renderer/.github/workflows/deploy.yml@v1
    with:
      path: 'docs'               # Path to markdown folder (defaults to '.' for root)
      title: 'My Project Docs'   # Optional: custom site title & brand name
      cname: 'docs.example.com'  # Optional: custom domain for GitHub Pages
      fallback-readme: true      # Automatically fallback to README.md if index.md is missing
```

### Option 2: GitHub Action Step (For Custom Workflows)

If you have an existing workflow or build pipeline, add `md-web-renderer` as a step:

```yaml
- name: Setup Documentation with md-web-renderer
  uses: smford/md-web-renderer@v1
  with:
    path: 'docs'                 # Path to markdown directory (defaults to '.')
    title: 'My Project Docs'     # Optional: custom site title
    cname: 'docs.example.com'    # Optional: custom domain (CNAME)
    custom-css: 'docs/theme.css' # Optional: inject custom stylesheet
    fallback-readme: 'true'

- name: Deploy to GitHub Pages
  uses: actions/deploy-pages@v4
```

### Option 3: Direct Download via GitHub Releases

For manual deployments, local servers, or non-GitHub CI:
Download `index.html` from the [Latest Release](https://github.com/smford/md-web-renderer/releases/latest) directly:

```bash
# Download index.html directly into your docs directory
curl -sSL https://github.com/smford/md-web-renderer/releases/latest/download/index.html -o docs/index.html
touch docs/.nojekyll
```

---

## Organizing Multi-Page Docs (`_sidebar.md`)

To display a persistent left navigation sidebar, add a `_sidebar.md` (or `SUMMARY.md`) to your docs directory with a standard markdown list:

```markdown
* [Home](index.md)
* [Getting Started](getting-started.md)
* Architecture
  * [System Overview](architecture/overview.md)
  * [Security Model](architecture/security.md)
* [API Reference](api/reference.md)
```

`md-web-renderer` will automatically render the collapsible sidebar, track the current page, and provide a mobile drawer toggle.

---

## Enabling GitHub Pages in Your Repository

1. Navigate to **Settings** > **Pages** in your GitHub repository.
2. Under **Build and deployment** > **Source**, select **GitHub Actions**.
3. Push changes to `main` (or run the workflow manually via the **Actions** tab).
4. GitHub Pages will publish your interactive documentation at `https://<owner>.github.io/<repo>/`.

> **Note:** Browsers restrict asynchronous `fetch()` requests when opening files directly via `file://`. A local static HTTP server or static web host (like GitHub Pages or Netlify) is required.
