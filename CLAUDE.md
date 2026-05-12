# CLAUDE.md

This file provides guidance to Claude when working with code in this repository.

## Commands

```bash
make serve      # start live-server with hot reload at http://localhost:8080
make prettier   # format all HTML files with Prettier
```

Both `live-server` and `prettier` must be installed globally:

```bash
npm install -g live-server prettier
```

## Project Overview

Static personal portfolio site for Emmet Hamell — software engineer. No build step, no bundler, no framework. Four plain HTML files served directly from the root.

| File | Purpose |
|------|---------|
| `index.html` | Landing page with animated 3D torus canvas |
| `about.html` | Bio, headshot, and work experience |
| `projects.html` | Project showcase with alternating image/text layout |
| `contact.html` | Contact form submitted via Formspree |
| `images/` | Static assets (SVG favicon, headshot, logos, project screenshots) |

## Tech Stack

- **CSS**: Tailwind CSS via CDN (`https://cdn.tailwindcss.com`) — no `tailwind.config.js` file; config is set inline as `tailwind.config = { darkMode: "class" }`
- **JS**: Alpine.js v3 via jsDelivr CDN (`defer`) — used only for dark mode state
- **Fonts**: Nunito (body) and Open Sans via Google Fonts CDN; applied as an inline `style` on `<body>`
- **Forms**: Formspree (`action="https://formspree.io/f/xvgkwoew"`, `method="POST"`) on `contact.html`
- **Canvas animation**: Vanilla JS inline script on `index.html` only

## Page Structure (all pages follow this pattern)

```html
<html lang="en" x-data="..." x-init="..." :class="...">
  <head>
    <!-- charset, favicon, Google Fonts links, viewport, title -->
    <!-- Tailwind CDN + inline config -->
    <!-- Alpine.js CDN defer -->
  </head>
  <body style="font-family: 'Nunito', sans-serif;" class="bg-white dark:bg-gray-900 ...">
    <nav>...</nav>
    <main class="max-w-5xl mx-auto px-4 sm:px-6 py-6 sm:py-10">
      <!-- page content -->
    </main>
    <footer>...</footer>
  </body>
</html>
```

## Dark Mode

Dark mode is managed entirely by Alpine.js. Every page must include this exact pattern on `<html>`:

```html
<html lang="en"
  x-data="{ dark: localStorage.getItem('dark') === 'true' }"
  x-init="$watch('dark', val => localStorage.setItem('dark', val))"
  :class="{ 'dark': dark }">
```

- Tailwind's `darkMode: "class"` config picks up the `dark` class on `<html>`.
- The toggle button in the nav uses `@click="dark = !dark"` and `x-text` to switch its label.
- Light/dark signature images use `x-show="!dark"` / `x-show="dark"` to conditionally render.
- Preference is persisted in `localStorage` and survives page navigation.

## Nav and Footer

The nav and footer are **copy-pasted verbatim** across all four pages — there is no include system or templating. When editing the nav or footer, the change must be made in all four files.

Nav contains: signature image (links to `index.html`), links to About / Projects / Contact, dark mode toggle button.

Footer contains: GitHub and LinkedIn SVG icon links, copyright line ("© 2025 Emmet Hamell"), "built with Alpine.js and TailwindCSS" credit.

## Styling Conventions

- All styling is done with **Tailwind utility classes** — no custom CSS classes, no separate stylesheet.
- Dark mode variants use the `dark:` prefix (e.g., `dark:bg-gray-900`, `dark:text-white`).
- Responsive breakpoints used: `sm:`, `md:`, `lg:`.
- Transitions on theme change: `transition-colors duration-300` on `<body>` and key elements.
- Color palette: gray-900 (dark bg), white (light bg), blue/indigo accents for links and buttons.

## Asset Paths

Images live in the `images/` directory. Some pages use **root-absolute paths** (`/images/...`) while others use **relative paths** (`images/...`). Be consistent within a page; prefer relative paths since this is a flat-file site with no subpath routing.

Project screenshots referenced in `projects.html`: `codeprep.png`, `DentalRank.png`, `StudySpace.png`, `LunchLink_Mockups.png` (inside `images/`). Some entries still use `https://picsum.photos/...` placeholders.

## Torus Animation (`index.html`)

The canvas renders a 3D dot-matrix torus using the pure Canvas 2D API. Key implementation notes:

- **Parameters**: `R` (ring radius, scales with canvas size), `r` (tube radius, fixed at 1).
- **Interaction**: Tracks mouse position via `targetRotation`; lerp-smoothed toward the mouse. Auto-rotates when the mouse is idle.
- **Breathing effect**: A `Math.sin` scalar applied to `R` makes the torus pulse slightly.
- **Depth sort**: All points are depth-sorted before drawing each frame; dot size and alpha scale with the Z depth value.
- **Theme awareness**: Reads `document.documentElement.classList.contains("dark")` each frame to choose dot color (white in dark mode, dark gray in light mode).
- The canvas is constrained with inline `style="max-width: ...; max-height: ..."` and redraws on `window.resize`.

## Known Inconsistencies

- `about.html` navbar uses `class="w-56"` for the signature image; other pages use `w-32 sm:w-40 lg:w-56` (responsive).
- `index.html` footer has an extra `mt-8`; other pages omit it.
- Several project entries in `projects.html` still contain Lorem ipsum placeholder text and picsum.photos images.
- `projects.html` asset paths are root-absolute; `about.html` experience section logo paths are also root-absolute.
