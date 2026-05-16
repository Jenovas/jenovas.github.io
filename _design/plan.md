# Engineering Atlas Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Implement the Engineering Atlas visual redesign defined in `_design/design.md` across the existing Jekyll + Beautiful Jekyll site, in ordered, reviewable chunks with browser-verification gates between each chunk.

**Architecture:** Token-driven CSS layered on the existing Beautiful Jekyll theme. All visual values move into a single `:root` token block in `assets/css/custom-styles.css`. Inline `<style>` blocks in `_layouts/default.html` and `_layouts/post.html` are consolidated into the same CSS file so there is one source of truth. Layout markup changes happen in `_includes/header.html`, `_includes/footer.html`, and per-page content files (`index.md`, `about.markdown`, `cv.md`, `projects.md`, `blog.md`). No JS framework added. Beautiful Jekyll variables in `_config.yml` are realigned so the upstream theme stops fighting the custom styles.

**Tech Stack:** Jekyll, Beautiful Jekyll theme, Bootstrap 4.5.2 utilities, FontAwesome 5.15.1, Google Fonts (Fraunces, IBM Plex Sans, IBM Plex Mono). Verification: Chrome (Claude-in-Chrome MCP), Lighthouse via Chrome DevTools.

**Branch & worktree:** before starting, follow `superpowers:using-git-worktrees` to isolate the work. The redesign touches many files and benefits from a clean branch.

**Source of truth for visual decisions:** `_design/design.md`. This plan does not redefine tokens — it applies them. Where the plan shows code, the values trace back to the design doc.

**Verification model (since there's no test framework):** every task ends with a browser check in the Claude-in-Chrome session against `bundle exec jekyll serve` running locally on `http://localhost:4000`. The check is concrete: "navigate to X, screenshot, confirm Y is true." If the check fails, the step is not complete.

**Commit cadence:** one commit per task minimum. Some tasks have an explicit second commit when verification finds issues that justify a separate fix.

---

## Pre-flight

- [ ] **P.1: Confirm Jekyll dev server runs**

Run: `bundle exec jekyll serve --livereload`
Expected: server starts on `http://localhost:4000`. If Gemfile.lock or vendor/bundle issues, run `bundle install` first.

- [ ] **P.2: Confirm worktree**

If not already in an isolated worktree, run the `superpowers:using-git-worktrees` skill to set one up. Branch name: `redesign/engineering-atlas`.

- [ ] **P.3: Snapshot the current site**

In the Claude-in-Chrome session, navigate to `http://localhost:4000/` and capture screenshots at 1440×900 and 390×844 for each page: `/`, `/about/`, `/cv/`, `/projects/`, `/blog/`, and one post. Save filenames `before-{page}-{width}.png`. These are the "before" reference for the final verification.

---

## File-touch map

Reference for which files each task touches. Cross-reference for any conflicts.

| File | Tasks that touch it |
| --- | --- |
| `_config.yml` | T1, T20 |
| `_layouts/default.html` | T1, T2, T4, T5, T6, T19 |
| `_layouts/page.html` | T6 (minor) |
| `_layouts/post.html` | T2, T18 |
| `_layouts/home.html` | T2 (cleanup) |
| `_includes/header.html` | T3, T7, T12 |
| `_includes/footer.html` | T20 |
| `_includes/cleanup.html` | T2 (remove obsolete script tag) |
| `assets/css/custom-styles.css` | T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T11, T12, T19, T20 |
| `assets/js/custom-scripts.js` | T19 (optional) |
| `assets/img/profile.jpg`, `assets/img/avatar.jpg` | T14 (real image or fallback) |
| `index.md` | T13 |
| `about.markdown` | T14 |
| `cv.md` | T15 |
| `projects.md` | T16 |
| `blog.md` | T17 |
| `404.html` | T22 (touch-up) |

---

## Task 1: Foundation — Wire new fonts and design tokens

**Files:**
- Modify: `_layouts/default.html:19-22` (Google Fonts link)
- Modify: `_layouts/default.html:31-268` (inline `<style>` block — replace `:root` block here AND remove duplicates)
- Modify: `assets/css/custom-styles.css:1-14` (replace `:root` block)

- [ ] **Step 1.1: Replace Google Fonts request**

In `_layouts/default.html`, replace the existing Google Fonts `<link>` (currently loads `Inter`) with the publication trio:

```html
<!-- Google Fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght,SOFT,WONK@9..144,400;9..144,600;9..144,900;9..144,400,100,0;9..144,600,100,0&family=IBM+Plex+Mono:wght@400;500&family=IBM+Plex+Sans:wght@400;500;600&display=swap" rel="stylesheet">
```

Verification: reload `http://localhost:4000`, open DevTools → Network → filter "fonts" → confirm 3 font families return 200.

- [ ] **Step 1.2: Replace `:root` tokens in `assets/css/custom-styles.css`**

Replace the existing block (lines 1–14):

```css
:root {
  /* Surfaces */
  --surface-0: #070A12;
  --surface-1: #0F141F;
  --surface-2: #161C2A;
  --surface-3: #1C2335;
  --border-subtle: #1E2536;
  --border-strong: #2A3247;

  /* Text */
  --text-primary: #ECEFF6;
  --text-secondary: #94A0B8;
  --text-muted: #5F6A85;
  --text-on-primary: #070A12;

  /* Primary blue */
  --primary: #5E80F2;
  --primary-hover: #7A98FF;
  --primary-pressed: #4866D6;
  --primary-soft: rgba(94, 128, 242, 0.10);
  --primary-ring: rgba(94, 128, 242, 0.45);

  /* Signal amber */
  --signal: #F5B73B;
  --signal-soft: rgba(245, 183, 59, 0.12);
  --signal-ink: #FFD06E;

  /* Shadows */
  --shadow-sm: 0 1px 0 rgba(255,255,255,0.04) inset, 0 1px 2px rgba(0,0,0,0.4);
  --shadow-md: 0 1px 0 rgba(255,255,255,0.05) inset, 0 2px 4px rgba(0,0,0,0.4), 0 12px 32px -12px rgba(0,0,0,0.55);
  --shadow-lg: 0 1px 0 rgba(255,255,255,0.06) inset, 0 8px 16px -4px rgba(0,0,0,0.5), 0 32px 64px -20px rgba(0,0,0,0.7);

  /* Radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-pill: 999px;

  /* Spacing scale */
  --space-1: 4px;  --space-2: 8px;  --space-3: 12px;
  --space-4: 16px; --space-5: 24px; --space-6: 32px;
  --space-7: 48px; --space-8: 64px; --space-9: 96px;
  --space-10: 128px;

  /* Motion */
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);
  --duration-fast: 120ms;
  --duration-base: 220ms;
  --duration-slow: 420ms;

  /* Font stacks */
  --font-display: 'Fraunces', Georgia, 'Times New Roman', serif;
  --font-body: 'IBM Plex Sans', system-ui, -apple-system, 'Segoe UI', sans-serif;
  --font-mono: 'IBM Plex Mono', 'JetBrains Mono', ui-monospace, 'SFMono-Regular', Menlo, Consolas, monospace;

  /* Legacy aliases — kept temporarily so unrefactored selectors still resolve.
     Removed in T7 once all consumers move to the new tokens. */
  --primary-color: var(--primary);
  --primary-hover-legacy: var(--primary-hover);
  --dark-bg: var(--surface-0);
  --dark-surface: var(--surface-1);
  --dark-surface-hover: var(--surface-2);
  --dark-border: var(--border-strong);
}
```

The legacy aliases at the bottom are deliberate. They let the rest of the CSS keep resolving while later tasks migrate selectors one component at a time. They're deleted in Task 7.

- [ ] **Step 1.3: Delete the duplicate `:root` block in `_layouts/default.html`**

In `_layouts/default.html` (lines 33–42), remove the inline `:root` block entirely. It's superseded by the one in `custom-styles.css` and only causes confusion.

While there, change the body `font-family` reference (line 47):

```css
body {
  background-color: var(--surface-0);
  color: var(--text-primary);
  font-family: var(--font-body);
  padding-top: 56px !important;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
```

- [ ] **Step 1.4: Reload and verify**

Reload `http://localhost:4000`. Open DevTools → Computed → on `<body>`:
- `font-family` resolves to `"IBM Plex Sans", system-ui, …`
- `background-color: rgb(7, 10, 18)` (the new `--surface-0`)

Headings still render with the old type scale — that's expected; Task 7 swaps in Fraunces.

- [ ] **Step 1.5: Commit**

```bash
git add _layouts/default.html assets/css/custom-styles.css
git commit -m "redesign: wire new design tokens and font trio"
```

---

## Task 2: Foundation — Consolidate stylesheets, purge light-theme leftovers

**Why:** styles live in three places (`custom-styles.css`, inline `<style>` in `default.html`, inline `<style>` in `post.html`). Light-theme color values pepper the CSS file. This task makes `custom-styles.css` the single source of truth and removes the light values.

**Files:**
- Modify: `_layouts/default.html` (delete inline `<style>` block lines 31–268 after migrating useful rules)
- Modify: `_layouts/post.html` (delete inline `<style>` block lines 39–144 after migrating useful rules)
- Modify: `_layouts/home.html` (remove `.post-preview` class from `<div>` on line 9 since the class will be deleted)
- Modify: `assets/css/custom-styles.css` (delete light-theme rules; merge in useful migrated rules)
- Modify: `_includes/cleanup.html` — remove obsolete `a:contains()` jQuery-style query that doesn't exist as a valid selector in vanilla JS (line 16). It's silently throwing and doing nothing. Drop it.

- [ ] **Step 2.1: Migrate useful rules from `_layouts/default.html` inline `<style>`**

Most rules in that inline block duplicate rules already in `custom-styles.css`. Migrate only the parts that do not exist in `custom-styles.css`:

- Body `display: flex; flex-direction: column; min-height: 100vh;`
- `main { flex: 1 0 auto; }`

Append to `assets/css/custom-styles.css` (near the existing body-level rules around line 1):

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

main {
  flex: 1 0 auto;
}
```

Then delete the entire inline `<style>` block in `_layouts/default.html` (lines 31–268, inclusive of the closing `</style>`).

- [ ] **Step 2.2: Migrate useful rules from `_layouts/post.html` inline `<style>`**

The inline block holds `.post-container`, `.post-title`, `.post-meta`, `.post-tags`, `.post-tag`, `.post-content`, `.post-content pre/code/blockquote/img`, and `.header-section { display: none }`.

We rebuild post styling cleanly in Task 18. For now, migrate ONLY the `.header-section { display: none }` rule (otherwise post pages get a duplicate page-title from `_includes/header.html`):

Add to `assets/css/custom-styles.css` (anywhere; group with header rules near line 168):

```css
/* On post pages, header.html's auto-rendered .header-section is replaced
   by the post-specific header inside the post layout. */
.post-page .header-section { display: none; }
```

Then change `_layouts/post.html` line 2–3 to add a `post-page` body class. The Beautiful Jekyll default doesn't have body-class hooks, so target the `<article>`'s ancestor instead — simplest path is to keep the existing `.header-section { display: none }` rule, scoped to the post layout's container:

Instead of the per-page-class approach, use the existing structure: keep the `display: none` rule scoped to a wrapper. Replace the migration with:

```css
/* Hide auto-rendered .header-section inside the post layout */
.post-container ~ .header-section,
.post-container .header-section {
  display: none;
}
```

But the cleanest fix is moving the `display: none` into the `<style>` removal: delete the whole inline `<style>` block in `_layouts/post.html` (lines 39–144), then update `header.html` in Task 3 so it does NOT emit `.header-section` on post pages by checking `{% if layout.layout != 'post' %}` (this happens in Task 12).

**For this step, do this minimal migration:**

Append to `assets/css/custom-styles.css`:

```css
/* Temporary: hide default page header inside post pages until Task 12
   gives the header.html include a layout-aware emission rule. */
.post .header-section,
body:has(.post) .header-section {
  display: none;
}
```

Then delete the inline `<style>` block in `_layouts/post.html` (lines 39–144).

Result: posts render with the temporarily-styled fallback. They'll look ugly until Task 18 — that's expected, and explicitly called out in the verification step.

- [ ] **Step 2.3: Purge light-theme rules from `custom-styles.css`**

Delete the following blocks entirely (line ranges are approximate — search by selector and verify):

| Selector | Approx line | Action |
| --- | --- | --- |
| `.post-preview` and its `.post-title`, `.post-subtitle`, `.post-meta`, `.post-entry` children | 482–515 | Delete entirely (home layout will use a different markup in T13) |
| Second `.header-section` block with `#f8f9fa` | 547–556 | Delete entirely |
| `.page-heading h1`, `.page-heading hr.small`, `.page-subheading` | 558–574 | Delete entirely |
| Second `footer` block with `#f8f9fa` | 577–603 | Delete entirely |
| `.collapsible`, `.collapsible:hover`, `.collapsible:after`, `.active:after`, `.collapsible-content`, `.collapsible.active + .collapsible-content` | 731–781 | Delete entirely (collapsibles aren't used in current content) |
| `.copy-button` (all variants), `.full-code-button` (all variants), `.full-code-container .code-header` | 793–865 | Delete entirely |
| `.post-title`, `.post-subtitle`, `.post-meta`, `.post-content`, `.post-tags`, `.post-tags a` (the light-theme versions) | 677–728 | Delete entirely (rebuilt cleanly in T18) |
| `.project-card .project-tag` | 660–665 | Delete entirely (replaced in T9) |
| Duplicate `@media (max-width: 767px)` block at the very end | 1630–1723 | Delete entirely; the earlier mobile block (1479–1566) is kept |

Use Grep to locate each before deleting:

Search patterns (run via Grep tool, output_mode `content`):
- `#fff[^a-zA-Z]`
- `#f8f9fa`
- `#e9ecef`
- `#343a40`
- `#6c757d`
- `#7f8c8d`
- `#95a5a6`
- `#0069d9`
- `#cce5ff`
- `#e9f5ff`

Each remaining hit must either be deleted or replaced with a token. After this step, there should be **zero matches** for these patterns in `assets/css/custom-styles.css`.

- [ ] **Step 2.4: Clean `_includes/cleanup.html`**

Remove lines 15–19 (the `a:contains("My Portfolio")` block — `:contains()` is not a valid CSS selector in `querySelectorAll`, so this is dead code). Leave the rest of the file as-is.

- [ ] **Step 2.5: Reload and verify**

Reload `http://localhost:4000/`. Verify:
- No console errors in DevTools.
- Site still renders. The look will be visibly broken in places (the blog post page will look unstyled — *expected*, fixed in T18).
- DevTools → Computed → on `<body>`: `background-color` is still `rgb(7, 10, 18)`.

Then navigate to `http://localhost:4000/blog/`. The blog index page should still render its cards (no light-theme leftovers should show as gray pills).

Re-run the search patterns from 2.3:

Run via Grep: `output_mode: content, pattern: "#fff|#f8f9fa|#e9ecef|#343a40|#6c757d|#7f8c8d|#95a5a6|#0069d9|#cce5ff|#e9f5ff"`, path: `assets/css/custom-styles.css`.

Expected: zero matches.

- [ ] **Step 2.6: Commit**

```bash
git add _layouts/default.html _layouts/post.html _includes/cleanup.html assets/css/custom-styles.css
git commit -m "redesign: consolidate styles in custom-styles.css and purge light-theme rules"
```

---

## Task 3: Foundation — Focus-visible and active-nav indicator

**Files:**
- Modify: `assets/css/custom-styles.css` (append new rules)
- Modify: `_includes/header.html` (add `aria-current` logic)

- [ ] **Step 3.1: Add global `:focus-visible` ring**

Append to `assets/css/custom-styles.css`:

```css
/* Global focus ring — visible only on keyboard navigation. */
:focus { outline: none; }
:focus-visible {
  outline: 2px solid var(--primary-ring);
  outline-offset: 3px;
  border-radius: var(--radius-sm);
}
a:focus-visible,
button:focus-visible,
.btn:focus-visible,
.nav-link:focus-visible,
.navbar-toggler:focus-visible {
  outline: 2px solid var(--primary-ring);
  outline-offset: 3px;
}

/* Skip-to-content link — visually hidden until focused. */
.skip-to-content {
  position: absolute;
  left: -9999px;
  top: var(--space-2);
  z-index: 2000;
  padding: var(--space-2) var(--space-3);
  background: var(--surface-2);
  color: var(--text-primary);
  border: 1px solid var(--border-strong);
  border-radius: var(--radius-sm);
  font-family: var(--font-mono);
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.1em;
}
.skip-to-content:focus,
.skip-to-content:focus-visible {
  left: var(--space-4);
}
```

- [ ] **Step 3.2: Add skip-to-content link**

In `_layouts/default.html`, immediately after `<body>` and before `{% include header.html %}`, add:

```html
<a class="skip-to-content" href="#main">Skip to content</a>
```

And add an `id="main"` to the `<main>` element on the same file (currently `<main role="main" class="container">`):

```html
<main id="main" role="main" class="container">
```

- [ ] **Step 3.3: Emit `aria-current="page"` on the active nav link**

Replace lines 11–27 of `_includes/header.html` with:

```html
<div class="collapse navbar-collapse" id="navbarNav">
  <ul class="navbar-nav ml-auto">
    {% assign nav_items = "" | split: "|" %}
    {% assign nav_items = nav_items | push: "Home,/" %}
    {% assign nav_items = nav_items | push: "Personal,/about/" %}
    {% assign nav_items = nav_items | push: "Professional,/cv/" %}
    {% assign nav_items = nav_items | push: "Projects,/projects/" %}
    {% assign nav_items = nav_items | push: "Blog,/blog/" %}
    {% for item in nav_items %}
      {% assign parts = item | split: "," %}
      {% assign label = parts[0] %}
      {% assign href = parts[1] %}
      {% assign is_active = false %}
      {% if href == "/" and page.url == "/" %}{% assign is_active = true %}{% endif %}
      {% if href != "/" and page.url contains href %}{% assign is_active = true %}{% endif %}
      <li class="nav-item">
        <a class="nav-link{% if is_active %} is-active{% endif %}"
           href="{{ href }}"
           {% if is_active %}aria-current="page"{% endif %}>{{ label }}</a>
      </li>
    {% endfor %}
  </ul>
</div>
```

- [ ] **Step 3.4: Style the active-nav state with the amber underline**

Append to `assets/css/custom-styles.css`:

```css
/* Active nav link — amber underline 4px below text. The first of three
   sitewide places where the signal color is used. */
.navbar .nav-link {
  position: relative;
  font-family: var(--font-body);
  font-weight: 500;
}
.navbar .nav-link.is-active,
.navbar .nav-link[aria-current="page"] {
  color: var(--text-primary) !important;
}
.navbar .nav-link.is-active::after,
.navbar .nav-link[aria-current="page"]::after {
  content: "";
  position: absolute;
  left: var(--space-3);
  right: var(--space-3);
  bottom: 2px;
  height: 2px;
  background: var(--signal);
  border-radius: 1px;
}
```

- [ ] **Step 3.5: Reload and verify**

Reload `http://localhost:4000/`. Verify in Chrome:
- Tab into the page. Each focused element shows the blue ring with 3px offset.
- The skip-to-content link appears at top-left when focused via Tab.
- The "Home" nav link has a thin amber underline below the text. Navigate to `/about/` — amber underline moves to "Personal."

Run via the JavaScript tool in Claude-in-Chrome:
```js
document.querySelectorAll('a[aria-current="page"]').length
```
Expected: `1` on every page.

- [ ] **Step 3.6: Commit**

```bash
git add _layouts/default.html _includes/header.html assets/css/custom-styles.css
git commit -m "redesign: add focus-visible ring, skip link, and active nav indicator"
```

---

## Task 4: Atmosphere — Build-stamp header band

**Files:**
- Modify: `_layouts/default.html` (add band markup, adjust body padding-top)
- Modify: `assets/css/custom-styles.css` (band styles)

- [ ] **Step 4.1: Add build-stamp markup**

In `_layouts/default.html`, immediately after the skip-to-content link from Task 3 and before `{% include header.html %}`, add:

```html
<div class="build-stamp" role="presentation" aria-hidden="true">
  <div class="build-stamp__inner">
    <span>v.{{ site.time | date: "%Y.%m" }}</span>
    <span class="build-stamp__sep">·</span>
    <span>KRAKÓW</span>
    <span class="build-stamp__sep">·</span>
    <span>RUNNING IN PROD</span>
  </div>
</div>
```

- [ ] **Step 4.2: Style the band**

Append to `assets/css/custom-styles.css`:

```css
/* Build-stamp header — the calling-card detail. Sits above the navbar. */
.build-stamp {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: 28px;
  background: var(--surface-0);
  border-bottom: 1px solid var(--border-subtle);
  z-index: 1031; /* one above the navbar */
  display: flex;
  align-items: center;
  justify-content: center;
}
.build-stamp__inner {
  font-family: var(--font-mono);
  font-size: 0.6875rem;
  font-weight: 500;
  letter-spacing: 0.16em;
  text-transform: uppercase;
  color: var(--text-muted);
  display: flex;
  align-items: center;
  gap: var(--space-2);
}
.build-stamp__sep {
  color: var(--border-strong);
}

/* Push the navbar and content down by the build-stamp height. */
.navbar.fixed-top { top: 28px; }
body { padding-top: calc(28px + 56px) !important; }

@media (max-width: 575px) {
  .build-stamp__inner span:nth-child(5) { display: none; } /* hide "RUNNING IN PROD" */
  .build-stamp__inner span:nth-child(4) { display: none; } /* and its separator */
}
```

Find the existing `body { padding-top: 56px !important; }` rule in `custom-styles.css` (around line 1 or earlier — search for `padding-top: 56px`) and remove it; the new rule replaces it.

- [ ] **Step 4.3: Reload and verify**

Reload `http://localhost:4000/`. Verify:
- Top of viewport shows the 28px band with `v.2026.05 · KRAKÓW · RUNNING IN PROD`.
- Navbar sits immediately below the band.
- Content starts after both. No overlap with the hero.
- Resize the window down to 390px wide. The band shows `v.2026.05 · KRAKÓW` only.

JavaScript verification:
```js
getComputedStyle(document.querySelector('.build-stamp')).fontFamily
```
Expected: includes `"IBM Plex Mono"`.

- [ ] **Step 4.4: Commit**

```bash
git add _layouts/default.html assets/css/custom-styles.css
git commit -m "redesign: add build-stamp header band"
```

---

## Task 5: Atmosphere — Dot-grid backdrop and grain overlay

**Files:**
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 5.1: Add the dot-grid backdrop**

Append to `assets/css/custom-styles.css`:

```css
/* Dot-grid backdrop — engineering paper texture. Lives on <body>.
   Cards have a solid --surface-1 background which sits on top, so the
   grid only shows through in the negative space between content. */
body {
  background-image:
    radial-gradient(circle at 1px 1px, rgba(148, 160, 184, 0.06) 1px, transparent 0);
  background-size: 24px 24px;
  background-position: -1px -1px;
  background-attachment: fixed;
}
```

- [ ] **Step 5.2: Add the grain overlay**

Append to `assets/css/custom-styles.css`:

```css
/* Grain overlay — sits on a fixed pseudo-element above the page background
   but below all content. Pure SVG noise, ~3% opacity, mix-blend-mode soft-light. */
body::before {
  content: "";
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 1;
  opacity: 0.6;
  mix-blend-mode: soft-light;
  background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='240' height='240'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/><feColorMatrix values='0 0 0 0 0.08  0 0 0 0 0.10  0 0 0 0 0.14  0 0 0 0.55 0'/></filter><rect width='100%25' height='100%25' filter='url(%23n)'/></svg>");
}

/* Push real content above the grain. */
.build-stamp,
.navbar,
main,
footer {
  position: relative;
  z-index: 2;
}
```

- [ ] **Step 5.3: Reload and verify**

Reload `http://localhost:4000/`. Verify:
- The empty dark areas of the page show subtle dots in a regular 24px grid.
- The page has a faint film-grain texture overall.
- Cards (when present) cover the grid behind them with a solid surface.

JS check:
```js
getComputedStyle(document.body).backgroundImage.includes('radial-gradient')
```
Expected: `true`.

- [ ] **Step 5.4: Commit**

```bash
git add assets/css/custom-styles.css
git commit -m "redesign: add dot-grid backdrop and grain overlay"
```

---

## Task 6: Atmosphere — Section block with § NN numbering

**Files:**
- Modify: `assets/css/custom-styles.css` (add `.section-block` + `.eyebrow` styles)
- Modify: `_layouts/page.html` (no markup change; this task only adds CSS)

This task adds CSS only. The markup is applied per-page in Tasks 13–17.

- [ ] **Step 6.1: Add `.section-block` and `.eyebrow` styles**

Append to `assets/css/custom-styles.css`:

```css
/* Section block — every named section uses this wrapper.
   The section number sits in the left margin on desktop, inline on mobile. */
.section-block {
  position: relative;
  margin-bottom: var(--space-10);
}
.section-block__num {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--text-muted);
  display: block;
  margin-bottom: var(--space-2);
}
@media (min-width: 1200px) {
  .section-block__num {
    position: absolute;
    top: 0.4em;
    left: -3.5rem;
    margin-bottom: 0;
  }
}

.section-block__title {
  font-family: var(--font-display);
  font-weight: 600;
  font-size: var(--text-h2, 1.75rem);
  line-height: 1.2;
  letter-spacing: -0.01em;
  color: var(--text-primary);
  margin: 0 0 var(--space-3);
}
.section-block__lede {
  font-family: var(--font-body);
  font-size: 1.0625rem;
  line-height: 1.6;
  color: var(--text-secondary);
  margin: 0 0 var(--space-5);
  max-width: 60ch;
}
.section-block__rule {
  border: 0;
  border-top: 1px solid var(--border-subtle);
  margin: 0 0 var(--space-6);
}

/* Generic eyebrow label — used in heroes too. */
.eyebrow {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--text-muted);
  display: inline-block;
  margin-bottom: var(--space-3);
}
```

Sketch of the markup that Tasks 13–17 will use (do NOT add this yet — reference only):

```html
<section class="section-block">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">Featured Skills</h2>
  <p class="section-block__lede">A small set of things I do well.</p>
  <hr class="section-block__rule">
  <!-- children -->
</section>
```

- [ ] **Step 6.2: Verify**

Skip; this task adds CSS that is exercised only when content uses the new markup. Verification happens in Tasks 13–17.

- [ ] **Step 6.3: Commit**

```bash
git add assets/css/custom-styles.css
git commit -m "redesign: add section-block and eyebrow components"
```

---

## Task 7: Typography — Apply Fraunces / Plex Sans / Plex Mono across base elements

**Files:**
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 7.1: Replace the existing typography block**

In `assets/css/custom-styles.css`, locate the existing block starting around line 16 (`/* Consistent typography styling */ h1, h2, h3, h4, h5, h6 { ... }` through `p { ... }`) and replace with:

```css
/* Type scale. Hero/page heroes use --text-display via .page-hero__title. */
h1, h2, h3, h4, h5, h6 {
  font-family: var(--font-display);
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: -0.01em;
  margin: 0 0 var(--space-3);
}
h1 {
  font-size: clamp(2rem, 2.5vw + 1rem, 3rem);
  line-height: 1.1;
  letter-spacing: -0.02em;
}
h2 {
  font-size: 1.75rem;
  line-height: 1.2;
}
h3 {
  font-family: var(--font-body);
  font-weight: 600;
  font-size: 1.25rem;
  line-height: 1.3;
  color: var(--text-primary);   /* was --primary-color — pulled back */
  letter-spacing: -0.005em;
}
h4 {
  font-family: var(--font-body);
  font-weight: 600;
  font-size: 1.0625rem;
  line-height: 1.4;
  letter-spacing: 0;
}
p {
  font-family: var(--font-body);
  font-size: 1.0625rem;
  line-height: 1.65;
  color: var(--text-primary);
  margin: 0 0 var(--space-4);
}
.lead {
  font-size: 1.1875rem;
  line-height: 1.55;
  font-weight: 400;
  color: var(--text-secondary);
}
.text-secondary,
.text-muted {
  color: var(--text-secondary) !important;
}
small, .small, .text-small {
  font-size: 0.875rem;
}
code, kbd, samp, pre {
  font-family: var(--font-mono);
}
```

- [ ] **Step 7.2: Migrate remaining `--primary-color` consumers that should stay blue**

These selectors should keep using the primary blue (links, button fills, etc.). Find/replace `var(--primary-color)` with `var(--primary)` across `assets/css/custom-styles.css` for the following selectors (use Edit tool with `replace_all` for `var(--primary-color)` → `var(--primary)`, then audit):

Run Grep with `pattern: "var\\(--primary-color\\)"` to confirm zero hits after replace.

- [ ] **Step 7.3: Migrate `--dark-bg`, `--dark-surface`, `--dark-surface-hover`, `--dark-border` consumers**

Use Edit tool with `replace_all` separately for each:
- `var(--dark-bg)` → `var(--surface-0)`
- `var(--dark-surface)` → `var(--surface-1)`
- `var(--dark-surface-hover)` → `var(--surface-2)`
- `var(--dark-border)` → `var(--border-strong)`

After all four replaces, Grep for each pattern in `assets/css/custom-styles.css` — expected: zero hits each.

- [ ] **Step 7.4: Drop the legacy alias block**

In `assets/css/custom-styles.css` lines ~50–58 (the legacy alias block added in T1), delete the entire block:

```css
/* Legacy aliases — kept temporarily so unrefactored selectors still resolve.
   Removed in T7 once all consumers move to the new tokens. */
--primary-color: var(--primary);
--primary-hover-legacy: var(--primary-hover);
--dark-bg: var(--surface-0);
--dark-surface: var(--surface-1);
--dark-surface-hover: var(--surface-2);
--dark-border: var(--border-strong);
```

- [ ] **Step 7.5: Reload and verify**

Reload `http://localhost:4000/`. Verify in Chrome:
- The big "Rafael Skubisz" hero is now in Fraunces (serif). Confirm in DevTools → Computed → `font-family` on the `<h1>` resolves to `"Fraunces"`.
- The `<h3>` "Android Development" title in the skill cards is now in IBM Plex Sans (not Fraunces, not Inter) and is *white*, not blue.
- Body paragraphs render in IBM Plex Sans.

JS check:
```js
[...document.querySelectorAll('h1,h2,h3,p')].map(el => ({tag: el.tagName, font: getComputedStyle(el).fontFamily.split(',')[0]}))
```
Expected: h1/h2 → `"Fraunces"`. h3/p → `"IBM Plex Sans"`.

- [ ] **Step 7.6: Commit**

```bash
git add assets/css/custom-styles.css
git commit -m "redesign: apply Fraunces/Plex Sans/Plex Mono and migrate legacy tokens"
```

---

## Task 8: Components — Card base + variants

**Files:**
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 8.1: Replace the existing `.card` block**

In `assets/css/custom-styles.css`, find the existing `.card { ... }` block (around line 114–127) and replace with the new card system:

```css
/* Card — base. Used by skill, project, post-preview, summary. */
.card {
  background-color: var(--surface-1);
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-sm);
  padding: 0;
  margin-bottom: var(--space-5);
  overflow: hidden;
  transition: transform var(--duration-base) var(--ease-out),
              border-color var(--duration-base) var(--ease-out),
              box-shadow var(--duration-base) var(--ease-out);
  /* Animation properties from old rule removed — no more entrance fade per card. */
}
.card:hover {
  transform: translateY(-2px);
  border-color: var(--border-strong);
  box-shadow: var(--shadow-md);
}
.card-body {
  padding: var(--space-6);
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
}
.card-title {
  font-family: var(--font-body);
  font-weight: 600;
  color: var(--text-primary);
  margin: 0;
  padding: 0;
  border-bottom: none;       /* drop the old border-bottom in custom-styles.css */
  display: block;            /* override the flex display added later in file */
}
.card-title::after { content: none; }  /* drop the 40px underline accent */

/* Variant: hero card — used for the home Welcome panel and Let's Connect. */
.card--hero {
  background-color: var(--surface-3);
  border-radius: var(--radius-lg);
  padding: var(--space-7);
}

/* Variant: linked card — entire card is an anchor. */
a.card--linked,
.card--linked {
  display: block;
  color: inherit;
  text-decoration: none;
}
a.card--linked:hover,
.card--linked:hover {
  border-color: rgba(94, 128, 242, 0.4);
  color: inherit;
  text-decoration: none;
}

/* Variant: accent card — amber left bar. Used only on the CV summary. */
.card--accent {
  border-left: 3px solid var(--signal);
}
```

- [ ] **Step 8.2: Remove redundant `.card-title` overrides further down the file**

Grep for `.card-title` in `assets/css/custom-styles.css`. Multiple later blocks (around lines 141–157, 760–771) re-declare `.card-title` with different rules. Delete those redundant blocks. The single rule in 8.1 above is the source of truth.

After cleanup, Grep `\.card-title` should return at most 2–3 hits (the new rule + per-page scoped rules in `#blog-section .card-title`).

- [ ] **Step 8.3: Reload and verify**

Navigate to `http://localhost:4000/`. Verify the home page Welcome card:
- Background: deep ink panel (looks like `--surface-3`).
- No top blue border accent (removed).
- The four skill cards have rounded 8px corners, slight shadow.
- Hover over a skill card: it lifts 2px, border brightens.

JS check:
```js
getComputedStyle(document.querySelector('.card')).borderRadius
```
Expected: `"8px"`.

- [ ] **Step 8.4: Commit**

```bash
git add assets/css/custom-styles.css
git commit -m "redesign: rebuild card component with variants and new tokens"
```

---

## Task 9: Components — Unified tag system

**Files:**
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 9.1: Replace `.tag` block and remove competing tag rules**

Locate the existing `.tag` block (around line 218–228), `.skill-tag` block (around line 2246–2280), and any other tag-related rules. Replace with a single unified system:

```css
/* Tag — base. Plex Mono for engineer-aesthetic. */
.tag,
.skill-tag {
  display: inline-flex;
  align-items: center;
  font-family: var(--font-mono);
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.04em;
  padding: var(--space-1) var(--space-3);
  border-radius: var(--radius-sm);
  background-color: var(--primary-soft);
  border: 1px solid var(--primary);
  color: var(--primary);
  margin: 0;
  text-decoration: none;
  transition: background-color var(--duration-fast) var(--ease-out),
              border-color var(--duration-fast) var(--ease-out),
              color var(--duration-fast) var(--ease-out);
}
.tag:hover,
.skill-tag:hover {
  background-color: rgba(94, 128, 242, 0.18);
  border-color: var(--primary-hover);
  color: var(--primary-hover);
  text-decoration: none;
}

/* Variant: signal (amber) — used only on blog post tags. */
.tag--signal {
  background-color: var(--signal-soft);
  border-color: var(--signal);
  color: var(--signal);
}
.tag--signal:hover {
  background-color: rgba(245, 183, 59, 0.22);
  border-color: var(--signal-ink);
  color: var(--signal-ink);
}

/* Variant: ghost — neutral; used in project cards so tags don't compete with title. */
.tag--ghost {
  background-color: transparent;
  border-color: var(--border-subtle);
  color: var(--text-secondary);
}
.tag--ghost:hover {
  border-color: var(--border-strong);
  color: var(--text-primary);
  background-color: transparent;
}
```

Remove the staggered `animation-delay` rules `.skill-tag:nth-child(1)` through `(12)` near the bottom of the file — that animation is killed in Task 19.

- [ ] **Step 9.2: Reload and verify**

Navigate to `http://localhost:4000/cv/`. Scroll to "Additional Technologies" section. Verify:
- Tags render with `IBM Plex Mono` font, sharp 4px corners.
- All tags look identical (no mix of pill and rounded-rectangle anymore).

JS check:
```js
getComputedStyle(document.querySelector('.skill-tag')).fontFamily.includes('IBM Plex Mono')
```
Expected: `true`.

- [ ] **Step 9.3: Commit**

```bash
git add assets/css/custom-styles.css
git commit -m "redesign: unify tag system with mono font and signal/ghost variants"
```

---

## Task 10: Components — Buttons and link-arrow

**Files:**
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 10.1: Replace button rules**

Locate the existing `.btn`, `.btn-primary`, `.btn-outline-primary` blocks (lines 68–112). Replace with:

```css
/* Buttons. */
.btn {
  display: inline-flex;
  align-items: center;
  gap: var(--space-2);
  font-family: var(--font-body);
  font-weight: 600;
  font-size: 0.9375rem;
  line-height: 1.2;
  padding: var(--space-3) var(--space-5);
  border-radius: var(--radius-md);
  border: 1px solid transparent;
  cursor: pointer;
  text-decoration: none;
  transition: background-color var(--duration-fast) var(--ease-out),
              border-color var(--duration-fast) var(--ease-out),
              color var(--duration-fast) var(--ease-out),
              transform var(--duration-fast) var(--ease-out),
              box-shadow var(--duration-fast) var(--ease-out);
  letter-spacing: 0;
}

.btn-primary {
  background-color: var(--primary);
  border-color: var(--primary);
  color: var(--text-on-primary);
  box-shadow: var(--shadow-sm);
}
.btn-primary:hover {
  background-color: var(--primary-hover);
  border-color: var(--primary-hover);
  color: var(--text-on-primary);
  transform: translateY(-1px);
  box-shadow: var(--shadow-md);
  text-decoration: none;
}
.btn-primary:active {
  background-color: var(--primary-pressed);
  border-color: var(--primary-pressed);
  transform: translateY(0);
  box-shadow: var(--shadow-sm);
}

.btn-outline-primary {
  background-color: transparent;
  border-color: var(--primary);
  color: var(--primary);
}
.btn-outline-primary:hover {
  background-color: var(--primary-soft);
  border-color: var(--primary-hover);
  color: var(--primary-hover);
  transform: translateY(-1px);
  text-decoration: none;
}
.btn-outline-primary:active {
  transform: translateY(0);
  background-color: rgba(94, 128, 242, 0.18);
}

/* Ghost button — tertiary inline. */
.btn-ghost {
  background-color: transparent;
  border-color: transparent;
  color: var(--text-secondary);
  padding: var(--space-2) var(--space-3);
}
.btn-ghost:hover {
  color: var(--text-primary);
  text-decoration: underline;
  text-decoration-color: var(--primary);
  text-underline-offset: 4px;
}

/* Link-arrow — text + arrow that slides right on hover. */
.btn-link-arrow {
  background: transparent;
  border: none;
  padding: var(--space-2) 0;
  color: var(--primary);
  font-weight: 500;
  font-family: var(--font-body);
}
.btn-link-arrow .arrow {
  transition: transform var(--duration-base) var(--ease-out);
  display: inline-block;
  margin-left: var(--space-2);
}
.btn-link-arrow:hover {
  color: var(--primary-hover);
  text-decoration: none;
}
.btn-link-arrow:hover .arrow {
  transform: translateX(4px);
}
```

- [ ] **Step 10.2: Reload and verify**

Navigate to `http://localhost:4000/`. Verify:
- Primary buttons (e.g., "Learn More About Me") have a flat blue fill, sharp-ish corners, IBM Plex Sans text.
- Hover lifts 1px and brightens slightly.
- Outline buttons stay outlined; on hover, fill becomes a faint blue wash — not a full color flip.

- [ ] **Step 10.3: Commit**

```bash
git add assets/css/custom-styles.css
git commit -m "redesign: rebuild buttons with new token system"
```

---

## Task 11: Components — Skill card

**Files:**
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 11.1: Replace `.skill-card` and `.skill-icon` blocks**

Locate the existing `.skill-card`, `.skill-icon-container`, `.skill-icon`, `.compact-skill-card`, `.compact-skill-icon`, `.compact-skill-name` blocks (lines 263–333 and 2068–2128) and replace with a single compact recipe:

```css
/* Skill card. Compact, square icon, no glow loops. */
.skill-card,
.compact-skill-card {
  background-color: var(--surface-1);
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius-md);
  padding: var(--space-5);
  text-align: left;
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
  min-height: 0;
  height: 100%;
  transition: transform var(--duration-base) var(--ease-out),
              border-color var(--duration-base) var(--ease-out),
              box-shadow var(--duration-base) var(--ease-out);
}
.skill-card:hover,
.compact-skill-card:hover {
  transform: translateY(-2px);
  border-color: var(--border-strong);
  box-shadow: var(--shadow-md);
}

.skill-icon-container,
.compact-skill-icon {
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: var(--primary-soft);
  border: none;
  border-radius: var(--radius-sm);
  margin: 0;
  animation: none !important;          /* kill pulseGlow loop */
  box-shadow: none;
}
.skill-icon,
.compact-skill-icon i {
  font-size: 1.125rem;
  color: var(--primary);
  transition: none;
}

.skill-card h3,
.compact-skill-card h3,
.compact-skill-name {
  font-family: var(--font-body);
  font-weight: 600;
  font-size: 1.0625rem;
  color: var(--text-primary);          /* not blue */
  margin: var(--space-2) 0 var(--space-2);
  text-align: left;
}
.skill-card p {
  font-family: var(--font-body);
  font-size: 0.9375rem;
  line-height: 1.55;
  color: var(--text-secondary);
  margin: 0;
  text-align: left;
}

/* Hover on the icon — once, with the card transition. No infinite glow. */
.skill-card:hover .skill-icon-container,
.compact-skill-card:hover .compact-skill-icon {
  background-color: var(--primary);
}
.skill-card:hover .skill-icon,
.compact-skill-card:hover .compact-skill-icon i {
  color: var(--text-on-primary);
}
```

- [ ] **Step 11.2: Reload and verify**

Navigate to `http://localhost:4000/`. Scroll to "Featured Skills." Verify:
- Icons are small squares with rounded corners (no circles, no glow).
- Titles are white, left-aligned.
- Hover: card lifts 2px, icon background turns solid blue.

- [ ] **Step 11.3: Commit**

```bash
git add assets/css/custom-styles.css
git commit -m "redesign: compact skill card with square icon and no glow loops"
```

---

## Task 12: Components — Page hero (header.html refactor)

**Files:**
- Modify: `_includes/header.html`
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 12.1: Rewrite the hero markup in `_includes/header.html`**

Replace lines 32–47 (the `<header class="header-section">` block) with:

```html
{% if page.title and layout.layout != 'post' %}
{% assign hero_eyebrow = page.eyebrow | default: page.title | upcase %}
<header class="page-hero">
  <div class="container">
    <div class="page-hero__inner">
      <span class="eyebrow page-hero__eyebrow">§ 00 &nbsp; {{ hero_eyebrow }}</span>
      <hr class="page-hero__rule">
      <h1 class="page-hero__title">{{ page.title }}.</h1>
      {% if page.subtitle %}
        <p class="page-hero__subtitle">{{ page.subtitle }}</p>
      {% endif %}
      {% if page.hero_cta %}
        <div class="page-hero__cta">
          {% for cta in page.hero_cta %}
            <a class="btn {{ cta.style | default: 'btn-outline-primary' }}" href="{{ cta.href }}">{{ cta.label }}</a>
          {% endfor %}
        </div>
      {% endif %}
    </div>
  </div>
</header>
{% endif %}
```

The `{% if ... layout.layout != 'post' %}` guard replaces the temporary `.post .header-section { display: none }` rule added in Task 2. Remove that temporary rule from `custom-styles.css` now.

- [ ] **Step 12.2: Add hero CSS**

Append to `assets/css/custom-styles.css`:

```css
/* Page hero — single pattern across Home / Personal / Professional / Projects / Blog. */
.page-hero {
  position: relative;
  background-color: var(--surface-1);
  border-bottom: 1px solid var(--border-subtle);
  padding: var(--space-9) 0 var(--space-8);
  overflow: hidden;
}
.page-hero::before {
  content: "";
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at 80% -20%, var(--primary-soft) 0%, transparent 60%);
  pointer-events: none;
}
.page-hero__inner {
  position: relative;
  max-width: 760px;
  margin: 0 auto;
}
.page-hero__eyebrow {
  display: block;
  margin-bottom: var(--space-4);
}
.page-hero__rule {
  width: 64px;
  border: 0;
  border-top: 1px solid var(--border-subtle);
  margin: 0 0 var(--space-4);
}
.page-hero__title {
  font-family: var(--font-display);
  font-weight: 600;
  font-size: clamp(2.75rem, 5vw + 1rem, 5.5rem);
  line-height: 1.02;
  letter-spacing: -0.03em;
  color: var(--text-primary);
  margin: 0 0 var(--space-4);
}
.page-hero__subtitle {
  font-family: var(--font-body);
  font-size: 1.1875rem;
  line-height: 1.5;
  color: var(--text-secondary);
  margin: 0 0 var(--space-5);
  max-width: 56ch;
}
.page-hero__cta {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-3);
}

/* Status pill — "currently at Pulsate." Second of the three signal uses. */
.status-pill {
  display: inline-flex;
  align-items: center;
  gap: var(--space-2);
  padding: var(--space-2) var(--space-4);
  background-color: var(--signal-soft);
  border: 1px solid var(--signal);
  border-radius: var(--radius-pill);
  font-family: var(--font-body);
  font-size: 0.8125rem;
  font-weight: 500;
  color: var(--signal);
}
.status-pill__dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--signal);
  box-shadow: 0 0 0 0 rgba(245, 183, 59, 0.5);
  animation: status-pulse 1.6s var(--ease-out) 1 forwards;
}
@keyframes status-pulse {
  0%   { box-shadow: 0 0 0 0 rgba(245, 183, 59, 0.5); }
  100% { box-shadow: 0 0 0 10px rgba(245, 183, 59, 0); }
}
```

Note the keyframe runs **once** (`1 forwards`), not infinitely.

- [ ] **Step 12.3: Remove the temporary post-page hide rule**

In `assets/css/custom-styles.css`, find the temporary rule added in Step 2.2:

```css
.post .header-section,
body:has(.post) .header-section {
  display: none;
}
```

Delete it. The `header.html` guard makes it unnecessary.

- [ ] **Step 12.4: Reload and verify**

Navigate to `http://localhost:4000/`. Verify:
- The home hero shows `§ 00 RAFAEL SKUBISZ` eyebrow in mono, then a 64px hairline, then "Rafael Skubisz." in Fraunces (with the period).
- Subtitle below in Plex Sans.

Navigate to `http://localhost:4000/about/`. Same pattern with `§ 00 PERSONAL`.

Navigate to `http://localhost:4000/2025-04-26-mvvm-vs-mvi/` (a post). The page-hero must NOT render — only the post layout's own header should show.

JS check:
```js
document.querySelector('.page-hero__title')?.textContent
```
Expected: `"Rafael Skubisz."` on home, `"Personal."` on about, `null` on post pages.

- [ ] **Step 12.5: Commit**

```bash
git add _includes/header.html assets/css/custom-styles.css
git commit -m "redesign: build page-hero with eyebrow, Fraunces title, and status pill"
```

---

## Task 13: Page — Home (`index.md`)

**Files:**
- Modify: `index.md`

- [ ] **Step 13.1: Add hero meta (eyebrow override)**

In `index.md` front matter (lines 1–8), keep `title: Rafael Skubisz` and `subtitle: Software Engineering Manager with 12+ years of experience`, and add:

```yaml
eyebrow: § 00 &nbsp; PORTFOLIO
hero_cta:
  - label: Read the CV →
    href: /cv/
    style: btn-primary
  - label: Contact
    href: mailto:skubisz.rafael@gmail.com
    style: btn-outline-primary
```

Then **remove** the old hero `<div class="container-fluid py-5 hero-section">...</div>` block (lines 10–19). The new `page-hero` rendered by `header.html` replaces it.

- [ ] **Step 13.2: Wrap "Featured Skills" in a section block**

Replace the existing block (lines 21–70 in `index.md`):

```html
<section class="section-block container">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">Featured Skills</h2>
  <p class="section-block__lede">A small set of things I do well.</p>
  <hr class="section-block__rule">

  <div class="row justify-content-center">
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="skill-card">
        <div class="skill-icon-container">
          <i class="fas fa-mobile-alt skill-icon"></i>
        </div>
        <h3>Android Development</h3>
        <p>Java / Kotlin expertise with Jetpack Compose and modern Android development practices</p>
      </div>
    </div>
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="skill-card">
        <div class="skill-icon-container">
          <i class="fab fa-apple skill-icon"></i>
        </div>
        <h3>iOS Development</h3>
        <p>Swift and Objective-C experience developing native iOS applications</p>
      </div>
    </div>
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="skill-card">
        <div class="skill-icon-container">
          <i class="fas fa-code skill-icon"></i>
        </div>
        <h3>SDK Development</h3>
        <p>Creating cross-platform SDKs used by major financial institutions</p>
      </div>
    </div>
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="skill-card">
        <div class="skill-icon-container">
          <i class="fas fa-server skill-icon"></i>
        </div>
        <h3>Backend Skills</h3>
        <p>Ruby on Rails, Java/Kotlin backends, and blockchain development</p>
      </div>
    </div>
  </div>
</section>
```

Note: removed the inline `style="--animation-order: N"` and the bottom-center title block. The section header is the eyebrow + title.

- [ ] **Step 13.3: Wrap "About Me / Professional Experience" two-up**

Replace the existing two-up block (lines 72–104):

```html
<section class="section-block container">
  <span class="section-block__num">§ 02</span>
  <h2 class="section-block__title">About / Experience</h2>
  <p class="section-block__lede">Two stories that overlap.</p>
  <hr class="section-block__rule">

  <div class="row">
    <div class="col-lg-6 mb-4">
      <div class="card card--hero h-100">
        <div class="card-body">
          <h3 class="card-title">About Me</h3>
          <p>I'm a mobile lead developer with a passion for creating technology that improves people's lives. With over a decade of experience in Android, iOS, and backend development, I've built solutions used by millions of users worldwide.</p>
          <p>Born in the U.S. and now living in Poland, I bring a unique perspective to my work and embrace challenges that push the boundaries of what's possible in mobile development.</p>
          <div class="mt-auto">
            <a href="/about/" class="btn btn-outline-primary">Learn More About Me</a>
          </div>
        </div>
      </div>
    </div>
    <div class="col-lg-6 mb-4">
      <div class="card card--hero h-100">
        <div class="card-body">
          <h3 class="card-title">Professional Experience</h3>
          <p>I'm currently the Software Engineering Manager at Pulsate, where I lead a team of 5 engineers while also contributing hands-on development (~30-50% of my time). I've developed an Android and iOS SDK used by major U.S. digital banking platforms, serving hundreds of financial institutions with 10 million monthly active users.</p>
          <p>My expertise spans mobile app architecture, SDK development, team leadership, and mentorship, with a focus on delivering secure, high-performance solutions while fostering growth in my team.</p>
          <div class="mt-auto">
            <a href="/cv/" class="btn btn-outline-primary">View My Professional CV</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>
```

- [ ] **Step 13.4: Wrap "Recent Posts" section, update card markup**

Replace lines 106–139:

```html
<section class="section-block container" id="blog-section">
  <span class="section-block__num">§ 03</span>
  <h2 class="section-block__title">Recent Posts</h2>
  <p class="section-block__lede">Latest thoughts and updates.</p>
  <hr class="section-block__rule">

  <div class="row">
    {% assign posts = site.posts | sort: 'date' | reverse %}
    {% for post in posts limit:3 %}
    <div class="col-lg-4 mb-4">
      <a class="card card--linked h-100" href="{{ post.url | relative_url }}">
        <div class="card-body">
          <p class="post-card__meta">{{ post.date | date: "%b %d %Y" | upcase }}</p>
          <h3 class="card-title">{{ post.title }}</h3>
          <p class="card-text">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
          <span class="btn-link-arrow mt-auto">Read more <span class="arrow">→</span></span>
        </div>
      </a>
    </div>
    {% endfor %}
    {% if site.posts.size == 0 %}
    <div class="col-12 text-center">
      <p>No posts available yet. Check back soon!</p>
    </div>
    {% endif %}
  </div>

  {% if site.posts.size > 3 %}
  <div class="mt-4">
    <a href="/blog/" class="btn btn-outline-primary">View all posts</a>
  </div>
  {% endif %}
</section>
```

Then add a small style for the new `.post-card__meta` class. Append to `assets/css/custom-styles.css`:

```css
.post-card__meta {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.08em;
  color: var(--text-muted);
  margin: 0 0 var(--space-2);
  text-transform: uppercase;
}
```

- [ ] **Step 13.5: Wrap "Let's Connect" section**

Replace lines 141–159:

```html
<section class="section-block container">
  <span class="section-block__num">§ 04</span>
  <h2 class="section-block__title">Let's Connect</h2>
  <p class="section-block__lede">Interested in working together? Get in touch.</p>
  <hr class="section-block__rule">

  <div class="card card--hero card--accent">
    <div class="card-body">
      <div class="d-flex flex-wrap" style="gap: var(--space-3);">
        <a href="mailto:skubisz.rafael@gmail.com" class="btn btn-primary">Contact Me</a>
        <a href="https://github.com/Jenovas" target="_blank" rel="noopener" class="btn btn-outline-primary">GitHub</a>
        <a href="https://www.linkedin.com/in/rafael-skubisz-210993b0/" target="_blank" rel="noopener" class="btn btn-outline-primary">LinkedIn</a>
      </div>
    </div>
  </div>
</section>
```

This re-uses `card--accent` (amber left bar) so the page's primary CTA gets a clear visual anchor.

- [ ] **Step 13.6: Reload and verify**

Navigate to `http://localhost:4000/`. Scroll the entire page. Verify:
- Hero with eyebrow, Fraunces title, two CTAs.
- § 01 Featured Skills section block, 4 compact skill cards.
- § 02 About / Experience two-up.
- § 03 Recent Posts, three linked cards, each with mono date and arrow link.
- § 04 Let's Connect, amber-left-bar hero card.
- No "Welcome to my professional portfolio" gradient h2 anywhere (the old hero is gone).

JS check:
```js
[...document.querySelectorAll('.section-block__num')].map(n => n.textContent.trim())
```
Expected: `["§ 01","§ 02","§ 03","§ 04"]`.

- [ ] **Step 13.7: Commit**

```bash
git add index.md assets/css/custom-styles.css
git commit -m "redesign: rebuild home page with section blocks and new card variants"
```

---

## Task 14: Page — Personal (`about.markdown`) + fix profile image

**Files:**
- Modify: `about.markdown`
- Modify: `assets/img/profile.jpg` (replace placeholder)
- Modify: `assets/css/custom-styles.css` (initials-fallback styles)

- [ ] **Step 14.1: Replace the broken profile image**

The file `assets/img/profile.jpg` is a 56-byte text placeholder. Two options:

- **Option A (preferred):** Rafael provides a real headshot. Save as `assets/img/profile.jpg` (or `.webp`). Confirm via `file assets/img/profile.jpg` — expected output includes `JPEG image data`.
- **Option B (fallback):** Render an initials-in-a-circle as a CSS fallback so the page doesn't break.

Implement **both** so the fallback shows correctly if the asset is ever missing again. For Option B, replace the `<img>` element in `about.markdown` line 12–14 with:

```html
<div class="profile-image-container" role="img" aria-label="Portrait of Rafael Skubisz">
  <img src="/assets/img/profile.jpg"
       alt="Rafael Skubisz, mobile developer based in Kraków"
       class="profile-image"
       onerror="this.style.display='none'; this.parentNode.classList.add('profile-image-container--fallback');">
  <span class="profile-image-fallback" aria-hidden="true">RS</span>
</div>
```

And append fallback CSS to `assets/css/custom-styles.css`:

```css
.profile-image-container--fallback {
  background: var(--surface-2);
  display: flex;
  align-items: center;
  justify-content: center;
  animation: none;
}
.profile-image-fallback {
  font-family: var(--font-display);
  font-size: 5rem;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: -0.04em;
}
.profile-image-container--fallback .profile-image-fallback {
  display: block;
}
.profile-image-fallback { display: none; }
```

If the real image is supplied, the `<img>` loads and the fallback stays hidden. If not, `onerror` hides the broken image and reveals the initials.

- [ ] **Step 14.2: Wrap sections in section blocks**

Replace the body of `about.markdown` after the front matter with the section-block markup. Reference the current file for content; only the wrappers change. Example for "My Story":

```html
<section class="section-block container">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">My Story</h2>
  <hr class="section-block__rule">
  <!-- existing My Story paragraphs go here, unchanged -->
</section>
```

Apply the same pattern to "Beyond Coding / What Drives Me" (combine into a two-up `<section class="section-block">` similar to home § 02) and "Connect With Me" (§ NN).

Update front matter to add the eyebrow:

```yaml
eyebrow: § 00 &nbsp; PERSONAL
```

- [ ] **Step 14.3: Remove the per-bullet hover translate**

In `assets/css/custom-styles.css` locate `.personal-list li:hover`, `.value-item:hover`, `.icon-container`, `.value-icon` hover effects (around lines 1922–1976). Remove the `transform: translateX(5px)` and `transform: scale(1.1)` rules — they're noisy. Keep the background-color change for a quiet hover indication.

- [ ] **Step 14.4: Reload and verify**

Navigate to `http://localhost:4000/about/`. Verify:
- Hero with eyebrow `§ 00 PERSONAL` and Fraunces title "Personal."
- Profile area either shows the real photo OR shows the "RS" initials fallback (depending on Step 14.1 option).
- Sections each have `§ NN` numbers.
- Hover on list items: only background changes; no slide.

- [ ] **Step 14.5: Commit**

```bash
git add about.markdown assets/css/custom-styles.css assets/img/profile.jpg
git commit -m "redesign: rebuild Personal page with section blocks; fix profile image fallback"
```

---

## Task 15: Page — Professional (`cv.md`)

**Files:**
- Modify: `cv.md`
- Modify: `assets/css/custom-styles.css` (tech-icon grid replacement, status pill placement)

- [ ] **Step 15.1: Add eyebrow and status pill in hero**

In `cv.md` front matter, set:

```yaml
eyebrow: § 00 &nbsp; CV / EXPERIENCE
```

After the front matter, insert a top section that uses `.status-pill` to declare current employment:

```html
<div class="container" style="margin-top: var(--space-5); margin-bottom: var(--space-7);">
  <span class="status-pill">
    <span class="status-pill__dot"></span>
    Currently at Pulsate
  </span>
</div>
```

This is the second of the three sitewide signal uses.

- [ ] **Step 15.2: Replace the floating tech-icon constellation**

Locate the `.professional-graphic` markup in `cv.md` and replace with a clean 2×2 + center grid. Replace whatever block contains `.tech-icon.android`, `.tech-icon.apple`, `.tech-icon.code`, `.tech-icon.mobile`, `.tech-icon.server` markup with:

```html
<div class="tech-grid">
  <div class="tech-grid__cell"><i class="fab fa-android"></i></div>
  <div class="tech-grid__cell"><i class="fab fa-apple"></i></div>
  <div class="tech-grid__cell tech-grid__cell--center"><i class="fas fa-server"></i></div>
  <div class="tech-grid__cell"><i class="fas fa-code"></i></div>
  <div class="tech-grid__cell"><i class="fas fa-mobile-alt"></i></div>
</div>
```

Then in `assets/css/custom-styles.css`, **delete** the existing `.tech-icon`, `.tech-icon.*` (`.android`, `.apple`, `.code`, `.mobile`, `.server`) and `@keyframes float` rules (lines 1083–1151), and append:

```css
/* Tech grid — 2x2 with a larger center cell. Replaces the chaotic float layout. */
.tech-grid {
  display: grid;
  grid-template-columns: repeat(3, 64px);
  grid-template-rows: repeat(2, 64px);
  gap: var(--space-3);
  justify-content: center;
  margin: var(--space-5) auto;
}
.tech-grid__cell {
  background: var(--surface-2);
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius-md);
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--primary);
  font-size: 1.5rem;
  transition: border-color var(--duration-fast) var(--ease-out),
              transform var(--duration-fast) var(--ease-out);
}
.tech-grid__cell:hover {
  border-color: var(--primary);
  transform: translateY(-2px);
}
.tech-grid__cell--center {
  grid-row: 1 / span 2;
  grid-column: 2;
  font-size: 1.875rem;
}
```

- [ ] **Step 15.3: Wrap remaining CV sections in section blocks**

In `cv.md`, identify each major section: Professional Summary, Key Skills, Additional Technologies, Professional Experience, Education. Wrap each in:

```html
<section class="section-block container">
  <span class="section-block__num">§ 0X</span>
  <h2 class="section-block__title">Section Title</h2>
  <hr class="section-block__rule">
  <!-- existing content -->
</section>
```

The Professional Summary card uses `.card--accent` for the amber left bar:

```html
<section class="section-block container">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">Professional Summary</h2>
  <hr class="section-block__rule">
  <div class="card card--accent">
    <div class="card-body">
      <p>[existing summary paragraph]</p>
    </div>
  </div>
</section>
```

- [ ] **Step 15.4: Replace floating date/location with mono header line in job entries**

Find each `.job-item` block. Replace the existing `.job-header` floating date/location markup with a clean mono line:

```html
<div class="job-item">
  <p class="job-meta">2023.07 — PRESENT  ·  REMOTE (IE)</p>
  <div class="job-title-container">
    <div class="job-icon">
      <i class="fas fa-people-group"></i>
    </div>
    <div>
      <h3 class="job-title">Software Engineering Manager</h3>
      <p class="company">Pulsate</p>
    </div>
  </div>
  <ul class="job-responsibilities">
    <!-- existing bullets -->
  </ul>
</div>
```

And in `assets/css/custom-styles.css`, append:

```css
.job-meta {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--text-muted);
  margin: 0 0 var(--space-3);
}
```

Drop the existing `.job-header` / `.job-date` / `.location` rules (around lines 2161–2196). They're superseded.

Also remove the per-bullet hover translate in `.job-responsibilities li:hover` (around 2222–2229).

- [ ] **Step 15.5: Reload and verify**

Navigate to `http://localhost:4000/cv/`. Verify:
- Eyebrow + Fraunces "Professional." title.
- Amber "Currently at Pulsate" pill below the hero with a single one-cycle pulse animation on the dot.
- Tech grid renders as a clean 2×2 + tall center (no chaotic floating icons).
- Each section has `§ NN` numbering.
- Professional Summary card has a small amber left bar.
- Job entries show mono date line at top, no right-floating date.

JS check:
```js
document.querySelectorAll('.status-pill').length
```
Expected: `1`.

- [ ] **Step 15.6: Commit**

```bash
git add cv.md assets/css/custom-styles.css
git commit -m "redesign: rebuild CV page with section blocks, tech grid, and status pill"
```

---

## Task 16: Page — Projects (`projects.md`)

**Files:**
- Modify: `projects.md`
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 16.1: Add eyebrow and wrap projects in section block**

Update front matter:

```yaml
eyebrow: § 00 &nbsp; WORK
```

Replace the existing projects layout with a section-block wrapper and convert each project card to the `.card--linked` pattern. Example for one card:

```html
<section class="section-block container">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">Projects</h2>
  <p class="section-block__lede">Selected work — production code, shipped.</p>
  <hr class="section-block__rule">

  <div class="row">
    <div class="col-lg-4 col-md-6 mb-4">
      <a class="card card--linked h-100" href="https://github.com/Jenovas/pulsate-sdk-example" target="_blank" rel="noopener">
        <div class="card-body">
          <p class="project-card__meta">PROJ. 01  ·  KOTLIN / SWIFT</p>
          <h3 class="card-title">Pulsate SDK</h3>
          <p class="card-text">An Android &amp; iOS SDK used by major Digital Banking Platforms in 500+ apps, serving over 10 million MAU. Provides secure authentication, communication, and engagement features.</p>
          <div class="project-card__tags">
            <span class="tag tag--ghost">Android</span>
            <span class="tag tag--ghost">iOS</span>
            <span class="tag tag--ghost">SDK</span>
          </div>
          <span class="btn-link-arrow mt-auto">View on GitHub <span class="arrow">→</span></span>
        </div>
      </a>
    </div>
    <!-- repeat for each project -->
  </div>
</section>
```

Repeat for each of the four projects (Pulsate SDK, Cryptonomy, KMM Financial Dashboard, Mobile QA Automation). Keep the existing project descriptions and tech tags.

Replace any "Areas of Interest" block at the bottom of `projects.md` with another `.section-block` (`§ 02 Areas of Interest`).

- [ ] **Step 16.2: Add project-card meta + tags layout CSS**

Append to `assets/css/custom-styles.css`:

```css
.project-card__meta {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--text-muted);
  margin: 0 0 var(--space-2);
}
.project-card__tags {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-2);
  margin: var(--space-3) 0;
}
```

Delete the existing `.project-card` rule (lines 605–665) — `.card--linked` supersedes it.

- [ ] **Step 16.3: Reload and verify**

Navigate to `http://localhost:4000/projects/`. Verify:
- Hero with `§ 00 WORK` + "Projects." Fraunces title.
- § 01 Projects section block.
- Each card: mono `PROJ. 01 · KOTLIN / SWIFT` line at top, then Fraunces-ish title, then description, then ghost tags, then arrow link.
- Hover on a card: lifts and border tints toward blue.
- The whole card is a single clickable link.

- [ ] **Step 16.4: Commit**

```bash
git add projects.md assets/css/custom-styles.css
git commit -m "redesign: rebuild projects page with linked cards and case-file numbers"
```

---

## Task 17: Page — Blog index (`blog.md`)

**Files:**
- Modify: `blog.md`
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 17.1: Apply section block + new post-card markup**

Update `blog.md` front matter:

```yaml
eyebrow: § 00 &nbsp; WRITING
```

Replace the existing posts list with a vertical stack of linked cards. The blog index file structure depends on the current content of `blog.md` (236 lines); the post-card markup pattern is:

```html
<section class="section-block container">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">Posts</h2>
  <hr class="section-block__rule">

  {% for post in site.posts %}
  <a class="card card--linked post-card" href="{{ post.url | relative_url }}">
    <div class="card-body">
      <p class="post-card__meta">{{ post.date | date: "%b %d %Y" | upcase }}</p>
      <h3 class="card-title">{{ post.title }}</h3>
      <p class="card-text">{{ post.excerpt | strip_html | truncatewords: 40 }}</p>
      {% if post.tags.size > 0 %}
      <div class="post-card__tags">
        {% for tag in post.tags %}
        <span class="tag tag--signal">{{ tag }}</span>
        {% endfor %}
      </div>
      {% endif %}
      <span class="btn-link-arrow mt-auto">Read more <span class="arrow">→</span></span>
    </div>
  </a>
  {% endfor %}
</section>
```

The `.tag--signal` here is the **third of the three sitewide amber uses** (active nav, status pill, blog post tags).

- [ ] **Step 17.2: Style the post-card tags row**

Append to `assets/css/custom-styles.css`:

```css
.post-card__tags {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-2);
  margin: var(--space-3) 0;
}
```

- [ ] **Step 17.3: Reload and verify**

Navigate to `http://localhost:4000/blog/`. Verify:
- Hero with `§ 00 WRITING`.
- Posts as vertical stack of linked cards.
- Each card: mono date, Fraunces-ish title, excerpt, amber tags, arrow link.
- Hover lifts the card and tints border.

JS check:
```js
[...document.querySelectorAll('.tag--signal')].length > 0
```
Expected: `true`.

- [ ] **Step 17.4: Commit**

```bash
git add blog.md assets/css/custom-styles.css
git commit -m "redesign: rebuild blog index with post cards and signal tags"
```

---

## Task 18: Layout — Blog post (`_layouts/post.html`)

**Files:**
- Modify: `_layouts/post.html`
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 18.1: Replace post layout markup**

Replace the entire body of `_layouts/post.html` (after the front matter) with:

```html
<article class="post-page">
  <header class="post-header container">
    <div class="post-header__inner">
      <span class="eyebrow"><a href="/blog/" style="color: inherit; text-decoration: none;">← Writing</a></span>
      <h1 class="post-header__title">{{ page.title }}</h1>
      {% if page.subtitle %}
        <p class="post-header__subtitle">{{ page.subtitle }}</p>
      {% endif %}
      <p class="post-header__meta">{{ page.date | date: "%b %d %Y" | upcase }}{% if page.read_time %} &middot; {{ page.read_time }}{% endif %}</p>
      {% if page.tags.size > 0 %}
      <div class="post-header__tags">
        {% for tag in page.tags %}<span class="tag tag--signal">{{ tag }}</span>{% endfor %}
      </div>
      {% endif %}
    </div>
  </header>

  <div class="post-content container">
    <div class="post-content__inner">
      {{ content }}
    </div>
  </div>

  <footer class="post-footer container">
    <hr>
    <div class="post-footer__nav">
      <a href="/blog/" class="btn btn-ghost">← back to writing</a>
      <a href="mailto:?subject={{ page.title | url_encode }}&body=Check out this post: {{ site.url }}{{ page.url }}" class="btn btn-outline-primary">Share</a>
    </div>
  </footer>
</article>
```

- [ ] **Step 18.2: Add post styles**

Append to `assets/css/custom-styles.css`:

```css
/* Blog post layout. */
.post-page { padding-top: var(--space-7); }

.post-header__inner,
.post-content__inner,
.post-footer { max-width: 720px; margin: 0 auto; }

.post-header__title {
  font-family: var(--font-display);
  font-weight: 600;
  font-size: clamp(2.25rem, 3vw + 1rem, 3.5rem);
  line-height: 1.05;
  letter-spacing: -0.02em;
  color: var(--text-primary);
  margin: var(--space-3) 0 var(--space-4);
}
.post-header__subtitle {
  font-family: var(--font-body);
  font-size: 1.1875rem;
  line-height: 1.5;
  color: var(--text-secondary);
  margin: 0 0 var(--space-4);
}
.post-header__meta {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--text-muted);
  margin: 0 0 var(--space-4);
}
.post-header__tags {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-2);
  margin: 0 0 var(--space-6);
}
.post-header { padding-bottom: var(--space-6); border-bottom: 1px solid var(--border-subtle); }

.post-content__inner {
  font-family: var(--font-body);
  font-size: 1.0625rem;
  line-height: 1.75;
  color: var(--text-primary);
  padding: var(--space-7) 0;
}
.post-content__inner p { margin: 0 0 var(--space-5); }
.post-content__inner h2,
.post-content__inner h3,
.post-content__inner h4 {
  font-family: var(--font-display);
  font-weight: 600;
  color: var(--text-primary);
  margin: var(--space-7) 0 var(--space-3);
}
.post-content__inner h2 { font-size: 1.875rem; }
.post-content__inner h3 { font-family: var(--font-body); font-size: 1.375rem; }
.post-content__inner a {
  color: var(--text-primary);
  text-decoration: underline;
  text-decoration-color: var(--primary);
  text-decoration-thickness: 1px;
  text-underline-offset: 3px;
  transition: text-decoration-color var(--duration-fast) var(--ease-out);
}
.post-content__inner a:hover {
  text-decoration-color: var(--signal);
}
.post-content__inner pre {
  background-color: var(--surface-2);
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius-md);
  padding: var(--space-5);
  margin: 0 0 var(--space-5);
  font-family: var(--font-mono);
  font-size: 0.875rem;
  line-height: 1.6;
  overflow-x: auto;
}
.post-content__inner code {
  background-color: var(--surface-2);
  color: var(--primary);
  padding: 2px 6px;
  border-radius: var(--radius-sm);
  font-family: var(--font-mono);
  font-size: 0.9em;
}
.post-content__inner pre code { background: transparent; padding: 0; color: inherit; }
.post-content__inner blockquote {
  border-left: 3px solid var(--primary);
  padding: var(--space-2) var(--space-5);
  margin: var(--space-5) 0;
  color: var(--text-secondary);
  font-style: italic;
}
.post-content__inner img {
  max-width: 100%;
  height: auto;
  border-radius: var(--radius-md);
  margin: var(--space-5) 0;
}
.post-content__inner ul,
.post-content__inner ol {
  margin: 0 0 var(--space-5);
  padding-left: var(--space-5);
}
.post-content__inner li { margin: 0 0 var(--space-2); }

.post-footer hr {
  border: 0;
  border-top: 1px solid var(--border-subtle);
  margin: var(--space-6) 0;
}
.post-footer__nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-bottom: var(--space-7);
}

/* Override the legacy `pre` rule that defaults to #1e1e2e. */
pre {
  background-color: var(--surface-2);
}
```

Also locate and delete (or override) the legacy `pre` rule at lines 868–891 in `custom-styles.css`. The new rule above should be the only `pre` definition.

- [ ] **Step 18.3: Reload and verify**

Navigate to `http://localhost:4000/2025-04-26-mvvm-vs-mvi/`. Verify:
- "← Writing" eyebrow link at top.
- Fraunces post title.
- Mono date line below title.
- Amber tags below date.
- Body content in Plex Sans 17px, 1.75 line-height, max-width 720px centered.
- Inline `<a>` links: blue underline default, hover swaps underline to amber.
- Code blocks on `--surface-2` background, Plex Mono font.
- Inline `<code>` styled with Plex Mono, blue text.

JS check:
```js
getComputedStyle(document.querySelector('.post-content__inner pre')).backgroundColor
```
Expected: `"rgb(22, 28, 42)"` (the `--surface-2` value).

- [ ] **Step 18.4: Commit**

```bash
git add _layouts/post.html assets/css/custom-styles.css
git commit -m "redesign: rebuild blog post layout with Fraunces title, mono meta, and amber tags"
```

---

## Task 19: Motion — Remove infinite loops, keep page-entrance choreography

**Files:**
- Modify: `assets/css/custom-styles.css`

- [ ] **Step 19.1: Strip looping animations**

In `assets/css/custom-styles.css`, locate and **delete or `animation: none`** the following:

| Selector | Action |
| --- | --- |
| `.welcome-message` (the `gradientText 5s ease infinite`) | Replace `animation: gradientText 5s ease infinite;` with `animation: none;` — but better: delete the welcome-message rule entirely; it's no longer used after T13 removed the old hero |
| `.greeting` (gradientText loop) | Delete |
| `.skill-icon-container` `animation: pulseGlow ... infinite` | Already overridden in T11; remove the original rule too |
| `.summary-icon { animation: pulseGlow 2s infinite ease-in-out; }` | Remove animation property; keep box-shadow |
| `.tech-icon { animation: float 6s infinite ease-in-out; }` and per-cell `animation-delay` | Already removed in T15 (tech-icon replaced) |
| `.profile-image-container { animation: pulseGlow 3s infinite; }` | Already handled in T14; remove animation property |
| `@keyframes float` | Delete |
| `.feature-card`, `.summary-card`, `.summary-text`, `.contact-section`, `.additional-skills`, `.job-item`, `.education-item`, `.connect-buttons`, `.compact-skill-card:nth-child(N)` — all the staggered `fadeInUp` with `animation-delay` patterns | Remove the `animation` and `opacity: 0` properties. Keep just `opacity: 1` or remove the property entirely |
| `.skill-tag:nth-child(N) { animation-delay: ... }` rules (already removed in T9) | Confirm zero hits |

After this step, `@keyframes pulseGlow` and `@keyframes float` should have zero consumers. **Delete the keyframe definitions** as well.

`@keyframes fadeInUp` keeps one consumer: the hero entrance. Keep it.
`@keyframes gradientText` has zero consumers. Delete it.

- [ ] **Step 19.2: Add hero entrance choreography**

Append to `assets/css/custom-styles.css`:

```css
/* Page-entrance choreography. Runs once on load.
   Sequenced via animation-delay; total length ~620ms. */
.page-hero__eyebrow,
.page-hero__rule,
.page-hero__title,
.page-hero__subtitle,
.page-hero__cta {
  opacity: 0;
  animation: fadeInUp var(--duration-slow) var(--ease-out) forwards;
}
.page-hero__eyebrow { animation-delay: 80ms; }
.page-hero__rule    { animation-delay: 120ms; }
.page-hero__title   { animation-delay: 160ms; }
.page-hero__subtitle { animation-delay: 220ms; }
.page-hero__cta     { animation-delay: 260ms; }

@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(12px); }
  to   { opacity: 1; transform: translateY(0); }
}
```

- [ ] **Step 19.3: Add reduced-motion fallback**

Append to `assets/css/custom-styles.css`:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.001ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.001ms !important;
  }
  .page-hero__eyebrow,
  .page-hero__rule,
  .page-hero__title,
  .page-hero__subtitle,
  .page-hero__cta { opacity: 1; transform: none; }
}
```

- [ ] **Step 19.4: Reload and verify**

Reload `http://localhost:4000/`. Verify:
- The hero elements fade-up in sequence (eyebrow → rule → title → subtitle → CTA), total ~600ms, then stop.
- No more pulsing icons. No more gradient text shimmers.
- No more floating tech icons (they're a grid now per T15).
- Scroll the home page — no staggered card-entrance animations as you scroll.

Toggle prefers-reduced-motion in DevTools → Rendering → Emulate CSS media feature. Reload. Hero appears instantly with no motion.

JS check (count infinite animations):
```js
[...document.querySelectorAll('*')]
  .map(el => getComputedStyle(el).animationIterationCount)
  .filter(c => c === 'infinite').length
```
Expected: `0`.

- [ ] **Step 19.5: Commit**

```bash
git add assets/css/custom-styles.css
git commit -m "redesign: kill infinite animation loops; add single hero entrance choreography"
```

---

## Task 20: Footer + Beautiful Jekyll config realignment

**Files:**
- Modify: `_includes/footer.html`
- Modify: `assets/css/custom-styles.css`
- Modify: `_config.yml`

- [ ] **Step 20.1: Rebuild footer**

Replace the entire body of `_includes/footer.html`:

```html
<footer class="site-footer">
  <div class="container">
    <div class="site-footer__grid">
      <div class="site-footer__brand">
        <p class="site-footer__brand-title">Rafael Skubisz.</p>
        <p class="site-footer__brand-tagline">Software Engineering Manager.<br>Twelve years building mobile.</p>
      </div>
      <nav class="site-footer__nav" aria-label="Footer">
        <p class="site-footer__heading">Pages</p>
        <ul>
          <li><a href="/">Home</a></li>
          <li><a href="/about/">Personal</a></li>
          <li><a href="/cv/">Professional</a></li>
          <li><a href="/projects/">Projects</a></li>
          <li><a href="/blog/">Writing</a></li>
        </ul>
      </nav>
      <nav class="site-footer__nav" aria-label="Connect">
        <p class="site-footer__heading">Connect</p>
        <ul>
          {% if site.social-network-links.email %}<li><a href="mailto:{{ site.social-network-links.email }}">Email</a></li>{% endif %}
          {% if site.social-network-links.github %}<li><a href="https://github.com/{{ site.social-network-links.github }}" rel="noopener" target="_blank">GitHub</a></li>{% endif %}
          {% if site.social-network-links.linkedin %}<li><a href="https://linkedin.com/in/{{ site.social-network-links.linkedin }}" rel="noopener" target="_blank">LinkedIn</a></li>{% endif %}
          {% if site.social-network-links.twitter %}<li><a href="https://twitter.com/{{ site.social-network-links.twitter }}" rel="noopener" target="_blank">Twitter</a></li>{% endif %}
          <li><a href="/feed.xml">RSS</a></li>
        </ul>
      </nav>
    </div>
    <div class="site-footer__stamp">
      <span>© {{ site.time | date: '%Y' }} {{ site.author | upcase }}.</span>
      <span class="site-footer__sep">·</span>
      <span>v.{{ site.time | date: "%Y.%m" }}</span>
      <span class="site-footer__sep">·</span>
      <span>BUILT WITH JEKYLL</span>
    </div>
  </div>
</footer>
```

- [ ] **Step 20.2: Style the footer**

Append to `assets/css/custom-styles.css`:

```css
/* Site footer. */
.site-footer {
  background-color: var(--surface-1);
  border-top: 1px solid var(--border-subtle);
  padding: var(--space-8) 0 var(--space-6);
  margin-top: var(--space-10);
}
.site-footer__grid {
  display: grid;
  grid-template-columns: 1.4fr 1fr 1fr;
  gap: var(--space-7);
  padding-bottom: var(--space-7);
  border-bottom: 1px solid var(--border-subtle);
  margin-bottom: var(--space-5);
}
.site-footer__brand-title {
  font-family: var(--font-display);
  font-weight: 600;
  font-size: 1.5rem;
  letter-spacing: -0.02em;
  color: var(--text-primary);
  margin: 0 0 var(--space-3);
}
.site-footer__brand-tagline {
  font-family: var(--font-body);
  color: var(--text-secondary);
  font-size: 0.9375rem;
  line-height: 1.6;
  margin: 0;
}
.site-footer__heading {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--text-muted);
  margin: 0 0 var(--space-3);
}
.site-footer__nav ul { list-style: none; padding: 0; margin: 0; }
.site-footer__nav li { margin: 0 0 var(--space-2); }
.site-footer__nav a {
  color: var(--text-secondary);
  font-size: 0.9375rem;
  text-decoration: none;
}
.site-footer__nav a:hover { color: var(--text-primary); }

.site-footer__stamp {
  font-family: var(--font-mono);
  font-size: 0.6875rem;
  letter-spacing: 0.16em;
  text-transform: uppercase;
  color: var(--text-muted);
  display: flex;
  align-items: center;
  gap: var(--space-2);
  flex-wrap: wrap;
}
.site-footer__sep { color: var(--border-strong); }

@media (max-width: 767px) {
  .site-footer__grid {
    grid-template-columns: 1fr;
    gap: var(--space-5);
  }
}
```

Also delete the existing `footer { ... }` block (the dark one) around lines 462–480 in `custom-styles.css`. The new `.site-footer` rules supersede it.

- [ ] **Step 20.3: Update Beautiful Jekyll color variables in `_config.yml`**

Replace lines 80–93 of `_config.yml` with the Engineering Atlas values:

```yaml
# --- Colours / background image --- #
navbar-col: "#070A12"
navbar-text-col: "#94A0B8"
navbar-border-col: "#1E2536"
page-col: "#070A12"
text-col: "#ECEFF6"
link-col: "#5E80F2"
hover-col: "#7A98FF"
footer-col: "#0F141F"
footer-text-col: "#94A0B8"
footer-link-col: "#5E80F2"

mobile-theme-col: "#070A12"
```

This keeps Beautiful Jekyll defaults from fighting the custom CSS.

- [ ] **Step 20.4: Reload and verify**

Reload `http://localhost:4000/`. Scroll to the bottom. Verify:
- New 3-column footer: Brand left, Pages middle, Connect right.
- Build-stamp echo line at the bottom of the footer with copyright, version, "BUILT WITH JEKYLL".
- Mobile (390px wide): footer collapses to single column.

- [ ] **Step 20.5: Commit**

```bash
git add _includes/footer.html _config.yml assets/css/custom-styles.css
git commit -m "redesign: rebuild footer with sitemap + stamp; align jekyll color config"
```

---

## Task 21: 404 page touch-up

**Files:**
- Modify: `404.html`

- [ ] **Step 21.1: Bring 404 into the system**

The current 404 uses Beautiful Jekyll's default markup, which renders with a centered navbar layout — visible in the earlier review screenshots. Replace the body of `404.html` with content that uses the same page-hero pattern:

(Open `404.html` with Read first to confirm structure; the change below assumes a Jekyll page front matter + content body.)

Replace its body with:

```html
---
layout: page
title: "404"
subtitle: "Page not found"
eyebrow: § E0 &nbsp; ERROR
---

<section class="section-block container">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">The requested page could not be found.</h2>
  <hr class="section-block__rule">
  <p>It may have moved, or never existed. From here you can:</p>
  <ul>
    <li><a href="/">Return home →</a></li>
    <li><a href="/blog/">Browse writing →</a></li>
    <li><a href="/projects/">See projects →</a></li>
  </ul>
</section>
```

- [ ] **Step 21.2: Reload and verify**

Navigate to `http://localhost:4000/this-does-not-exist/`. Verify the 404 page now uses the page-hero pattern and `§ NN` styled content.

- [ ] **Step 21.3: Commit**

```bash
git add 404.html
git commit -m "redesign: bring 404 page into the Engineering Atlas system"
```

---

## Task 22: Final cross-page verification

**Files:** (none — verification only)

- [ ] **Step 22.1: Walk every page at 1440 width**

In Claude-in-Chrome, resize to 1440×900. Visit each URL, capture screenshots saved as `after-{page}-1440.png`:
- `http://localhost:4000/`
- `http://localhost:4000/about/`
- `http://localhost:4000/cv/`
- `http://localhost:4000/projects/`
- `http://localhost:4000/blog/`
- `http://localhost:4000/2025-04-26-mvvm-vs-mvi/`
- `http://localhost:4000/this-does-not-exist/` (404)

For each, verify:
- Build-stamp band present at top.
- Navbar below, with one amber active-link underline.
- Hero with eyebrow + Fraunces title + subtitle (where applicable; not on posts).
- Section blocks numbered `§ NN`.
- Footer with 3-column layout + build-stamp echo.

- [ ] **Step 22.2: Walk every page at 390 width**

Resize to 390×844. Repeat the walk. Verify:
- Build-stamp band shrinks (`RUNNING IN PROD` hidden).
- Section-block numbers move from left margin into the content flow.
- Footer collapses to single column.
- Nothing horizontally scrolls.

- [ ] **Step 22.3: Count signal-color usages**

In Chrome DevTools console on each page, run:

```js
[...document.querySelectorAll('*')].filter(el => {
  const s = getComputedStyle(el);
  const isAmber = (c) => /245,\s*183,\s*59/.test(c);
  return isAmber(s.color) || isAmber(s.backgroundColor) || isAmber(s.borderColor) || isAmber(s.outlineColor);
}).map(el => ({tag: el.tagName, cls: el.className})).slice(0, 20)
```

Expected: amber appears only in elements with classes matching `nav-link.is-active::after`, `status-pill*`, `tag--signal`, `signal-soft` underlines on hover. Zero usage in headings, plain text, dividers, or non-blog tags.

- [ ] **Step 22.4: Lighthouse accessibility**

For each of the seven URLs (home / personal / professional / projects / blog index / one post / 404), run a Lighthouse audit in Chrome DevTools — Accessibility category only is sufficient.

Expected: score ≥ 95 on every page.

If any page falls below 95, capture the issue list, add a follow-up task to this plan, fix, re-run.

- [ ] **Step 22.5: Build with `JEKYLL_ENV=production`**

```bash
JEKYLL_ENV=production bundle exec jekyll build
```

Expected: clean build, no warnings. The `_site/` directory contains the production output.

- [ ] **Step 22.6: Final commit and PR-ready state**

If the worktree is clean, the redesign is complete. Push the branch:

```bash
git push -u origin redesign/engineering-atlas
```

Open a PR (or hand off to `superpowers:finishing-a-development-branch` to decide).

---

## Self-review

**Spec coverage check.** Walking through `_design/design.md`:

- §1 Concept "Engineering Atlas" — anchored across T4 (build stamp), T5 (dot-grid + grain), T6 (§ NN sections), T7 (Fraunces), T9 (mono tags), T15 (CV layout).
- §4 Design tokens — fully realized in T1.
- §5 Typography — T7.
- §5.3 Color tokens including amber signal — T1; signal applied at exactly three places in T3 (nav), T15 (pill), T17 (post tags). Verified in T22.3.
- §6 Atmosphere — dot-grid + grain T5, hero radial wash T12, section rules T6.
- §7 Composition — container widths T1 (in :root), §7.3 build stamp T4, §7.4 numbered sections T6.
- §8 Components — navbar T3, hero T12, section T6, card T8, tag T9, button T10, link in T18, date T13/T15, skill card T11, project card T16, status pill T12+T15, footer T20, code block T18.
- §9 Motion — T19.
- §10 Accessibility — focus-visible T3, skip link T3, active link T3, alt text T14.
- §11 Per-page application — Home T13, Personal T14, Professional T15, Projects T16, Blog index T17, Post T18.
- §12 CSS cleanup — T2.
- §13 Open decisions — addressed in tasks where they land (KRAKÓW in T4, PROJ. NN in T16, wordmark period in T12, read-time stretch in T18, narrow container at T18).
- §14 Success criteria — verified in T22.

No gaps.

**Placeholder scan.** Searched the plan for "TBD", "TODO", "implement later", "add appropriate", "similar to". None found. Each code step shows full code.

**Type consistency.** Component class names cross-checked: `.page-hero`, `.page-hero__title`, `.page-hero__eyebrow` (T12) consistent across T13/T14/T15/T16/T17. `.section-block`, `.section-block__num`, `.section-block__title`, `.section-block__lede`, `.section-block__rule` consistent. `.tag`, `.tag--signal`, `.tag--ghost` consistent. `.card`, `.card--hero`, `.card--linked`, `.card--accent` consistent. `.btn-link-arrow .arrow` consistent.

Tokens cross-checked: every CSS code block uses tokens defined in T1's `:root` block. Legacy aliases used in T1–T6 and removed in T7.4.

The plan is internally consistent.

---

## Execution Handoff

**Plan complete and saved to `_design/plan.md`. Two execution options:**

**1. Subagent-Driven (recommended)** — A fresh subagent gets dispatched per task, two-stage review between tasks, fast iteration, keeps the main session clear of file diffs.

**2. Inline Execution** — Tasks execute in this session using `superpowers:executing-plans`, batch execution with explicit user checkpoints between major milestones (after T7, T12, T19).

**Which approach?**
