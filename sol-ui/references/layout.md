# Layout modes & desktop rules

Every project picks **one primary mode** up front. A project can mix (e.g. Site for marketing pages + Console for the logged-in area), but each page belongs to exactly one mode.

| Mode | Root class | Use for | Desktop look |
|---|---|---|---|
| **App** | `.sol-app` | Mobile-first services, tools used mostly on phones, mini-apps, flows (sign-up, checkout) | Centered 480px column on plain bg |
| **Site** | `.sol-site` | Landing, marketing, docs, blog, content pages | Sticky top nav + centered container + full-width sections |
| **Console** | `.sol-console` | Dashboards, admin, settings-heavy logged-in products | Fixed left sidebar + sunken bg + white panels |

How to decide: if most users are on desktop and need to see/compare lots of data → Console. If it's a public page to read/convert → Site. Otherwise → App.

## Breakpoints

Mobile-first. Write the phone layout first, then enhance.

| Name | Min width | What changes |
|---|---|---|
| base | 0 | Single column, `--page-gutter` 20px |
| md | 768px | Gutter 32px, 2-col grids, BottomCTA → inline right-aligned, bottom sheet → centered modal |
| lg | 1024px | Gutter 40px, grid gap 24px, 3/4-col grids, sidebar visible, top-nav links visible, larger title scale |
| xl | 1280px | Nothing new — containers stop growing |

Never design a separate layout per device; one fluid layout that reflows.

## Desktop rules

1. **Containers cap width.** Content never stretches past `--container` (1120px). Forms/settings/articles use `--container-narrow` (720px). Data-heavy console pages may use `--container-wide` (1360px). Running text max `--measure` (68ch).
2. **Sunken background + white panels.** On Console pages the bg is `--color-bg-sunken`; group content in `.sol-panel` (radius 16, no shadow). Panels replace the mobile `SectionGap`. Site pages stay on plain bg and alternate with `.sol-section--sunken` bands.
3. **Lists stay lists.** ListRow is still the default inside panels. Switch to `.sol-table` only when users compare **3+ attributes across rows** (dates, amounts, status). Tables: numbers right-aligned + tabular, one status Badge column max, row height 56.
4. **One primary action per view.** On desktop it moves to the page header (`.sol-pagehead__actions`, top-right) or the end of a form (`.sol-actions`, right-aligned, secondary on the left). No sticky full-width bottom button on desktop.
5. **Grids are for peers.** Use `.sol-grid--2/3/4` only for items of equal importance (stat cards, feature cards). For primary content + secondary summary use `.sol-split` (2fr / 1fr).
6. **Stat cards first, detail below.** Console overview pages: row of 2–4 `Stat` panels in `.sol-grid--stats` (2 columns on phones, 4 on desktop) → main chart/list panel → secondary panels. Max 4 stats in a row; keep stat labels ≤ 8 characters and deltas to one short line so they fit at phone width.
7. **Navigation.**
   - Console: sidebar 248px, items 44px, max ~8 top-level items, group labels allowed. Active = `--color-primary-weak` bg + `--color-primary-text`. Below lg the sidebar is an off-canvas drawer opened from a menu button in `.sol-console__bar`.
   - Site: top nav 64px, ≤ 5 links, one primary button on the right. Below lg links collapse into a menu button (drawer or sheet).
   - App: unchanged (AppBar per screen).
8. **Hover is an enhancement.** Hover states only under `@media (hover: hover)` (already in components.css). Never hide information behind hover only.
9. **Dialogs stay small.** Dialog max 400px, sheet-as-modal max 480px. Large editing goes to its own page or a side panel, not a giant modal.
10. **Type scales up once.** At lg, Site/Console use title1 32px and display 44px (scoped in CSS). Body text stays 16/15px — never bump body size on desktop.
11. **Density.** Console may use `ListRow size="sm"` and 48px buttons. Site keeps generous spacing (sections 80px tall padding on desktop).
12. **Keyboard.** Every interactive element reachable by Tab, visible `:focus-visible` ring, Esc closes dialogs/drawers, drawer traps focus while open.

## Page templates

### Console page

```
┌────────────┬──────────────────────────────────────────────┐
│ Brand      │ [☰ md only]  Page title          [actions]   │  ← .sol-console__bar (sticky)
│            ├──────────────────────────────────────────────┤
│ ▣ Overview │  PageHead: title + description   [Primary]   │
│ ▢ Records  │  ┌─Stat─┐ ┌─Stat─┐ ┌─Stat─┐ ┌─Stat─┐         │  ← .sol-grid--stats
│ ▢ Goals    │  └──────┘ └──────┘ └──────┘ └──────┘         │
│            │  ┌─Panel (2fr)──────────┐ ┌─Panel (1fr)──┐   │  ← .sol-split
│ GROUP      │  │ chart / table         │ │ ListRows     │   │
│ ▢ Settings │  └──────────────────────┘ └──────────────┘   │
│ ─────────  │                                              │
│ user       │                                              │
└────────────┴──────────────────────────────────────────────┘
```

### Site page

```
┌──────────────────────────────────────────────────────────┐
│ Brand    Link Link Link                    [Primary CTA] │  ← .sol-topnav (sticky)
├──────────────────────────────────────────────────────────┤
│   Hero: display title (≤2 lines) + desc + 1–2 buttons    │  ← .sol-hero in .sol-container
├──────────────────────────────────────────────────────────┤
│   Feature grid (.sol-grid--3) — icon, heading, 2 lines    │  ← .sol-section
├──────────────────────────────────────────────────────────┤
│   Sunken band: proof / numbers / FAQ (ListRows)          │  ← .sol-section--sunken
├──────────────────────────────────────────────────────────┤
│   Final CTA                                              │
│   Footer                                                 │
└──────────────────────────────────────────────────────────┘
```

---

## React components

Add these to `src/components/ui/` next to the ones in `components.md`.

### ConsoleShell + Sidebar

```tsx
import { useEffect, useState, type ReactNode } from 'react';
import { Menu } from 'lucide-react';

export type NavItem = { href: string; label: string; icon: ReactNode; badge?: ReactNode };
export type NavGroup = { label?: string; items: NavItem[] };

type ConsoleShellProps = {
  brand: ReactNode;
  nav: NavGroup[];
  currentPath: string;
  title: string;              // shown in the sticky bar
  barActions?: ReactNode;     // max 2 icon buttons
  footer?: ReactNode;         // user/account row
  children: ReactNode;
};

export function ConsoleShell({ brand, nav, currentPath, title, barActions, footer, children }: ConsoleShellProps) {
  const [open, setOpen] = useState(false);

  useEffect(() => { setOpen(false); }, [currentPath]);
  useEffect(() => {
    if (!open) return;
    const onKey = (e: KeyboardEvent) => e.key === 'Escape' && setOpen(false);
    window.addEventListener('keydown', onKey);
    return () => window.removeEventListener('keydown', onKey);
  }, [open]);

  return (
    <div className="sol-console">
      <aside className="sol-sidebar" data-open={open} aria-label="주요 메뉴">
        <div className="sol-sidebar__brand">{brand}</div>
        <nav className="sol-sidebar__nav">
          {nav.map((g, i) => (
            <div key={i}>
              {g.label && <div className="sol-sidebar__group">{g.label}</div>}
              {g.items.map(it => (
                <a key={it.href} href={it.href} className="sol-navitem" aria-current={it.href === currentPath ? 'page' : undefined}>
                  {it.icon}<span>{it.label}</span>{it.badge}
                </a>
              ))}
            </div>
          ))}
        </nav>
        {footer && <div className="sol-sidebar__foot">{footer}</div>}
      </aside>
      {open && <div className="sol-scrim" onClick={() => setOpen(false)} />}

      <div className="sol-console__main">
        <header className="sol-console__bar">
          <button className="sol-iconbtn sol-console__menu" aria-label="메뉴 열기" aria-expanded={open} onClick={() => setOpen(true)}><Menu /></button>
          <div className="sol-console__title">{title}</div>
          {barActions}
        </header>
        <main className="sol-console__content">{children}</main>
      </div>
    </div>
  );
}
```

With a router (React Router / Next.js), swap `<a>` for the router's `Link` and pass the current pathname.

### PageHead (console/site page header)

```tsx
export function PageHead({ title, description, actions }: { title: ReactNode; description?: ReactNode; actions?: ReactNode }) {
  return (
    <div className="sol-pagehead">
      <div>
        <h1 className="t-title1" style={{ margin: 0 }}>{title}</h1>
        {description && <p className="t-body2" style={{ margin: '6px 0 0' }}>{description}</p>}
      </div>
      {actions && <div className="sol-pagehead__actions">{actions}</div>}
    </div>
  );
}
```

### Panel

```tsx
export function Panel({ title, aux, children, padded = false, className }: {
  title?: ReactNode; aux?: ReactNode; children: ReactNode; padded?: boolean; className?: string;
}) {
  return (
    <section className={['sol-panel', className].filter(Boolean).join(' ')}>
      {(title || aux) && (
        <div className="sol-panel__head">
          {title && <h2 className="t-heading" style={{ margin: 0 }}>{title}</h2>}
          {aux}
        </div>
      )}
      {padded ? <div className="sol-panel__body">{children}</div> : children}
    </section>
  );
}
```

`padded` for free content (charts, forms). Leave it off when the children are ListRows or a table (they carry their own padding).

### Stat

```tsx
export function Stat({ label, value, delta, trend }: { label: string; value: ReactNode; delta?: string; trend?: 'up' | 'down' }) {
  return (
    <div className="sol-panel sol-stat">
      <div className="sol-stat__label">{label}</div>
      <div className="sol-stat__value">{value}</div>
      {delta && <div className={`sol-stat__delta${trend ? ` sol-stat__delta--${trend}` : ''}`}>{delta}</div>}
    </div>
  );
}
```

"up" is green only when up is good. If a rising number is bad (errors, cost), pass `trend="down"` styling deliberately or omit trend.

### DataTable

```tsx
export type Column<T> = { key: string; header: string; render: (row: T) => ReactNode; numeric?: boolean };

export function DataTable<T>({ columns, rows, rowKey, empty }: {
  columns: Column<T>[]; rows: T[]; rowKey: (row: T) => string | number; empty?: ReactNode;
}) {
  if (!rows.length) return <div className="sol-empty">{empty ?? '아직 데이터가 없어요'}</div>;
  return (
    <div className="sol-table-wrap">
      <table className="sol-table">
        <thead><tr>{columns.map(c => <th key={c.key} className={c.numeric ? 'num' : undefined} scope="col">{c.header}</th>)}</tr></thead>
        <tbody>
          {rows.map(r => (
            <tr key={rowKey(r)}>{columns.map(c => <td key={c.key} className={c.numeric ? 'num' : undefined}>{c.render(r)}</td>)}</tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

Below md, if a table has more than 3 columns, render the same data as ListRows instead (title = main column, description = secondary, trailing = number/badge) rather than forcing horizontal scroll.

### SiteShell (top nav)

```tsx
export function SiteShell({ brand, links, currentPath, cta, children, footer }: {
  brand: ReactNode; links: { href: string; label: string }[]; currentPath: string;
  cta?: ReactNode; children: ReactNode; footer?: ReactNode;
}) {
  const [open, setOpen] = useState(false);
  return (
    <div className="sol-site">
      <header className="sol-topnav">
        <div className="sol-container sol-topnav__inner">
          <a className="sol-topnav__brand" href="/">{brand}</a>
          <nav className="sol-topnav__links" aria-label="주요 메뉴">
            {links.map(l => <a key={l.href} href={l.href} aria-current={l.href === currentPath ? 'page' : undefined}>{l.label}</a>)}
          </nav>
          <div className="sol-topnav__actions">
            {cta}
            <button className="sol-iconbtn sol-topnav__menu" aria-label="메뉴 열기" aria-expanded={open} onClick={() => setOpen(true)}><Menu /></button>
          </div>
        </div>
      </header>
      <BottomSheet open={open} onClose={() => setOpen(false)}>
        {links.map(l => <a key={l.href} href={l.href} className="sol-row sol-row--sm"><span className="sol-row__body"><span className="sol-row__title">{l.label}</span></span></a>)}
      </BottomSheet>
      {children}
      {footer && <footer className="sol-footer"><div className="sol-container">{footer}</div></footer>}
    </div>
  );
}
```

### Form page on desktop

```tsx
<div className="sol-container sol-container--narrow">
  <PageHead title="프로필 수정하기" />
  <Panel padded>
    <div style={{ display: 'grid', gap: 20 }}>
      <TextField label="이름" />
      <TextField label="이메일" type="email" />
    </div>
    <div className="sol-actions">
      <Button variant="secondary">닫기</Button>
      <Button>저장하기</Button>
    </div>
  </Panel>
</div>
```

Inside a Panel, `TextField` uses `.sol-field` which already has gutter padding — set `style={{ padding: 0 }}` on the field or override `.sol-panel .sol-field { padding: 0 }` once in the project.
