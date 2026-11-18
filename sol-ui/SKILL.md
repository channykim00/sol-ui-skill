---
name: sol-ui
description: Sol UI — a calm, list-first, responsive design system (mobile app, marketing site and desktop dashboard/console layouts) with a green brand color, Pretendard type, generous whitespace and Korean 해요체 copy. Use whenever building or restyling UI for web projects (React, Vite, Next.js, plain HTML prototypes) — new screens, pages, components, landing pages, dashboards, admin consoles, forms, tables, dialogs, toasts — or when the user says "sol-ui", "솔 UI", "솔 디자인", "내 디자인", "내 디자인 시스템", "우리 스타일로", "그린 테마로", or asks for a clean/minimal look in their usual style. Do NOT use for Apps in Toss mini-apps (those must use TDS).
---

# Sol UI

A calm, minimal, list-first design system. One clear message per screen, big bold titles, lots of whitespace, a single green accent on a near-neutral canvas.

## Files

- `assets/tokens.css` — all design tokens (color, type, spacing, radius, motion, dark mode). Copy verbatim.
- `assets/components.css` — `.sol-*` component classes. Copy verbatim.
- `references/components.md` — React + TS components that wrap those classes. Read before writing any UI.
- `references/writing.md` — Korean UX writing rules. Read before writing any user-facing text.
- `references/layout.md` — layout modes (App / Site / Console), breakpoints, desktop rules, shell/panel/table components. Read before building any page meant for desktop or tablet.

## Setup in a project (first time only)

1. Copy `assets/tokens.css` and `assets/components.css` into `src/styles/` and import them at the root (tokens first).
2. Add Pretendard (link in `references/components.md`).
3. `npm i lucide-react` for icons.
4. Create the components from `references/components.md` in `src/components/ui/` and wrap the app in `<FeedbackProvider>`.

If the project already has these files, reuse them — never duplicate tokens.

## Core rules

### Color
- Components use **semantic tokens only** (`--color-primary`, `--text-sub`, …). Never hard-code hex values, never use raw `--g-*` / `--n-*` in components.
- Green is an accent, not a background. Per screen: one primary button + small accents (key number in the title, progress, selected state, row icons). Everything else is neutral.
- `--color-primary` (#09855F) for anything with text on it or green text on white — it passes 4.5:1. `--color-accent` (#12A97B) only for non-text graphics (bars, icons, charts).
- Status colors only for status. Never use red for emphasis.
- Light + dark both supported through tokens; only test that semantic tokens are used and dark mode follows automatically.

### Layout mode (decide first)
- **App** (`.sol-app`): mobile-first 480px column — phone-centric services and flows.
- **Site** (`.sol-site`): top nav + containers + sections — landing, marketing, docs.
- **Console** (`.sol-console`): sidebar + sunken bg + panels — dashboards, admin, data-heavy logged-in areas.
- If the user doesn't say, infer from the product (lots of data on desktop → Console; public page → Site; else App) and state the choice in one line. Details and templates in `references/layout.md`.
- All modes are mobile-first and fluid: phone layout first, then enhance at 768px and 1024px. One layout that reflows — never separate desktop/mobile pages.

### Layout (App mode / phone widths)
- Mobile-first single column, `max-width: var(--app-max-width)` (480px), centered on desktop. Design at 375px first.
- Screen order is fixed: **AppBar → PageHeader → (Progress/Tabs) → SectionHeader + ListRows → SectionGap → … → BottomCTA**.
- Horizontal padding = `--gutter` (24px) everywhere. Spacing on the 4pt scale only.
- Separate sections with `SectionGap` (12px grey band), not borders or cards. Use `Divider` sparingly between rows of different kinds.
- Prefer `ListRow` over cards. Cards only for genuinely visual content (images, charts).

### Layout (Site / Console at desktop widths)
- Cap width with `.sol-container` (1120px), `--narrow` (720px) for forms/articles, `--wide` (1360px) for data dashboards.
- Console: sunken background, content grouped in white `.sol-panel`s (no shadows). Panels replace SectionGap.
- Primary action moves to the page header (top-right) or the end of a form (`.sol-actions`, right-aligned). No full-width sticky bottom button on desktop.
- ListRows stay the default; use `.sol-table` only when comparing 3+ attributes across rows. Below 768px, wide tables become ListRows.
- Grids only for peers (stats, features). Main + summary uses `.sol-split` (2fr / 1fr). Stat cards go in `.sol-grid--stats` (2 cols on phones, max 4 on desktop).
- Hover styles only for real pointers (built in); never hide info behind hover.
- Desktop type scale bumps titles only (title1 32px, display 44px); body text stays 15–16px.
- One `PageHeader` title per screen, ≤ 2 lines. Highlight at most one key word/number in `--color-primary`.

### Components
- Navigation: `AppBar` with back button on every non-root screen; max one trailing icon button.
- Main action: a single primary `Button` inside `BottomCTA`. Optional secondary on the left (35% width). Never two primary buttons.
- Confirmation: always `useDialog().confirm`. Left button is "닫기".
- Feedback after an action: `useToast()` — one line, past tense.
- Bottom sheets: only on explicit tap. Never on page entry, never on back.
- Selectable lists: `ListRow selectable` inside `role="radiogroup"`; the CTA label reflects the selection.

### Typography
- Pretendard only. Weights 400 / 500 / 600 / 700.
- Titles use `.t-title1` (26/700, tight tracking). Section titles `.t-heading` (17/700). Row titles 16/600. Descriptions 15 `--text-sub`. Meta 13–14 `--text-weak`.
- Numbers use `.t-num` (tabular figures).
- Korean text wraps between words (`word-break: keep-all`, set on body). Never let a word split across lines.

### Icons & graphics
- `lucide-react`, stroke 2. 24px in AppBar, 22px in row leading, 20px in toast.
- Monotone. One icon per spot. No decorative particles, heavy gradients, or sad/pleading illustrations.

### Interaction
- Every tap gives instant feedback (`:active` states are built in).
- Anything slower than ~300ms shows a skeleton or a busy button (`loading`). Nothing hangs silently.
- Respect `prefers-reduced-motion` (built into components.css).
- Touch targets ≥ 44px.

### Accessibility
- Text contrast ≥ 4.5:1 (tokens already satisfy this — don't put `--text-weak` on long body text).
- Icon-only buttons need `aria-label`. Dialogs use `role="alertdialog"` and focus "닫기" first. Tabs use `role="tab"` + `aria-selected`.

### Copy
- Follow `references/writing.md`: 해요체, active voice, positive framing, buttons that predict the next action, no dark patterns.

## Don'ts
- Don't copy third-party design-system assets (component code, fonts, icon sets, Figma kits, logos). This system is original: tokens, CSS and components here are the source of truth.
- Don't add new colors, shadows or radii ad hoc — extend `tokens.css` first if truly needed.
- Don't use cards with shadows for list content.
- Don't center-align body text or lists (only empty states and dialogs' content may center).

## Checklist before finishing a screen
- [ ] Layout mode chosen (App / Site / Console) and stated
- [ ] App: AppBar → PageHeader → content → BottomCTA · Console: bar → PageHead → stats → panels · Site: topnav → hero → sections → footer
- [ ] Only semantic tokens, no hex in components
- [ ] One primary action, label predicts the result
- [ ] Back works; root screen has no back button
- [ ] Confirmations via dialog with "닫기"
- [ ] Loading state for anything async
- [ ] Copy is 해요체, no vague CTAs, no dark patterns
- [ ] Looks right at 375px, 768px and 1280px+, and in dark mode
- [ ] Desktop: content width capped, one primary action placed top-right or end of form
