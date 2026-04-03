# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-file static HTML dashboard (`dashboard_accompagnement.html`) for **Asterion Accompagnement**, a French impact-focused VC/accelerator. No build system, no dependencies beyond Chart.js loaded from CDN. Open the file directly in a browser.

## Architecture

Everything lives in one file, organized in three sections:

1. **CSS** (`<style>` block, lines ~8–195) — CSS custom properties define the design system (`--orange: #E8590C`, `--violet: #6B3FA0`, etc.). All components (`.kpi`, `.card`, `.pill`, `.hbar-*`, `.em-card`, `.event-card`) use these tokens.

2. **HTML** (`<body>`, lines ~197–~750) — Five `<div class="page">` sections, one per tab: `overview`, `commercial`, `newsletters`, `events`, `board`. Navigation buttons call `showPage(id)`. All data displays are either static HTML or containers populated by JS (canvas for charts, `id`-targeted elements for dynamic KPIs/tables).

3. **JavaScript** (`<script>` block, lines ~750–end) — Structure:
   - **Data constants** at the top: `COMMERCIAL`, `NL_DETAIL`, `NL_MONTHLY`, `EVENTS`, `EM_DATA`, etc. — all data is hardcoded here.
   - **`mkChart(id, type, labels, datasets, options)`** — thin wrapper around `new Chart()` that destroys any existing chart on that canvas before creating a new one.
   - **`renderHBars(id, data, color)`** — renders horizontal bar lists into `.hbar-list` elements.
   - **`showPage(id)`** — toggles `.active` on nav buttons and page divs, then calls the relevant `render*()` function.
   - One `render*()` function per page (`renderCommercial()`, `renderNL()`, `renderEvents()`, `renderBoard()`); overview charts are initialized directly in `init()`.
   - **`init()`** called on `DOMContentLoaded`.

## Data updates

All data is inline JS. To update figures, find the relevant constant near the top of the `<script>` block:
- `COMMERCIAL` — intro pipeline data per startup
- `NL_DETAIL` — per-startup per-month newsletter records (fields: `startup`, `mois`, `moisLabel`, `date`, `format`, `alerte`, `fonds`)
- `NL_MONTHLY` — aggregated monthly newsletter stats
- `EVENTS` — event cards data
- `EM_DATA` — "Entreprise à mission" participation status

The header date (`Dashboard Accompagnement · 24 mars 2026`) is a static string in the `<header>` — update it manually when refreshing the dashboard.

## Design tokens

| Token | Value | Usage |
|---|---|---|
| `--orange` | `#E8590C` | Primary accent, active states |
| `--violet` | `#6B3FA0` | Secondary accent |
| `--green/yellow/red` | status colors | Pill statuses: OK / Retard / Non reçue |
| `--surface/surface2/surface3` | dark greys | Card backgrounds |

Pills use classes: `pill-ok`, `pill-warn`, `pill-err`, `pill-orange`, `pill-violet`, `pill-grey`.
