# Getting Started

[← Back to Home](index.md)

Welcome to **md-web-renderer**! This guide walks you through setting up and organizing your documentation.

---

## 1. Quick Setup

1. Copy [`index.html`](index.html) into your project or docs directory.
2. Create an `index.md` file as your landing page.
3. Serve the directory with any static HTTP server or push to GitHub Pages.

```bash
# Start a local preview server
python3 -m http.server 8000
```

---

## 2. Adding Pages and Links

Any Markdown file in your directory can be linked using standard relative Markdown links:

* **Sibling files:** `[Overview](architecture/overview.md)`
* **Section anchors:** `[Diagrams](index.md#1-graphical-diagrams-mermaidjs)`
* **Parent directory traversal:** `[Home](../index.md)`

The client-side router automatically intercepts relative `.md` links and manages the browser URL hash without full page reloads.

---

## 3. GitHub Pages Deployment

This repository includes an automated GitHub Actions workflow (`.github/workflows/deploy-pages.yml`).

To enable it:
1. In your GitHub repository, go to **Settings** > **Pages**.
2. Under **Build and deployment** > **Source**, choose **GitHub Actions**.
3. Push changes to `main`, and GitHub Actions will automatically deploy your documentation site.
