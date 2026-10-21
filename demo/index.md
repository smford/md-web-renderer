---
title: Platform Documentation
description: Demo and feature showcase for md-web-renderer
---

# System Architecture & Platform Documentation

Welcome to the zero-build documentation engine. This `index.md` file serves as a comprehensive test suite for routing, graphical diagram rendering, scroll-spy navigation, offline caching, and full-text search.

> [!NOTE]
> Press `⌘K` (macOS) or `Ctrl+K` (Linux/Windows) at any time to open the Command Palette and search across headings and document content.

---

## 1. Graphical Diagrams (Mermaid.js)

Hover over either diagram below to test the **floating toolbar** (`+`, `−`, `↺`, `SVG`, `PNG`). Use your mouse wheel to zoom toward your cursor, click-and-drag to pan, or double-click to reset the viewport.

### Request & Caching Lifecycle

```mermaid
graph LR
    User([User Browser]) -->|Navigate #index.md| Router{Hash Router}
    Router -->|1. Check Cache| Cache[(CacheStorage API)]
    Cache -->|Hit| Render[Immediate DOM Render]
    Router -->|2. Revalidate| Network[HTTP Fetch]
    Network -->|200 OK| HashCheck{SHA-256 Changed?}
    HashCheck -->|Yes| UpdateCache[Update Cache & Re-render]
    HashCheck -->|No| Done([No-Op])
    Network -->|Offline / Error| Badge[Show Offline Badge]
```

### Sequence Topology

```mermaid
sequenceDiagram
    autonumber
    actor Dev as Engineer
    participant UI as index.html
    participant Parser as Marked + DOMPurify
    participant Graphics as Mermaid + PrismJS
    participant Indexer as Idle Search Crawler

    Dev->>UI: Opens #index.md#1-graphical-diagrams-mermaidjs
    UI->>Parser: Parse Raw Markdown & Sanitize SVG/HTML
    Parser-->>UI: Safe HTML Fragment
    UI->>Graphics: Render Vector Diagrams & Syntax Highlighting
    UI->>Indexer: Discover Outbound *.md Links
    Indexer-->>UI: Pre-warm CacheStorage & Search Map
```

---

## 2. GitHub Callout Alerts

All five GitHub-flavored blockquote alerts are parsed and styled automatically:

> [!TIP]
> Click the **Node Map** button in the top navigation bar to visualize this document's heading hierarchy and outbound `.md` links as an interactive 2D spring graph.

> [!IMPORTANT]
> Relative links to other Markdown files (such as `[Architecture Overview](architecture/overview.md)`) are intercepted automatically and converted into hash routes without full page reloads.

> [!WARNING]
> Because browsers block `fetch()` over the `file://` protocol, always serve this directory over a local HTTP server (e.g., `python3 -m http.server 8000`).

> [!CAUTION]
> Modifying DOMPurify's SVG whitelist will prevent Mermaid vector nodes from rendering properly.

---

## 3. Code Highlighting & One-Click Copy

Hover over the top-right corner of the code blocks below to test the **Copy** button and PrismJS dynamic language autoloading.

### Go Concurrent Worker Pattern

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

func worker(ctx context.Context, id int, jobs <-chan string, wg *sync.WaitGroup) {
	defer wg.Done()
	for {
		select {
		case <-ctx.Done():
			return
		case job, ok := <-jobs:
			if !ok {
				return
			}
			fmt.Printf("[worker-%d] processed: %s\n", id, job)
		}
	}
}

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel()

	jobs := make(chan string, 3)
	var wg sync.WaitGroup

	for w := 1; w <= 2; w++ {
		wg.Add(1)
		go worker(ctx, w, jobs, &wg)
	}

	jobs <- "index.md"
	jobs <- "architecture/overview.md"
	close(jobs)
	wg.Wait()
}
```

### Terraform Static Hosting Configuration

```hcl
resource "aws_s3_bucket" "docs_portal" {
  bucket_prefix = "markdown-docs-engine-"
  force_destroy = true

  tags = {
    Component   = "documentation-engine"
    Environment = "production"
  }
}

resource "aws_s3_bucket_website_configuration" "docs_site" {
  bucket = aws_s3_bucket.docs_portal.id

  index_document {
    suffix = "index.html"
  }
}
```

---

## 4. Structured Data & Deep Linking

Test intra-document deep linking by clicking this anchor to jump back to [Graphical Diagrams](#1-graphical-diagrams-mermaidjs), or hover over any heading to see its `#` permalink.

| Feature Module | Technology Used | Offline Capable |
| :--- | :--- | :--- |
| **Markdown Parser** | `marked.js` (GFM enabled) | Yes (after initial load) |
| **XSS Sanitizer** | `DOMPurify` (SVG whitelisted) | Yes |
| **Vector Graphics** | `Mermaid.js` + Custom Pan/Zoom | Yes |
| **Document Cache** | Native `window.caches` + `SubtleCrypto` | Yes (SWR lifecycle) |
| **Search Index** | In-Memory `Map` + `requestIdleCallback` | Yes (pre-warmed) |

---

## 5. Multi-File Routing & Subdirectory Links

To test multi-file navigation, the background search crawler, and the **Node Map** graph edges, create any of the linked files below in your directory:

* [Getting Started Guide](getting-started.md) — Sibling document in the same directory.
* [System Architecture Overview](architecture/overview.md) — Nested document in a subdirectory testing relative asset and path resolution.
* [API Reference: Authentication](api/reference.md#authentication) — Cross-document deep link directly into a specific section anchor.

---

## 6. New Features & Configuration Guide

Here is how to take full advantage of the newest `md-web-renderer` capabilities:

### YAML Frontmatter

Add metadata at the very beginning of any `.md` file. It will be parsed, cleanly stripped from the body, and used to customize the browser title, breadcrumb label, and search ranking:

```yaml
---
title: My Custom Document Title
description: High-level overview of the architecture
---
```

### Navigation Sidebar (`_sidebar.md` / `SUMMARY.md`)

Place a `_sidebar.md` file in your documentation directory. Use a standard markdown nested list to build your navigation hierarchy:

```markdown
* [Home](index.md)
* [Getting Started](getting-started.md)
* System Architecture
  * [Overview](architecture/overview.md)
  * [Security Model](architecture/security.md)
* [API Reference](api/reference.md)
```

The top-left **`☰`** button lets readers collapse the sidebar on desktop for a distraction-free view, or open a slide-out drawer on mobile screens.

### Light & Dark Theme Toggle

Use the **☀️ / 🌙** button in the top navigation bar to toggle themes. The theme selection is saved in `localStorage`, and all Mermaid diagrams and interactive Node Maps automatically re-render with matching high-contrast color palettes.

### Accessibility & WCAG 2.2 Standards (GDS-Inspired)

Built with design guidance and accessibility characteristics inspired by the UK Government Digital Service (GDS) Design System:

* **Skip to Main Content Link (`Tab` key):** A hidden skip link appears when tabbing into the page, allowing keyboard users to bypass navigation directly to `#main-content`.
* **High-Contrast Focus Indicators:** All interactive elements feature thick, dual-layer high-contrast focus rings (`:focus-visible`) that remain visible on any background and in Windows High Contrast / Forced Colors mode.
* **Readable Measure & Underlined Links:** Text body lines are constrained to optimal reading length (`75ch`), and links within body content feature persistent underlines with offset so color is never the sole visual cue.
* **Accessible Tables:** Markdown tables are automatically wrapped in a container with keyboard scrollability (`tabindex="0"` and `role="region"`) for horizontal exploration.
* **Reduced Motion:** Fully honors `prefers-reduced-motion: reduce` by disabling smooth scrolling, animations, and continuous canvas physics.
* **Screen Reader Live Announcements:** Dynamic actions (copying code snippets, search result counts, theme switches) are announced via an `aria-live="polite"` region.

### GitHub Actions Customization

Configure your GitHub Actions deployment with custom branding, custom domains, or custom CSS:

```yaml
- name: Deploy Docs
  uses: smford/md-web-renderer@v1
  with:
    path: 'docs'
    title: 'My Project Docs'    # Custom header brand and page title
    cname: 'docs.example.com'   # Custom domain for GitHub Pages
    custom-css: 'theme.css'     # Custom stylesheet injection
```
