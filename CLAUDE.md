# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static travel-guide website for 해솔마을, a fictional seaside village. `PRD.md` is the spec: purpose, pages, menu and link rules, and things not to build (login, booking, payment, guestbook). All user-facing text is Korean, and the user writes in Korean.

There is no build step, package manager, linter, or test suite. The site is plain HTML files plus JPG photos.

## Running and checking

- Preview locally: run `python3 -m http.server`, then open `http://localhost:8000/`. Opening the HTML files directly also works, because image paths are relative.
- Live site: GitHub Pages serves `main` from the repo root at https://nana990710san-jpg.github.io/day02-haesolwebsite/. A push to `main` redeploys it in about 1–2 minutes.
- Visual check in the cloud environment: Playwright with Chromium at `executablePath: '/opt/pw-browsers/chromium'`. Screenshot at 1280px and 390px widths. Check that no `<img>` has `naturalWidth === 0` and that `document.documentElement.scrollWidth` is not wider than the viewport.

## Architecture

- **Pages:** ten top-level files: `index`, `about`, `spots`, `food`, `festival`, `course`, `stay`, `gallery`, `map`, `faq`. Each one is a complete, self-contained document, and its CSS and JS are inline as the PRD requires.
- **Shared parts are copied, not included.** Every page carries an identical copy of the `<style>` block, the `<header>` menu, the `<footer>` contacts, and the trailing `<script>`. Any change to styling, menu, footer, or behavior must be applied to all ten files. A page differs from the others only in its `<title>`, its meta description, which menu link has `class="active" aria-current="page"`, and the contents of `<main>`.
- **Index page as template:** the pages were originally generated from `index.html` used as a template. The generator scripts were never committed. To add a page, copy an existing one and replace those per-page parts.
- **No JavaScript dependency:** interactions avoid JavaScript where possible, because previews on the user's iPad and in the Claude app can block scripts.
  - Mobile menu: CSS checkbox toggle (`#menu-toggle` + `label.menu-btn`, `.menu-toggle:checked ~ .menu`).
  - FAQ: `<details class="faq-item">` with `<summary class="faq-q">`.
  - Hero zoom: `.hero` has `tabindex="0"`. CSS `@keyframes hero-pop` runs on `:hover` and `hero-pop-click` runs on `:focus`. The small inline script only restarts the animation when the hero is clicked again.
- **Page layout:** pages with a photo hero use `<section class="hero">` with `img.hero-img` and `.hero-text`. Pages without one use `<section class="page-head">`, a gradient title band. Content is laid out with `.grid` and `article.card` (using `img.card-img`, `.tag`, `.price`), plus `.two`, `.box`, `table.info`, `.timeline`, and `.gallery`.
- **Theme:** colors are CSS custom properties on `:root`: sea blues `--sea-deep`, `--sea`, `--sea-light`, and sunset tones `--sunset`, `--sunset-light`, `--dusk`. Keep to the "blue sea + sunset" look the PRD asks for.

## Images

Photos live in `images/` as JPG, resized to at most 1200px wide (the hero is 1672px), quality about 82, named `<page>-<subject>.jpg`. User-supplied PNGs get converted with Pillow before use. Give every `<img>` real `width`/`height` attributes and Korean `alt` text, and use `loading="lazy"` below the fold. Card images are cropped to 4:3 with `object-fit: cover`; use an inline `object-position` when the subject sits off-center, as with `spots-lighthouse.jpg`. The map on `map.html` is intentionally an inline SVG diagram, not a photo.

Card and page copy must match the photos: spot, food, and stay names were renamed to fit the images. When you rename one, update every place that mentions it, including `course.html`, `faq.html`, and the `index.html` cards.

## Workflow notes

- Work is committed and pushed straight to `main`, and GitHub Pages deploys from there.
- The user works from an iPad. Their reliable way to view changes is the GitHub Pages URL in Safari, not local files.
