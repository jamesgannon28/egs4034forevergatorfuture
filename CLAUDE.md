# CLAUDE.md — ForeverGatorFuture Website

This file gives Claude Code full context on the ForeverGatorFuture project so you can work on it effectively without needing to re-derive intent from the code.

---

## What This Project Is

**ForeverGatorFuture** is a nonprofit private accreditation body for AI product companies. It certifies organizations that voluntarily meet high standards of care across three pillars: Environmental, Economic, and Societal responsibility. All standards are publicly published and were developed collaboratively by industry members.

The website is the organization's primary public-facing presence. It communicates the mission, publishes principles, lists accredited organizations, and accepts compliance registrations.

---

## File Structure

```
index.html        ← The entire website. Single-file, no build step, no framework.
CLAUDE.md         ← This file.
```

The site is intentionally a **single HTML file** so it can be deployed by simply dragging it onto Netlify, GitHub Pages, or any static host. Do not break it into multiple files unless the user explicitly asks. Do not introduce a build system, npm, or a framework unless asked.

---

## Architecture: How the Site Works

The site uses **vanilla JS client-side routing** — no URL changes, no router library. All four pages live inside `index.html` as `<div class="page">` elements. Only one is visible at a time via the `.active` CSS class.

### Page switching
```js
function showPage(name) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById('page-' + name).classList.add('active');
  window.scrollTo(0, 0);
}
```

Navigation links and buttons call `showPage('home' | 'compliance' | 'accredited' | 'beliefs')`.

### Page IDs
| Page | HTML Element ID | Nav Label |
|------|----------------|-----------|
| Home / Landing | `page-home` | Home |
| Compliance Registration | `page-compliance` | Register for Compliance |
| Accredited Organizations | `page-accredited` | Accredited Orgs |
| Beliefs & Structure | `page-beliefs` | Beliefs & Structure |

---

## Design System

All design tokens are CSS custom properties on `:root`. **Always use these variables — never hardcode colors or fonts.**

### Colors
| Variable | Value | Usage |
|----------|-------|-------|
| `--bg` | `#f7f6f2` | Page background (warm off-white) |
| `--white` | `#ffffff` | Card/surface background |
| `--ink` | `#1a1a1a` | Primary text |
| `--ink-soft` | `#4a4a4a` | Secondary/body text |
| `--ink-muted` | `#888` | Tertiary/placeholder text |
| `--accent` | `#2e6b4f` | Primary green — buttons, highlights, links |
| `--accent-light` | `#e8f1ec` | Light green tint — stat boxes, badges, notes |
| `--accent-mid` | `#4a9970` | Mid green — available for hover states |
| `--border` | `#ddd9d0` | All borders and dividers |
| `--gold` | `#b8986a` | Decorative gold — not yet in heavy use |

### Typography
| Variable | Value | Usage |
|----------|-------|-------|
| `--serif` | `'Cormorant Garamond', Georgia, serif` | Headings, hero titles, org names |
| `--sans` | `'DM Sans', sans-serif` | Body, labels, nav, buttons |

Both fonts are loaded from Google Fonts in `<head>`. Do not swap them without updating the import.

### Key Spacing
- Horizontal page padding: `5vw` on all major sections
- Nav height: `68px` — all pages have `padding-top: 68px` to clear the fixed nav
- Section padding: `5rem 5vw`

---

## Page-by-Page Notes

### Home (`page-home`)
- Two-column hero: left = text/CTAs, right = stats card
- Three pillar cards (Environmental, Economic, Societal) in a grid
- Four-step "How It Works" process row
- Shared footer with nav links

### Compliance Registration (`page-compliance`)
- A centered form card (max-width 720px)
- Fields: org name, legal type, contact info, website, product description, org size, self-assessment checkboxes, ethics policy dropdown, notes
- On submit, calls `submitForm()` which shows a toast notification (no actual backend — form is currently front-end only)
- **If adding a backend**: hook into the `submitForm()` function. The form fields do not have `name` attributes yet — add them before wiring to a backend.

### Accredited Organizations (`page-accredited`)
- Organizations are stored as a JS array (`const orgs`) at the bottom of the `<script>` block
- Each org object: `{ name, type, initial, desc, tags[] }`
- Tags must be one or more of: `'environmental'`, `'economic'`, `'societal'`
- `renderOrgs(filter)` renders cards; `filterOrgs(tag, btn)` handles the filter buttons
- **To add a real org**: add an object to the `orgs` array. The `initial` field is the first letter shown in the avatar circle.
- Cards are currently placeholder/example data — replace with real accredited organizations

### Beliefs & Structure (`page-beliefs`)
- Two-column layout: sticky TOC sidebar (left) + scrollable content (right)
- Six sections with IDs `#b1` through `#b6`
- TOC links use `href="#b1"` etc. for in-page scroll anchoring
- Sidebar is hidden on mobile via `display: none` in the responsive breakpoint
- Content sections use `.belief-list` (arrow-prefixed list items) and `.author-note` (green callout block)

---

## Components & Reusable Patterns

### Buttons
```html
<a class="btn btn-primary" href="#" onclick="showPage('compliance')">Label →</a>
<a class="btn btn-outline" href="#" onclick="showPage('beliefs')">Label</a>
<button class="btn btn-primary" onclick="submitForm()">Submit →</button>
```

### Section Header Pattern
```html
<p class="section-tag">Short Label</p>
<h2 class="section-title">Main Heading</h2>
<p class="section-lead">Supporting description text...</p>
```

### Accent Callout Block
```html
<div class="author-note">
  <p>Italicized note text in green.</p>
</div>
```

### Org Card (dynamically rendered)
Org cards are built in JS inside `renderOrgs()`. To change the card layout, edit the template literal in that function.

### Toast Notification
```html
<div class="toast" id="toast">Message here</div>
```
Shown/hidden via `display: block` in JS. Auto-hides after 4 seconds.

---

## Responsive Behavior

Breakpoint: `max-width: 768px`

At mobile widths:
- Hero becomes single-column
- Pillar grid becomes single-column
- Steps grid becomes 2-column
- Beliefs TOC sidebar is hidden
- Nav hides all links except the CTA button (Register for Compliance)

---

## What Doesn't Exist Yet (Known Gaps)

- **No backend** — the compliance form does not actually submit data anywhere. It needs a form handler (e.g. Netlify Forms, Formspree, or a custom endpoint).
- **No real accredited org data** — the six orgs in the JS array are placeholders. Replace with actual accredited organizations.
- **No CMS** — content is hardcoded in HTML. If content needs to be editable by non-developers, a headless CMS or admin panel would need to be added.
- **No custom domain** — the site is ready to deploy on any static host but uses whatever subdomain the host provides.
- **No analytics** — no tracking script is included. Add one to `<head>` if needed.
- **No email confirmation** — the registration form shows a UI toast but sends nothing. Wire `submitForm()` to a real endpoint.

---

## Deployment

The site is a single static HTML file. Deploy by:

1. **Netlify (easiest):** Drag `index.html` to netlify.com/drop → live in seconds at `[random].netlify.app`
2. **GitHub Pages:** Push to a repo, enable Pages in settings → live at `[username].github.io/[repo]`
3. **Vercel:** `vercel deploy` from the folder or connect the repo
4. **Any static host:** Upload `index.html` — no server required, no build step

---

## Conventions to Follow

- Keep everything in `index.html` unless the user asks otherwise
- Use CSS variables for all colors and fonts — no hardcoded values
- Add new pages as `<div id="page-[name]" class="page">` and register them in the nav via `showPage('[name]')`
- New sections should follow the existing `section-tag → section-title → section-lead` header pattern
- Match the existing spacing and border conventions (`var(--border)`, `5vw` padding, `border-radius: 10px` for cards)
- Org filter tags must stay within the set: `environmental`, `economic`, `societal`