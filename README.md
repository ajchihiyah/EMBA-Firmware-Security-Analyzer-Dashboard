# EMBA Firmware Security Analyzer Dashboard

> A modern, interactive web dashboard that wraps EMBA's CLI output into a visually rich single-page application for firmware security analysis. Built for penetration testers, product security teams, and KRITIS compliance auditors in Germany.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [EMBA Module Mapping](#emba-module-mapping)
- [Pages & Navigation](#pages--navigation)
- [Why Germany / KRITIS](#why-germany--kritis)
- [Installation & Usage](#installation--usage)
- [Customization](#customization)
- [Technology Stack](#technology-stack)
- [Screenshots](#screenshots)
- [License](#license)

---

## Overview

[EMBA](https://github.com/e-m-b-a/emba) (Embedded Analyzer) is an open-source firmware analysis framework used by security professionals worldwide. While EMBA generates excellent CLI reports, the default web interface is minimal. This dashboard transforms EMBA's raw output into a **production-grade, dark-themed security cockpit** with:

- Real-time risk scoring and CVSS visualization
- Interactive SBOM tables with vulnerability correlation
- CVE timelines with BSI-WID (German BSI Warning ID) references
- Hardcoded credential discovery with severity classification
- Binary analysis with dangerous function detection
- QEMU emulation test results with terminal-style logs
- EU Cyber Resilience Act (CRA) and NIS2 compliance tracking
- Report generation in multiple formats (HTML, PDF, SARIF, CycloneDX SBOM, VEX)

---

## Features

### Executive Summary
| Feature | Description |
|---------|-------------|
| **KPI Cards** | Critical findings, high CVEs, SBOM component count, security grade (F) with trend indicators vs. baseline |
| **Risk Gauge** | Animated SVG arc gauge (0–10) with gradient coloring (green → yellow → red) |
| **Module Matrix** | 36 EMBA modules color-coded by status (pass/warn/fail/info) with hover tooltips and filter tabs |

### SBOM Analysis
| Feature | Description |
|---------|-------------|
| **Component Table** | 10+ components with version, type (kernel/library/binary), license, CVE count, and EOL status |
| **Live Filtering** | Text search + status filters (All / Vulnerable / EOL / Current) |
| **Statistics** | Total components, vulnerable count, outdated count, up-to-date count |

### CVE Timeline
| Feature | Description |
|---------|-------------|
| **Chronological View** | 7 CVEs from 2024–2026 with severity dots and connecting timeline line |
| **Year Filtering** | Filter by disclosure year (All / 2026 / 2025 / 2024) |
| **BSI Integration** | German BSI-WID identifiers for each CVE |
| **Exploit Tracking** | Metasploit module, PoC availability, and exploit status badges |

### Hardcoded Credentials
| Feature | Description |
|---------|-------------|
| **Credential Types** | Passwords, RSA keys, API tokens, TLS pre-shared keys, self-signed certificates |
| **Type Filtering** | Filter by credential category (All / Passwords / Keys / Tokens / Certificates) |
| **File Paths** | Exact filesystem locations with monospace font for clarity |
| **Severity** | CRITICAL and HIGH severity badges |

### Binary Analysis
| Feature | Description |
|---------|-------------|
| **Binary Cards** | 6 binaries with architecture, compiler, size, strip status, and PIE/NX flags |
| **Dangerous Functions** | Color-coded tags for `strcpy`, `sprintf`, `gets`, `system`, `popen`, `memcpy` |
| **Risk Score** | 0–10 risk bar with gradient fill per binary |

### Emulation Tests
| Feature | Description |
|---------|-------------|
| **QEMU Results** | 4 test scenarios: system boot, network fuzzing, SSH brute force, path traversal |
| **Terminal Logs** | Color-coded log output (OK green, WARN yellow, ERROR red, INFO blue) |
| **Status Indicators** | Pass/fail/warn dots with glow effects |

### Compliance (NIS2 / CRA)
| Feature | Description |
|---------|-------------|
| **CRA Banner** | EU Cyber Resilience Act classification (Class I Important) with compliance status |
| **Metrics** | SBOM coverage %, VEX status, patch cadence (days), NIS2 impact level |
| **NIS2 Matrix** | Article 21 requirements with compliance status, finding counts, and remediation actions |

### Report Generator
| Feature | Description |
|---------|-------------|
| **Format Selection** | HTML, PDF, CycloneDX SBOM, VEX, SARIF, Raw JSON |
| **Progress Animation** | Simulated server-side generation with animated progress bar |
| **Download Links** | Generated file links with timestamps |

---

## Architecture

```
emba_dashboard.html          # Single self-contained file (~82 KB)
├── <head>
│   ├── Google Fonts (Inter, JetBrains Mono)
│   └── CSS (~1,200 lines, custom properties, responsive breakpoints)
├── <body>
│   ├── Header (logo, KRITIS/CRA badges, scan status, firmware info)
│   ├── Sidebar (8 navigation items, grouped by category)
│   ├── Content Area (8 page sections, CSS-display toggled)
│   └── <script> (~200 lines, vanilla JS, no frameworks)
└── No external dependencies beyond fonts
```

### Page Routing
```javascript
// Client-side SPA routing — no page reloads
function switchPage(pageId, navEl) {
  // 1. Update sidebar active state
  // 2. Hide all .page-section elements
  // 3. Show target page with fade-in animation
  // 4. Scroll to top
}
```

### State Management
All filter states are stored in module-level variables (e.g., `currentSbomFilter`, `currentCredFilter`) — no external state library needed.

---

## EMBA Module Mapping

| Dashboard Section | EMBA Modules | Description |
|-------------------|--------------|-------------|
| **System Info** | P00–P25 | Firmware extraction, kernel analysis, bootloader checks |
| **CVE Search** | S10, S120 | CVE database correlation and aggregation |
| **Binary Analysis** | S15, S20 | Radare2 decompilation, dangerous function detection |
| **Credential Hunt** | S65 | Hardcoded passwords, keys, tokens discovery |
| **SBOM Generation** | S70, S75 | CycloneDX/SPDX generation, license audit |
| **Emulation** | S40, S45, L10–L30 | QEMU system emulation, network/service/web tests |
| **Compliance** | F50 | Report generation and compliance overlay |

---

## Pages & Navigation

| Sidebar Section | Page ID | Content |
|-----------------|---------|---------|
| **Overview** | | |
| Executive Summary | `overview` | KPIs, risk gauge, module matrix |
| SBOM Analysis | `sbom` | Component table, statistics |
| CVE Timeline | `cve` | Vulnerability timeline, year filter |
| **Findings** | | |
| Hardcoded Creds | `credentials` | Password/key/token/cert cards |
| Binary Analysis | `binaries` | ELF binary risk analysis |
| Emulation Tests | `emulation` | QEMU test results with logs |
| **Compliance** | | |
| NIS2 / CRA | `compliance` | EU compliance matrix and metrics |
| Generate Report | `report` | Multi-format report generator |

---

## Why Germany / KRITIS

This dashboard is designed specifically for the German cybersecurity landscape:

| Context | Implementation |
|---------|----------------|
| **KRITIS** | Header badge + energy sector example (SMA Solar inverter) + NIS2 HIGH impact rating |
| **BSI-WID** | Every CVE includes a German BSI Warning ID (e.g., `BSI-2026-0612-1`) |
| **CRA Class I** | Firmware classified as "Important" under EU Cyber Resilience Act |
| **ENISA Deadline** | Compliance banner shows reporting deadline (Sept 11, 2026) |
| **German Manufacturers** | Demo target: SMA Sunny Boy inverter firmware — representative of German embedded device ecosystem (SMA, Siemens, ABB, etc.) |
| **NIS2 Article 21** | Full compliance matrix with 7 requirements, gap analysis, and remediation |

---

## Installation & Usage

### Option 1: Standalone HTML (Recommended)

```bash
# Simply open the file in any modern browser
open emba_dashboard.html        # macOS
xdg-open emba_dashboard.html      # Linux
start emba_dashboard.html       # Windows
```

No build step, no server, no dependencies. Works offline in air-gapped environments.

### Option 2: Serve via HTTP

```bash
# Python 3
python -m http.server 8080

# Node.js
npx serve .

# Then navigate to http://localhost:8080/emba_dashboard.html
```

### Option 3: Integrate with EMBA

1. Place `emba_dashboard.html` in your EMBA output directory
2. Modify the `<script>` section to load `emba_logs/html-report/style.css` data
3. Or use EMBA's `-F` flag to generate compatible JSON and inject it into the dashboard

---

## Customization

### Changing the Demo Firmware

Edit the header firmware info:

```html
<div class="fw-info">
  Firmware: <span>YOUR_FIRMWARE.bin</span> 
  &middot; Size: <span>XX.X MB</span> 
  &middot; Arch: <span>YOUR_ARCH</span>
</div>
```

### Adding New CVEs

Duplicate a `.timeline-item` block in the CVE page:

```html
<div class="timeline-item" data-year="2026">
  <div class="timeline-dot critical"></div>
  <div class="timeline-content">
    <div class="timeline-header">
      <span class="cve-id">CVE-2026-XXXX</span>
      <span class="cve-score score-critical">CVSS X.X</span>
    </div>
    <div class="cve-desc">Your description here.</div>
    <div class="cve-meta">
      <span>Published: YYYY-MM-DD</span>
      <span>Component: name version</span>
    </div>
  </div>
</div>
```

### Adding New SBOM Components

Add a `<tr>` to `#sbomTableBody` with `data-status` attribute:

```html
<tr data-status="outdated vulnerable">
  <td><strong>Component Name</strong></td>
  <td>1.0.0</td>
  <td><span class="component-type type-binary">Binary</span></td>
  <td>MIT</td>
  <td><span class="cve-badge cve-high">5 CVEs</span></td>
  <td><span style="color: var(--accent-orange); font-weight:600;">Outdated</span></td>
  <td>2023-01-01</td>
</tr>
```

### Theming

All colors are CSS custom properties in `:root`:

```css
:root {
  --bg-primary: #0a0e17;      /* Main background */
  --bg-secondary: #111827;     /* Card background */
  --accent-red: #ef4444;       /* Critical severity */
  --accent-orange: #f97316;    /* High severity */
  --accent-yellow: #eab308;    /* Medium severity */
  --accent-green: #22c55e;     /* Low / pass */
  --accent-blue: #3b82f6;      /* Info / links */
  --accent-cyan: #06b6d4;      /* File paths */
  --accent-purple: #a855f7;    /* CRA / compliance */
}
```

---

## Technology Stack

| Layer | Technology | Rationale |
|-------|------------|-----------|
| **Markup** | HTML5 | Semantic, accessible, no framework lock-in |
| **Styling** | Vanilla CSS | Custom properties, Grid, Flexbox, media queries |
| **Typography** | Inter + JetBrains Mono | Professional readability; monospace for technical data |
| **Scripting** | Vanilla JavaScript | Zero dependencies, works offline, fast load |
| **Icons** | Unicode emoji | No icon font dependencies, works everywhere |
| **Charts** | Inline SVG | Risk gauge with gradient arcs, no charting library |

### Browser Support

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

---

## Screenshots

> *Note: Screenshots would be added here in a real repository. The dashboard renders as a dark-themed security cockpit with the following visual hierarchy:*

1. **Header**: Red gradient logo icon, "EMBA Security Dashboard" title, KRITIS and CRA badges, green pulsing scan status dot
2. **Sidebar**: Dark navy panel with 8 navigation items grouped under "Overview", "Findings", "Compliance"
3. **KPI Cards**: 4 cards with top gradient bars (red, orange, yellow, blue) and large metric numbers
4. **Risk Gauge**: Half-circle SVG arc with green-yellow-red gradient, "9.2" score in red
5. **Module Grid**: 36 colored squares (green/yellow/red/blue) in a 6×6 grid with hover tooltips
6. **CVE Timeline**: Vertical line with colored dots, each entry showing CVE ID, CVSS score, description, and metadata
7. **Credential Cards**: 2-column grid with icon, file path, credential value, and severity badge
8. **Compliance Banner**: Purple gradient card with "NOT COMPLIANT" status and 4 metric boxes

---

## License

This dashboard is provided as a reference implementation for EMBA firmware analysis visualization. It is not affiliated with the official EMBA project. Use it as a starting point for your own security reporting infrastructure.

The EMBA project itself is licensed under [GPL-3.0](https://github.com/e-m-b-a/emba/blob/master/LICENSE).

---

## Acknowledgments

- [EMBA](https://github.com/e-m-b-a/emba) — The open-source firmware analysis framework
- [BSI](https://www.bsi.bund.de/) — German Federal Office for Information Security
- [ENISA](https://www.enisa.europa.eu/) — European Union Agency for Cybersecurity
- [CycloneDX](https://cyclonedx.org/) — SBOM standard
- [SARIF](https://sarifweb.azurewebsites.net/) — Static Analysis Results Interchange Format

---

*Built for penetration testers, product security engineers, and KRITIS compliance teams analyzing embedded firmware in critical infrastructure environments.*
