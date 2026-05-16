# jenovas.github.io — Redesign Design Doc

Author: Rafael Skubisz
Status: Draft for review (v2 — Engineering Atlas direction)
Scope: Visual / UX redesign of the personal portfolio site at `https://jenovas.github.io/`. Same Jekyll + Beautiful Jekyll stack, same content, same dark + blue identity. Goal: lift the site from "competent dev portfolio" to a distinctive personal publication that an engineering manager could be proud to hand a hiring committee.

This document defines **what** the site should look and feel like after the redesign. The **how** (file-by-file changes, ordering, commits) lives in a separate plan doc that follows after this one is approved.

This is v2. The first draft picked a "safe modern dark dev portfolio" direction with Inter + a mint accent. That direction was flagged as the generic AI-default aesthetic. v2 commits to a single distinctive concept (Engineering Atlas) instead.

---

## 1. Concept: Engineering Atlas

**One-line:** a technical broadsheet crossed with an engineer's lab notebook.

**The mood:** imagine a refined trade journal for software engineers — the kind you'd see on the desk of a principal engineer who also collects vintage Domus issues. Big confident serif headlines on a deep ink page, monospace running text for system details (dates, version stamps, section numbers), a dot-grid backdrop that recalls graph paper, and one warm signal color used like a highlighter mark in a notebook.

**Why this aesthetic for Rafael:**
- He's a Software Engineering Manager with 12+ years of mobile/SDK depth. The aesthetic needs gravitas, not trendiness.
- "Dark + blue" stays intact — but the dark becomes inkier and the blue becomes more refined; the look stops being read as "GitHub theme."
- The serif/mono pairing reads as "engineer who writes" rather than "developer who downloaded a template," which matches his actual content (he publishes long-form posts).
- The numbered-section + version-stamp motif gives the site a "documented system" feel — which is exactly what a senior engineering manager *is* selling.

**The one unforgettable thing:** the build-stamp header sitting quietly at the top of every page in IBM Plex Mono, e.g. `v.2026.05 · KRAKÓW · RUNNING IN PROD`. It's the one detail a visiting engineer will remember and quote when describing the site to someone else.

## 2. Goals

1. **Commit to the Engineering Atlas concept.** Every component decision flows from it. Restraint, precision, type as a hero, mono as a texture, blue as the dominant chord, amber as the single grace note.
2. **Stop looking like a template.** A visitor should not be able to identify Beautiful Jekyll, Bootstrap, or GitHub-dark within the first 5 seconds.
3. **Hierarchy from typography and rhythm, not from color.** Currently the site uses blue for h3, dividers, badges, tags, icons, links, buttons — diluting the accent. Hierarchy moves to type-scale, whitespace, and surface elevation.
4. **One signal accent, used like a highlighter.** Amber appears in *exactly* three places: active nav indicator, "currently working at" pill, blog post tags. Nowhere else.
5. **One choreographed entrance per page**, then calm. No always-running animation loops.
6. **Accessible.** AA contrast, visible `:focus-visible`, active-page indicator, real alt text, no motion-essential cues.
7. **Content stays.** No copy rewrites. No page additions or removals.

## 3. Non-goals

- No framework swap (still Jekyll + Beautiful Jekyll + Bootstrap 4 utilities).
- No light-mode toggle.
- No content rewrites.
- No new pages.
- No icon-library swap (FontAwesome stays).
- No paid fonts. Everything from Google Fonts so the build stays free and self-contained.

## 4. Audience & tone

Primary visitor: a hiring manager or senior engineer evaluating Rafael for a senior / lead mobile or engineering-management role. Secondary: peers landing from a blog post link.

Tone: **considered, technical, quiet.** Reads less like "look at me" and more like "this person ships things." Confidence comes from typography, density and rhythm — not animation or saturation.

## 5. Visual identity

### 5.1 Typography pairing

| Role | Family | Weights | Source |
| --- | --- | --- | --- |
| Display (hero, page title, blockquote pulls) | **Fraunces** | 400, 600, 900 | Google Fonts |
| Body / UI | **IBM Plex Sans** | 400, 500, 600 | Google Fonts |
| Mono (eyebrows, dates, version stamps, code, section numbers) | **IBM Plex Mono** | 400, 500 | Google Fonts |

**Why Fraunces:** variable serif with `SOFT` and `WONK` axes — has personality without being cartoonish. Used for hero h1 and page titles in weight 600 with slightly increased optical size for warmth. It immediately distances the site from the Inter / Geist / Space-Grotesk-bro club.

**Why IBM Plex Sans:** the engineering counterpart to Helvetica. Slight humanist details (the tail on the `l`, the open `a`) read warm without being cute. Pairs naturally with Fraunces because both have similar x-height proportions.

**Why IBM Plex Mono:** completes the IBM Plex family — coherent grotesque + mono pairing that reads "engineer" without needing to literally write "engineer" anywhere. Carries the eyebrows, version stamps, dates, and code blocks.

Fraunces + Plex Sans + Plex Mono is a deliberate "publication trio" — display serif + workhorse sans + functional mono. The same three-role pairing magazines have used for a century.

### 5.2 Type scale

| Token | Value | Line-height | Letter-spacing | Use |
| --- | --- | --- | --- | --- |
| `--text-display` | `clamp(2.75rem, 5vw + 1rem, 5.5rem)` | 1.02 | -0.03em | Hero h1 (Fraunces 600, slight wonk) |
| `--text-h1` | `clamp(2rem, 2.5vw + 1rem, 3rem)` | 1.1 | -0.02em | Page title (non-hero, Fraunces 600) |
| `--text-h2` | `1.75rem` | 1.2 | -0.01em | Section title (Fraunces 600 — yes, sections too) |
| `--text-h3` | `1.25rem` | 1.3 | -0.005em | Card title (Plex Sans 600) |
| `--text-h4` | `1.0625rem` | 1.4 | 0 | List sub-headings (Plex Sans 600) |
| `--text-body` | `1.0625rem` | 1.65 | 0 | Default (Plex Sans 400) |
| `--text-small` | `0.875rem` | 1.5 | 0 | Captions, meta (Plex Sans 500) |
| `--text-mono` | `0.8125rem` | 1.5 | 0.02em | Dates, file paths (Plex Mono 400) |
| `--text-eyebrow` | `0.75rem` | 1 | 0.12em | Section eyebrows (Plex Mono 500, uppercase) |
| `--text-stamp` | `0.6875rem` | 1 | 0.16em | Build-stamp header (Plex Mono 500, uppercase) |

Body bumps from 16px to ~17px (1.0625rem) — small change, big perceived shift toward "publication." Line-height 1.65 gives breathing room for long-form posts.

### 5.3 Color tokens

**Surfaces — inkier and cooler than GitHub dark.** The page reads as deep ink, not gray.

| Token | Value | Use |
| --- | --- | --- |
| `--surface-0` | `#070A12` | Page background (was #0d1117 — pushes toward deep ink) |
| `--surface-1` | `#0F141F` | Default card / panel |
| `--surface-2` | `#161C2A` | Hover, raised card |
| `--surface-3` | `#1C2335` | Hero panel, CTA section |
| `--border-subtle` | `#1E2536` | Hairline borders inside content |
| `--border-strong` | `#2A3247` | Card borders, table dividers |

**Text — softened off-white, slightly cool gray.**

| Token | Value |
| --- | --- |
| `--text-primary` | `#ECEFF6` |
| `--text-secondary` | `#94A0B8` |
| `--text-muted` | `#5F6A85` |
| `--text-on-primary` | `#070A12` |

**Primary — refined blue, slightly more cobalt.**

| Token | Value | Use |
| --- | --- | --- |
| `--primary` | `#5E80F2` | Links, primary CTA fill |
| `--primary-hover` | `#7A98FF` | |
| `--primary-pressed` | `#4866D6` | |
| `--primary-soft` | `rgba(94, 128, 242, 0.10)` | Tag fill, soft surfaces |
| `--primary-ring` | `rgba(94, 128, 242, 0.45)` | `:focus-visible` ring |

**Signal — warm amber. The one grace note. Used like a highlighter.**

| Token | Value | Use |
| --- | --- | --- |
| `--signal` | `#F5B73B` | Active nav underline, "currently at" pill, blog post tags |
| `--signal-soft` | `rgba(245, 183, 59, 0.12)` | Background of the three signal uses only |
| `--signal-ink` | `#FFD06E` | Hover state for signal |

Why amber, not the v1 mint: amber is warmer, suggests notebook-highlighter, and creates a stronger contrast against the dominant blue. Mint would have been "another cool color" — amber is *temperature contrast*, which is what makes a signal accent actually signal.

### 5.4 Shadows

Designed for dark UIs — every shadow gets an inner-top highlight so cards look lit from above instead of merely outlined.

| Token | Layered value |
| --- | --- |
| `--shadow-sm` | `0 1px 0 rgba(255,255,255,0.04) inset, 0 1px 2px rgba(0,0,0,0.4)` |
| `--shadow-md` | `0 1px 0 rgba(255,255,255,0.05) inset, 0 2px 4px rgba(0,0,0,0.4), 0 12px 32px -12px rgba(0,0,0,0.55)` |
| `--shadow-lg` | `0 1px 0 rgba(255,255,255,0.06) inset, 0 8px 16px -4px rgba(0,0,0,0.5), 0 32px 64px -20px rgba(0,0,0,0.7)` |

### 5.5 Radius

| Token | Value | Use |
| --- | --- | --- |
| `--radius-sm` | `4px` | Tags, inputs |
| `--radius-md` | `8px` | Cards |
| `--radius-lg` | `12px` | Hero / CTA panels |
| `--radius-pill` | `999px` | Status pill ("currently at") |

Smaller radii than v1. Engineering Atlas wants slightly sharper corners — softness is delegated to the serif headings.

### 5.6 Spacing scale

| Token | Value |
| --- | --- |
| `--space-1` | `4px` |
| `--space-2` | `8px` |
| `--space-3` | `12px` |
| `--space-4` | `16px` |
| `--space-5` | `24px` |
| `--space-6` | `32px` |
| `--space-7` | `48px` |
| `--space-8` | `64px` |
| `--space-9` | `96px` |
| `--space-10` | `128px` |

### 5.7 Motion

| Token | Value |
| --- | --- |
| `--ease-out` | `cubic-bezier(0.16, 1, 0.3, 1)` |
| `--ease-in-out` | `cubic-bezier(0.65, 0, 0.35, 1)` |
| `--duration-fast` | `120ms` |
| `--duration-base` | `220ms` |
| `--duration-slow` | `420ms` |

No looping animations. No infinite gradients. No always-on pulses. One choreographed entrance per page (see §9). `prefers-reduced-motion` disables any transition > 50ms.

## 6. Atmosphere

The single biggest visual signature of Engineering Atlas: the page isn't a flat dark surface. It has texture.

### 6.1 Dot-grid backdrop

A subtle 24px dot grid sits behind `<body>`, fading toward the edges:

```css
background-image:
  radial-gradient(circle at 1px 1px, rgba(148, 160, 184, 0.06) 1px, transparent 0);
background-size: 24px 24px;
background-position: -1px -1px;
```

The grid evokes engineering paper. It's barely visible — present but not screaming. It dies out behind cards (cards have a solid `--surface-1`) so it only shows in the negative space.

### 6.2 Grain overlay

A `::before` on `<body>` carries a fixed-position SVG noise layer at ~3% opacity, multiplied. Gives the deep blacks a hint of imperfection so the page reads as "printed" rather than "computer-generated."

### 6.3 Hero radial wash

Each page hero gets a quiet radial gradient in the top-right corner using `--primary-soft` — about 60% of the hero panel, fading to transparent. Lights the page from upper right, the same direction as the inner-shadow highlight on cards. Coherent lighting.

### 6.4 Section rules

A single horizontal hairline between major sections — `1px solid var(--border-subtle)` with the section number `§ 01` etc. sitting in the left margin in IBM Plex Mono. Reads like chapter headings in a technical book.

## 7. Composition

The Engineering Atlas uses an asymmetric layout where it counts, but only where it counts. Most pages stay readable left-aligned.

### 7.1 Container widths

| Container | Max-width |
| --- | --- |
| Narrow (long-form: about, cv, post) | `720px` |
| Standard (home, projects) | `1120px` |
| Wide hero | `1280px` |

Horizontal padding: `--space-5` mobile, `--space-7` desktop. The narrow container *anchors left* on desktop with a right gutter — leaves space for a meta rail (date, tags, share) instead of centering content in the void.

### 7.2 Vertical rhythm

- Section to section: `--space-10` desktop, `--space-8` mobile.
- Hero to first section: `--space-9`.
- Heading to body: `--space-4`.
- Card grid gap: `--space-5`.

Looser overall than v1. Engineering Atlas wants air.

### 7.3 The Build-stamp header

Across every page, immediately above the navbar, a thin band carries the build stamp in IBM Plex Mono 500 uppercase 11px:

```
v.2026.05  ·  KRAKÓW  ·  RUNNING IN PROD
```

Height: 28px. Color: `--text-muted` on `--surface-0`. Border-bottom: `1px solid var(--border-subtle)`.

The text is dynamic where it makes sense:
- `v.{year}.{month}` from Jekyll's `site.time`.
- `KRAKÓW` is the author location (could become a Jekyll variable).
- `RUNNING IN PROD` is a constant — a small joke for engineers reading.

This element is the calling card. It's the one detail that turns "another portfolio" into "the one with the build stamp."

### 7.4 Numbered sections

Every section on every page is prefixed with `§ NN` in IBM Plex Mono. The section number sits in the left margin on desktop (in the negative space outside the content column), and inline above the title on mobile.

Numbering is per-page and starts at 01.

## 8. Components

### 8.1 Navbar

- Sticky below the build-stamp band, total top stack: 28 + 56 = 84px.
- Background: `--surface-0` (not surface-1 — blends with the page so the build stamp visually separates the chrome from the content).
- Brand: `Rafael Skubisz.` in Fraunces 600 italic, `--text-primary`. (The period is part of the wordmark — small editorial flourish.)
- Links: IBM Plex Sans 500, `--text-secondary`, 14px.
- **Active link:** 2px `--signal` (amber) underline 4px below the text. The amber here is the first place a visitor sees the signal color, and it's saying "you are here." That's exactly the kind of "signal" use the color is for.
- Hover: link color → `--text-primary`. No background change.
- `:focus-visible`: 2px `--primary-ring` outline, 3px offset.
- Mobile: collapses to a hamburger; expanded panel slides down with `--ease-out` 220ms.

### 8.2 Page hero

One pattern across Home / Personal / Professional / Projects / Blog:

```
┌──────────────────────────────────────────────────────────┐
│  § 00  RAFAEL.SKUBISZ                                    │   ← left-margin mono
│        ────────────────                                  │
│        Rafael Skubisz.                                   │   ← Fraunces display
│                                                          │
│        Software Engineering Manager,                     │   ← Plex Sans body
│        twelve years of mobile and SDKs.                  │
│                                                          │
│        [ Read the CV → ]   ( contact )                   │   ← Plex Sans buttons
│                                                          │
│                              · · · · · · · · · ·         │   ← radial wash + dots
│                              · · · · · · · · · ·         │
└──────────────────────────────────────────────────────────┘
```

- Surface: `--surface-1`.
- Eyebrow above title in IBM Plex Mono, 12px, tracked 0.12em uppercase. On Home, it's `§ 00  RAFAEL.SKUBISZ`. On other pages: `§ 00  PERSONAL`, `§ 00  CV / EXPERIENCE`, `§ 00  WORK`, `§ 00  WRITING`.
- Title in Fraunces, weight 600, `letter-spacing: -0.03em`. Always followed by a period for the wordmark identity.
- Subtitle in Plex Sans 400 `--text-secondary`, max-width 60ch.
- Hairline divider sitting between eyebrow and title in `--border-subtle`, width 64px.
- Right-side: optional graphic slot. On Professional, this is where the tech-icon constellation lives. On other pages, it's left empty *intentionally* — restraint is part of the aesthetic.
- Hero radial wash sits in the top-right corner.

### 8.3 Section block

```
┌──────────────────────────────────────────────────────────┐
│  § 01                                                    │   ← left margin mono
│  Featured Skills                                         │   ← Fraunces h2
│  A small set of things I do well.                        │   ← Plex Sans body, --text-secondary
│  ──── hairline ──────────────────────────────────────    │
│                                                          │
│  [ children ]                                            │
└──────────────────────────────────────────────────────────┘
```

- Section number `§ NN` sits absolute-positioned in the left margin on desktop (outside the content column), in IBM Plex Mono 500, `--text-muted`.
- Title in Fraunces 600, left-aligned, no underline accent (currently every title has a blue underline — removed).
- Hairline divider below the title: `1px solid var(--border-subtle)`, full width of the content column.
- Bottom margin `--space-10`.

### 8.4 Card

Base card: skill, project, blog post preview, summary.

- Surface: `--surface-1`.
- Border: `1px solid var(--border-subtle)`.
- Radius: `--radius-md`.
- Shadow: `--shadow-sm` at rest, `--shadow-md` on hover.
- Padding: `--space-6`.
- Hover: lift `translateY(-2px)`, border → `--border-strong`. **No color shifts to blue**, no top accent strip.
- Card title in Plex Sans 600 `--text-primary`.

Variants:
- `.card--hero` → `--surface-3`, `--radius-lg`, padding `--space-7`. Used for the home Welcome card and the Let's Connect CTA.
- `.card--linked` → entire card becomes an `<a>`; on hover, border edges toward `--primary` at 40% opacity. Used for project cards and blog post cards.
- `.card--accent` → border-left 3px `--signal`, used only for the "Professional Summary" card on the CV page (it's the page's thesis statement; the amber bar marks it as such).

### 8.5 Tag

Single base recipe:

- Padding: `--space-1 --space-3`.
- Background: `--primary-soft`.
- Border: `1px solid var(--primary)`.
- Color: `--primary`.
- Radius: `--radius-sm`.
- Font: IBM Plex Mono 500, 12px, tracked 0.04em.

The tags becoming mono is part of the engineer-aesthetic — they read like file-extension labels.

Variants:
- `.tag--signal` → amber version (`--signal` border + color, `--signal-soft` fill). **Used only on blog post tags** to mark "this is editorial content."
- `.tag--ghost` → `--text-secondary` color, `--border-subtle` border, transparent fill. For project tech tags where blue would compete with the project title.

### 8.6 Button

- `.btn-primary` → fill `--primary`, text `--text-on-primary`, radius `--radius-md`, padding `--space-3 --space-5`, IBM Plex Sans 600. Hover: `--primary-hover`, shadow `--shadow-md`, translate `-1px`. Pressed: `--primary-pressed`, no translate.
- `.btn-outline-primary` → transparent fill, `1px solid var(--primary)`, color `--primary`. Hover: fill `--primary-soft` (no full color flip — current behavior is too jarring).
- `.btn-ghost` → no fill, no border, color `--text-secondary`, underline on hover. For tertiary inline-style links inside cards.
- `.btn-link-arrow` → text + animated arrow (`→`). Hover slides the arrow 4px right with `--ease-out 220ms`. Used for "Read the CV" hero CTA and "Read More" on blog cards.

All buttons get the `--primary-ring` `:focus-visible` outline.

### 8.7 Inline link

Inside body copy on posts and the personal page:

- Color: `--text-primary` (links don't shout — the underline does).
- Underline: 1px `--primary`, offset 3px.
- Hover: underline → `--signal` (amber). The hover-state-on-link is the one place amber shows up inside running text — small, surprising, in a moment of intent.

### 8.8 Blog post date display

Currently a blue-outlined badge that competes with tags. Redesign:

```
APR 26 2025  ·  5 MIN READ
```

IBM Plex Mono 500, 12px, tracked 0.08em uppercase, `--text-muted`. Middle-dot separator. Read-time is a stretch goal — if Jekyll filter is too painful, drop it.

### 8.9 Skill card

- Compact: 4-up grid on desktop, `--space-5` padding.
- Icon at 28px (not 60px), `--primary-soft` square background with `--radius-sm`. **No circle, no glow, no pulse.** The icon is a label, not a feature.
- Title in Plex Sans 600 `--text-primary` (was blue).
- Body in `--text-secondary`.
- Hover: same lift as base card; icon background brightens to `--primary` with white icon.

### 8.10 Project card

- Linked card variant.
- Plex Mono mono header line at top: `PROJ. NN  ·  KOTLIN / SWIFT`. Acts as a "case file number."
- Fraunces h3 title in `--text-primary`.
- Plex Sans body summary.
- Ghost tags (`.tag--ghost`) for tech stack — they don't compete with the title.
- Single arrow link: "View on GitHub →".

### 8.11 Job entry (CV page)

- Plex Mono date line at top right of each entry: `2023.07 — PRESENT  ·  REMOTE (IE)`.
- Job title in Plex Sans 600.
- Company name in Plex Sans 500 `--primary`.
- Responsibility list: classic `<ul>` with custom marker — a small `›` glyph in `--text-muted`. No per-bullet hover translates.
- Between entries: hairline `--border-subtle` with `§ NN` style number? No — keep that for sections, not entries. Just hairline.

### 8.12 "Currently working at" pill

A new component for the hero/about area:

```
[ ● Currently at Pulsate ]
```

- Pill shape, `--radius-pill`.
- `--signal-soft` background, `--signal` 1px border.
- Animated 8px `--signal` dot on the left, single soft pulse on page entrance (one cycle, then static — not looping).
- Plex Sans 500 13px `--signal` text.

This is the second place amber appears. It says "active right now," exactly the signal use.

### 8.13 Footer

Three-column grid on desktop:

```
┌──────────────────────────────────────────────────────────┐
│  RAFAEL SKUBISZ.                  PAGES         CONNECT  │
│  ────────────                     home          email    │
│  Software Engineering             personal      github   │
│  Manager, twelve years.           professional  linkedin │
│                                   projects      rss      │
│                                   writing                │
│                                                          │
│  © 2026 RS.  ·  v.2026.05  ·  built in jekyll  ·  rss  │
└──────────────────────────────────────────────────────────┘
```

- Surface: `--surface-1`.
- Top border: `1px solid var(--border-subtle)`.
- Brand left, link columns center/right.
- Bottom row: build stamp echo in IBM Plex Mono — closes the loop with the header.

### 8.14 Code block (blog post)

- `pre` background: `--surface-2` (was Dracula's `#1e1e2e` — inconsistent with the rest of the surfaces).
- `code` inline: `--surface-2` background, `--primary` color, `--radius-sm`, Plex Mono 400.
- Syntax highlight: keep the existing Dracula-style palette; it works on `--surface-2`. (Optional polish later: retune one or two colors to harmonize with `--primary`/`--signal`.)
- Copy button: ghost style, mono `[ copy ]` text in `--text-secondary`, hover → `--text-primary`. No light `#333` text.
- Pre wrapper gets a top-right mono filename when available, e.g. `CounterViewModel.kt` in `--text-muted`. (Pure polish, optional.)

## 9. Motion

### 9.1 One choreographed page-entrance

On every page load, three things happen in sequence (total < 600ms):

1. **t = 0:** dot-grid + grain fade in from 0 → final opacity (200ms).
2. **t = 80:** hero eyebrow + title fade-up 16px (`--duration-slow`, `--ease-out`).
3. **t = 200:** subtitle + CTAs fade-up 12px (`--duration-base`).

Cards below the hero do not stagger-animate as the user scrolls. They're just there.

### 9.2 Micro-interactions kept

- Card hover lift + shadow swap.
- Button hover translate and pressed translate.
- `.btn-link-arrow` arrow slide.
- Inline link underline color swap (blue → amber on hover).
- "Currently at" pill single soft pulse on page entry (one cycle).

### 9.3 Micro-interactions cut

- `pulseGlow` infinite loops on skill icons, profile container, summary icons, every "icon-container" in CSS.
- `gradientText` infinite background-position loops on welcome message and personal greeting.
- `float` infinite loop on tech icons.
- Stagger animation-delays on cards/tags that appear after scroll.
- Per-bullet hover translate in responsibility lists.
- The `:hover:after` link-underline grow on the navbar (replaced by the active-state amber underline pattern).

### 9.4 Reduced motion

`@media (prefers-reduced-motion: reduce)` disables every transition longer than 50ms and replaces the entrance choreography with instant rendering.

## 10. Accessibility

- All text passes WCAG AA on its surface. (Re-checked: `--text-primary` on `--surface-0` ≈ 16:1; `--text-secondary` on `--surface-1` ≈ 6.2:1; `--primary` on `--surface-1` ≈ 5.7:1.)
- Every interactive element has a visible `:focus-visible` ring (`--primary-ring`).
- Navbar active link uses both color and underline shape — never color-only.
- `aria-current="page"` on the active nav link.
- Skip-to-content link, visually hidden until focused, lands on `<main id="main">`.
- Real image alt text. Profile photo gets descriptive text, not just "Rafael Skubisz".
- No motion-essential information.
- Tab order verified per page; the build-stamp band is non-focusable.

## 11. Per-page application

### 11.1 Home

- Build stamp + navbar + hero (§ 00) — eyebrow `§ 00  PORTFOLIO`, title "Rafael Skubisz.", subtitle, CTAs.
- § 01 Featured Skills — section block, 4-up grid of redesigned compact skill cards.
- § 02 About / Professional — two-up hero-variant cards.
- § 03 Recent Posts — section block, three linked post cards.
- § 04 Let's Connect — `.card--hero` with the `--signal`-left-border treatment + primary CTAs.
- Footer.

### 11.2 Personal

- Fix broken profile photo (currently a missing image showing alt text). Investigate `/assets/img/avatar.jpg` reference — restore the asset, or fall back to initials in a `--radius-pill` circle on `--surface-2`.
- Hero (§ 00 PERSONAL) — "Rafael." in Fraunces, subtitle.
- § 01 My Story — single long-form text block in narrow container.
- § 02 Beyond Coding / What Drives Me — two-up cards.
- § 03 Connect With Me — single-column card; no border-top accent (not the page's primary CTA, just a closing note).
- Footer.

### 11.3 Professional / CV

- Hero (§ 00 CV / EXPERIENCE) — title, "Currently at Pulsate" amber pill below subtitle, dual CTAs (Download CV, View CV).
- Tech-icon constellation: drop the chaotic absolute-positioned float. Replace with a clean 2×2 grid + larger center, sitting in the hero right column. No `float` animation loop.
- § 01 Professional Summary — `.card--accent` with amber left-border.
- § 02 Key Skills — compact skill cards in 4-up grid.
- § 03 Additional Technologies — tag cloud using mono ghost tags.
- § 04 Professional Experience — job entries (see 8.11). Date moves from floating right to mono header line. No per-bullet hover.
- § 05 Education (if present in source) — same pattern as job entries.
- Footer.

### 11.4 Projects

- Hero (§ 00 WORK).
- § 01 Projects — 3-up grid of project cards (the linked-card variant), falling to 2-up at tablet and 1-up at mobile. With 4 projects today, desktop shows 3 + 1.
- § 02 Areas of Interest (currently exists below the cards) — kept as a section block with body text.
- Footer.

### 11.5 Blog index

- Hero (§ 00 WRITING).
- § 01 Posts — vertical list of post cards. Each card: Plex Mono date line, Fraunces h3 title, body excerpt, amber `tag--signal` row, ghost arrow link "Read More →".
- Pagination: simple `[ ← previous ] [ next → ]` in mono.
- Footer.

### 11.6 Blog post

- Narrow container (720px), left-anchored on desktop with a right rail (date + read-time + tags) when viewport allows.
- Title in Fraunces display.
- Body in Plex Sans 17px / 1.65 line-height.
- Code blocks in `--surface-2` Plex Mono.
- Inline links: blue underline default, amber underline on hover.
- Bottom-of-post: prev/next post in mono small-caps, then a quiet "← back to writing" link.

## 12. CSS cleanup (clear in passing)

The current `assets/css/custom-styles.css` carries light-theme leftovers. The redesign must purge them as part of the foundation pass:

- `.post-preview` (~L482) — white background block.
- `.post-tags a` (~L711) — light-gray pill, color `#6c757d`.
- `.project-card .project-tag` (~L659) — light-gray pill.
- `.post-title`, `.post-subtitle`, `.post-meta` light-theme colors (~L677–697).
- `.collapsible`, `.collapsible-content`, `.full-code-button`, `.copy-button` light surfaces (~L731–834).
- Second `.header-section` rule (~L547) with `#f8f9fa` background.
- Second `footer` rule (~L577) with `#f8f9fa` background.
- Duplicate `@media (max-width: 767px)` block at the bottom of the file.

In `_config.yml`, Beautiful Jekyll's color variables (`navbar-col: #F8F9FA`, `link-col: #007BFF`, `mobile-theme-col: #007BFF`, etc.) point to light-theme values. Update to match the new token set so default Beautiful Jekyll styling doesn't fight the custom CSS.

## 13. Open decisions

1. **Build-stamp location text.** "KRAKÓW" is the obvious choice given Rafael lives there. Alternative: drop the city, show `42.0094km altitude` style "engineering nonsense" — riskier but more memorable. Default: KRAKÓW.
2. **Project case-file numbers.** The plan calls for `PROJ. 01` etc. on project cards. Confirm Rafael is OK seeing his work catalogued.
3. **The wordmark period.** "Rafael Skubisz." (with period) in the brand and hero. Small editorial flourish — easy to drop if it reads as too cute.
4. **Read-time stat on blog posts.** Stretch goal; depends on whether the Jekyll filter to compute it is acceptable. Acceptable to drop.
5. **Right meta-rail on blog posts.** Looks great at ≥1200px viewports; falls back to inline at smaller widths. Confirm the visual cost is worth it (it adds one more responsive breakpoint to maintain).

## 14. Success criteria

The redesign is done when:

1. A first-time visitor cannot identify Beautiful Jekyll, Bootstrap, or GitHub Dark within 5 seconds.
2. The build-stamp header reads correctly on every page and renders at the correct height across viewports.
3. No light-mode color values (`#fff`, `#f8f9fa`, `#343a40`, `#6c757d`, `#95a5a6`, `#0069d9`, etc.) remain in `assets/css/custom-styles.css`.
4. The amber `--signal` color appears in **exactly three places** sitewide: navbar active underline, "currently at" pill, blog post tags. Nowhere else.
5. Every interactive element has a visible `:focus-visible` state.
6. Each section on every page is numbered with `§ NN` in IBM Plex Mono.
7. Side-by-side screenshots (before/after, 1440 + 390) for each page show: deeper-ink background with dot-grid texture, Fraunces hero serif, mono section numbers and dates, unified tag style, one-not-six places amber shows up.
8. Lighthouse Accessibility ≥ 95 on Home, Personal, Professional, Projects, Blog index, and one blog post.
9. The site looks unmistakably like itself — not like a template, not like Linear, not like Vercel.

---

Once this design doc is approved, the next deliverable is the implementation plan: an ordered, reviewable sequence of changes with file-level diffs and review checkpoints.
