---
name: "design-system-architect"
description: "Create a modern design system for any product or company, or audit an existing codebase or live URL and recommend better design choices, tokens and components."
---

# Design System Architect

Two modes. Pick from the request; if both apply, run Review first and feed its findings into Create.

- **Create**: build a complete, documented design system for a product, business or company, from whatever sources exist (brand files, Figma, decks, a codebase, a website, or nothing but a description).
- **Review**: audit an existing codebase or URL(s), extract the design system it actually uses, score it, and prescribe a better one.

The reference shape for every system is a living brand book + tokens + previews + components + UI kits (modelled on a real fintech system: tiny brand palette, one accent blue with a 50-950 ramp, a single brand typeface with a web fallback, 4pt spacing, pill CTAs, cool-tinted layered shadows, a watermark pattern built from the logo mark).

---

## 0. Intake (keep it short)

Find sources before asking anything: attached files, connected folders, Figma links/connector, repos, URLs in the message, an existing Design System artifact. Ask (one AskUserQuestion, max 3 questions) only for what blocks you:

- Product/company name, category, audience
- Brand personality in 3 adjectives (if no sources)
- Platforms (web app, marketing site, mobile, print/decks)
- Light only, or light + dark

If the user is unattended, assume sensible defaults and state them at the top of the deliverable.

---

## 1. Inventory the sources (Create and Review)

Enumerate everything before designing. Track it as a checklist and report what is left at the end.

| Source | How to extract |
|---|---|
| Figma | variables/styles (colour, type, effects), component sets and their variants, icon set names, cover/brand pages |
| Codebase | `tailwind.config.*`, CSS custom properties (`:root`), SCSS/LESS variables, theme files (MUI/Chakra/styled-components), `tokens.json`, component folders, icon imports, font loading (`@font-face`, next/font, Google Fonts links) |
| URL | load the page in a browser tool; read computed styles of body, headings, buttons, links, inputs, cards; collect every distinct colour, font-family, font-size, radius, shadow; screenshot desktop + mobile; note logo/favicon files |
| Brand assets | logos, icon marks, patterns, mockups: copy the files, never redraw or approximate a mark |
| Nothing | build a SMALL starter system (see section 3 minimums) and say so |

Rules: exact values always; code beats screenshots; never invent a value and pass it off as sourced. Mark every value as **sourced**, **derived** (e.g. a ramp generated from a sourced hex) or **proposed**.

---

## 2. Brand fundamentals (the brand book's opening)

Write these sections in `README.md`, in this order:

1. **Sources**: table of source, what it gave, status.
2. **Brand at a glance**: name and exact spelling/casing rules, category, audience, pillars, sub-brands, voice in one line.
3. **Content fundamentals**: tone, person (we/you), casing registers (headline vs body vs micro-labels), emphasis devices, CTA patterns, emoji policy, real copy examples to reuse.
4. **Visual foundations**: colour, type, spacing/grid, backgrounds, signature texture or pattern, radii, buttons, cards, borders, shadows, transparency/blur, motion, hover/press states, layout rules. Each rule names the token it uses.
5. **Iconography**: logo/mark usage, clear space, allowed colours, UI icon family (stroke, size, style), what is forbidden.
6. **Substitutions and flags**: licensed fonts that cannot ship as webfonts and their fallback, approximations, open questions.
7. **Index**: every file and what it is.

Keep authors' words when they exist; mark anything you wrote as a proposal.

---

## 3. Foundations (tokens)

### Colour
- Brand palette: keep it small (2-5 brand colours). One primary accent, used sparingly for action, trust and interaction, not as a wash.
- Ramps: generate 50-950 for the primary (and any secondary) so the sourced hex lands on its natural step (often 500 or 600). Use OKLCH to keep perceptual steps even.
- Neutrals: 8-11 steps from near-black ink to hairline grey; slightly tint toward the brand hue (cool brand → cool greys).
- Semantic roles (what components use): `fg-1/2/3`, `fg-on-accent`, `bg-1/2/3`, `border`, `border-strong`, `accent`, `accent-hover`, `accent-soft`, `focus-ring`, `success`, `warning`, `danger`, `info` (each with a soft background variant).
- Themes: light first; dark is a remap of semantic roles, not an inverted ramp. Raise dark surfaces with lighter greys, not shadows.
- Contrast: body text ≥ 4.5:1 on its background in every theme; large text (24px+), icons, control borders and focus rings ≥ 3:1. Verify programmatically (WCAG relative luminance). States that must be told apart differ in lightness, not hue alone; prefer blue/orange over red/green for paired meaning.

### Type
- One brand family (two at most: display + text). Licensed fonts get a documented fallback stack and a Google Fonts substitute.
- Scale: display, h1, h2, h3, title, body-lg, body, sm, caption, label. Ratio about 1.2-1.333; display can break the ratio.
- Line height: tight (1.0-1.1) for display, 1.2-1.3 headings, 1.45-1.6 body. Tracking: negative (-0.01 to -0.02em) at large sizes, positive (+0.06 to +0.1em) on all-caps labels.
- Weights in use: name them (e.g. 300/400/500/700) and say where each goes.

### Spacing, radius, elevation, motion
- Spacing: 4pt base; steps 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80, 96.
- Radius: sm 6-8, md 10-12, lg 16, xl 24, pill 999. State which components use which.
- Shadows: 3-4 layered, low-spread shadows tinted with the brand's dark hue (never pure black drops), plus an optional accent glow for primary CTAs.
- Motion: durations (120/180/240ms), easing (ease-out for enter, ease-in for exit), hover rise 2-4px, press scale 0.98. Respect `prefers-reduced-motion`.
- Breakpoints and layout grid: e.g. 640/768/1024/1280; content max-widths; app shell (sidebar + top bar + canvas).

### Token file formats
Write tokens twice, from one source of truth:
- `tokens.css`: `:root { --name: value }` plus `[data-theme="dark"]` and `@media (prefers-color-scheme: dark)` remaps.
- `tokens.json`: every family as a LIST: `{"tokens":[{"name","value","usage"}]}`; colour carries `themes` and per-theme values `{"light":"#..","dark":"#.."}`; type uses `fonts`, `families`, `groups[].styles[]`. Names `[A-Za-z0-9][A-Za-z0-9_.-]*`, unique. Colour values in hex/rgb/hsl/oklch or `{alias}`; no named colours, `var()` or `color-mix()`. Every token has a usage note.
- Offer a Tailwind config / DTCG export only if the user's stack needs it.

**Minimums for a starter system** (no sources): 6-10 colours in one theme, 5-7 text styles, 4+ spacing steps, 3 radii, one-paragraph README; say it is a starting point.

---

## 4. Components

Build the families the product needs; for a typical SaaS/fintech product:

- **Actions**: Button (primary solid, secondary outline, ghost, destructive; sm/md/lg; loading, disabled, icon-only), Link, IconButton
- **Inputs**: TextField, Select, Checkbox, Radio, Switch, DatePicker, SearchField, with label, hint, error, focus-ring
- **Display**: Card, Badge/Chip, Tag, Avatar, Stat/KPI tile, Table, EmptyState, Skeleton
- **Navigation**: Sidebar, TopBar, Tabs, Breadcrumbs, Pagination
- **Feedback**: Alert/Banner, Toast, Modal/Dialog, Tooltip, Progress
- **Domain**: whatever the product is about (e.g. tax: filing status tracker, deduction row, deadline card; AI: assistant panel, suggestion chip)

For each component write a short guideline: first sentence = summary; anatomy; variants and when to use each; tokens it uses; states; accessibility (roles, keyboard, focus, labels); do/don't. Build a live preview (small HTML document using the tokens). If a bundle is built, it is a single classic script assigning `window.<Namespace>` with typed props documented in `index.d.ts`.

---

## 5. Previews and UI kits

- One specimen card per foundation: brand colours, ramp, neutrals, semantic colours, type family, display scale, body scale, spacing, radii, elevation, logos, icon marks, pattern, brand-in-use.
- UI kits that recreate real surfaces with the system: a marketing site (nav, hero, features, CTA, footer) and/or product app (shell, dashboard, list/table, detail, settings, assistant). Use realistic domain copy in the brand voice, not lorem ipsum.
- A cover page for the system: name, mark, one-line promise, palette strip.

---

## 6. Delivering

1. If the Artifact tool lists a **Design System** type, create the system from it (`type_url`, `title`) and follow the SKILL.md it returns for file paths and the index; that is the preferred home. To revise an existing one, read it first and change only what was asked.
2. Otherwise publish a self-contained HTML brand book as an artifact, and deliver `tokens.css`, `tokens.json`, component files and assets as files (written to a connected folder when there is one).
3. Copy logos, icons and fonts as real files; never paste base64 into chat.
4. Finish with: link, what was sourced vs proposed, what remains of the inventory, one offer to take it further (components, dark theme, Figma sync, code integration).

---

## 7. Review mode (codebase or URL audit)

### Gather
- **Codebase**: search for colour literals (`#[0-9a-fA-F]{3,8}`, `rgb(`, `hsl(`), font-family declarations, font-size/px values, border-radius, box-shadow, z-index, breakpoints, inline styles, duplicated components (multiple Button/Modal implementations), icon libraries in use. Count occurrences.
- **URL(s)**: open each page (desktop 1440 and mobile 390), read computed styles, collect distinct values, screenshot key screens, run a contrast check on text/background pairs, note focus visibility by tabbing, check `prefers-color-scheme` and reduced motion.

### Extract the de-facto system
Cluster near-duplicate values (e.g. 14 greys → 6 steps; 9 radii → 4). Show the actual palette, type scale, spacing and radius in use, with counts.

### Score (0-5 each, with evidence)
1. Colour (palette discipline, semantic use, contrast)
2. Typography (scale consistency, hierarchy, readability)
3. Spacing and layout (grid adherence, rhythm, responsiveness)
4. Components (consistency, states, duplication)
5. Accessibility (contrast, focus, labels, keyboard, motion)
6. Brand expression (distinctiveness, voice, imagery)
7. Token architecture (hard-coded values vs tokens, theming readiness)

### Findings
Rank by severity: **Critical** (accessibility failures, broken flows), **High** (inconsistency users notice, brand dilution), **Medium**, **Nit**. Each finding: where (file:line or URL + element), what, why it matters, the exact fix (token name and value, or component change).

### Prescribe
- A consolidated token set (old value → new token mapping table).
- Before/after for the 3-5 highest-impact screens or components (HTML mockup when useful).
- A phased migration plan: 1) tokens + contrast fixes, 2) core components, 3) screens, 4) dark mode/polish. Note breaking changes.
- Deliver as a doc or HTML report; offer to then build the full system via Create.

Never edit the user's code in Review mode unless they ask.

---

## 8. Quality checklist

- [ ] Every value marked sourced / derived / proposed; nothing invented silently
- [ ] Logos and marks are the real files; no logo means plain wordmark type and a note
- [ ] Contrast verified in every theme and every preview
- [ ] Every token has a usage note; every component lists the tokens it uses
- [ ] Brand name spelled consistently everywhere, including sample copy
- [ ] Fonts: real files or documented licensed fallback
- [ ] Copy follows the brand's own voice rules (casing, emoji policy, CTA pattern)
- [ ] Responsive at 390px with no horizontal scroll
- [ ] Inventory reported: built, skipped, remaining

## Anti-patterns (do not ship)

- Generic AI tropes: blue-to-purple gradients everywhere, emoji feature cards, left-border accent cards, glassmorphism with no reason
- An accent colour used as a background wash instead of for action
- Pure black drop shadows; more than 4 shadow levels
- Arbitrary values (13px, 17px, 22px radius) outside the scale
- Redrawn or recoloured logos
- Red/green as the only signal for status
- Token maps (DTCG nested objects) where a list-shaped tokens.json is required