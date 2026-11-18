# Components (React + TypeScript)

All components are thin wrappers over the `.sol-*` classes in `assets/components.css`.
Copy `assets/tokens.css` and `assets/components.css` into the project (e.g. `src/styles/`) and import both once at the app root, tokens first.

```tsx
// src/main.tsx
import './styles/tokens.css';
import './styles/components.css';
```

Load Pretendard once in `index.html`:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/variable/pretendardvariable-dynamic-subset.min.css">
```

Icons: use `lucide-react` (stroke 2, size 24 in AppBar, 22 in row leading, 20 in toast). One icon per spot — never pair two icons side by side.

Put components in `src/components/ui/`. Export everything from `src/components/ui/index.ts`.

---

## Screen layout (always this order)

```tsx
<div className="sol-app">
  <AppBar title="…" onBack={…} />
  <main className="sol-page">
    <PageHeader eyebrow="…" title="…" description="…" />
    {/* optional: Progress / Tabs / Segmented */}
    <SectionHeader title="…" aux="…" />
    <ListRow … />
    <ListRow … />
    <SectionGap />
    <SectionHeader title="…" />
    <ListRow … />
  </main>
  <BottomCTA><Button>…</Button></BottomCTA>
</div>
```

---

## AppBar

```tsx
import { ChevronLeft } from 'lucide-react';
import type { ReactNode } from 'react';

type AppBarProps = {
  title?: string;
  onBack?: () => void;          // omit on the root screen
  trailing?: ReactNode;          // max ONE icon button
  centerTitle?: boolean;
};

export function AppBar({ title, onBack, trailing, centerTitle }: AppBarProps) {
  return (
    <header className="sol-appbar">
      {onBack ? (
        <button className="sol-iconbtn" onClick={onBack} aria-label="뒤로 가기"><ChevronLeft /></button>
      ) : centerTitle ? <span className="sol-iconbtn" aria-hidden /> : null}
      <h1 className={`sol-appbar__title${centerTitle ? ' sol-appbar__title--center' : ''}`}>{title}</h1>
      {trailing ?? (centerTitle ? <span className="sol-iconbtn" aria-hidden /> : null)}
    </header>
  );
}

export function IconButton({ label, children, onClick }: { label: string; children: ReactNode; onClick?: () => void }) {
  return <button className="sol-iconbtn" aria-label={label} onClick={onClick}>{children}</button>;
}
```

## PageHeader

The single big message of the screen. Wrap the key number/word in `<em className="t-accent">` (style `em` with `font-style: normal`).

```tsx
type PageHeaderProps = { eyebrow?: string; title: ReactNode; description?: ReactNode };

export function PageHeader({ eyebrow, title, description }: PageHeaderProps) {
  return (
    <div className="sol-pageheader">
      {eyebrow && <div className="sol-pageheader__eyebrow">{eyebrow}</div>}
      <h2 className="t-title1" style={{ margin: 0 }}>{title}</h2>
      {description && <p className="sol-pageheader__desc" style={{ margin: '8px 0 0' }}>{description}</p>}
    </div>
  );
}
```

## SectionHeader / SectionGap / Divider

```tsx
export function SectionHeader({ title, aux }: { title: string; aux?: ReactNode }) {
  return (
    <div className="sol-sectionheader">
      <h3 className="t-heading" style={{ margin: 0 }}>{title}</h3>
      {aux && <span className="sol-sectionheader__aux">{aux}</span>}
    </div>
  );
}
export const SectionGap = () => <div className="sol-section-gap" aria-hidden />;
export const Divider = () => <div className="sol-divider" role="separator" />;
```

## ListRow

The workhorse. Replaces most cards. Three zones: `leading` / body (`title` + `description`) / `trailing`.

```tsx
import { ChevronRight } from 'lucide-react';

type ListRowProps = {
  leading?: ReactNode;
  leadingTone?: 'primary' | 'neutral';
  title: ReactNode;
  description?: ReactNode;
  trailing?: ReactNode;
  chevron?: boolean;               // show › for navigation rows
  size?: 'sm' | 'md' | 'lg';
  onClick?: () => void;
  disabled?: boolean;
  selectable?: boolean;            // renders a radio and role="radio"
  selected?: boolean;
};

export function ListRow({
  leading, leadingTone = 'primary', title, description, trailing,
  chevron, size = 'md', onClick, disabled, selectable, selected,
}: ListRowProps) {
  const Tag = onClick ? 'button' : 'div';
  const cls = ['sol-row', size !== 'md' && `sol-row--${size}`].filter(Boolean).join(' ');
  return (
    <Tag
      className={cls}
      onClick={onClick}
      aria-disabled={disabled || undefined}
      {...(selectable ? { role: 'radio', 'aria-checked': !!selected } : {})}
    >
      {leading && (
        <span className={`sol-row__leading${leadingTone === 'neutral' ? ' sol-row__leading--neutral' : ''}`}>{leading}</span>
      )}
      <span className="sol-row__body">
        <div className="sol-row__title">{title}</div>
        {description && <div className="sol-row__desc">{description}</div>}
      </span>
      {(trailing || chevron || selectable) && (
        <span className="sol-row__trailing">
          {trailing}
          {chevron && <ChevronRight className="sol-row__chevron" />}
          {selectable && <span className="sol-radio" />}
        </span>
      )}
    </Tag>
  );
}
```

Wrap a group of selectable rows in `<div role="radiogroup" aria-label="…">`.

## Button / BottomCTA

```tsx
import { forwardRef, type ButtonHTMLAttributes } from 'react';

type ButtonProps = ButtonHTMLAttributes<HTMLButtonElement> & {
  variant?: 'primary' | 'secondary' | 'weak' | 'danger' | 'text';
  size?: 'lg' | 'md' | 'sm';
  block?: boolean;
  loading?: boolean;
};

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(function Button(
  { variant = 'primary', size = 'lg', block, loading, className, children, ...rest }, ref,
) {
  const cls = ['sol-btn', `sol-btn--${variant}`, size !== 'lg' && `sol-btn--${size}`, block && 'sol-btn--block', className]
    .filter(Boolean).join(' ');
  return <button ref={ref} className={cls} aria-busy={loading || undefined} {...rest}>{children}</button>;
});

/** Sticky bottom action area. One primary button, optionally one secondary on the left. */
export function BottomCTA({ children }: { children: ReactNode }) {
  return <div className="sol-bottomcta">{children}</div>;
}
```

Usage:

```tsx
<BottomCTA>
  <Button variant="secondary">닫기</Button>
  <Button onClick={save} loading={saving} disabled={!valid}>저장하기</Button>
</BottomCTA>
```

## Badge

```tsx
type BadgeTone = 'primary' | 'neutral' | 'danger' | 'warning' | 'info' | 'solid';
export const Badge = ({ tone = 'primary', children }: { tone?: BadgeTone; children: ReactNode }) =>
  <span className={`sol-badge sol-badge--${tone}`}>{children}</span>;
```

## Tabs / Segmented

```tsx
type TabItem<T extends string> = { value: T; label: string };

export function Tabs<T extends string>({ items, value, onChange }: { items: TabItem<T>[]; value: T; onChange: (v: T) => void }) {
  return (
    <div className="sol-tabs" role="tablist">
      {items.map(it => (
        <button key={it.value} role="tab" className="sol-tab" aria-selected={it.value === value} onClick={() => onChange(it.value)}>
          {it.label}
        </button>
      ))}
    </div>
  );
}

export function Segmented<T extends string>({ items, value, onChange }: { items: TabItem<T>[]; value: T; onChange: (v: T) => void }) {
  return (
    <div className="sol-segmented">
      {items.map(it => (
        <button key={it.value} aria-pressed={it.value === value} onClick={() => onChange(it.value)}>{it.label}</button>
      ))}
    </div>
  );
}
```

Tabs = switch between content lists. Segmented = switch the view/filter of the same data (2–4 options).

## Progress

```tsx
export const Progress = ({ value, label }: { value: number; label: string }) => (
  <div className="sol-progress" role="progressbar" aria-label={label} aria-valuemin={0} aria-valuemax={100} aria-valuenow={Math.round(value)}>
    <i style={{ width: `${Math.min(100, Math.max(0, value))}%` }} />
  </div>
);
```

## TextField

```tsx
import { useId, type InputHTMLAttributes } from 'react';

type TextFieldProps = InputHTMLAttributes<HTMLInputElement> & { label: string; help?: string; error?: string };

export function TextField({ label, help, error, ...rest }: TextFieldProps) {
  const id = useId();
  return (
    <div className="sol-field">
      <label className="sol-field__label" htmlFor={id}>{label}</label>
      <input id={id} className="sol-input" aria-invalid={!!error || undefined} aria-describedby={`${id}-help`} {...rest} />
      {(error || help) && (
        <div id={`${id}-help`} className={`sol-field__help${error ? ' sol-field__help--error' : ''}`}>{error ?? help}</div>
      )}
    </div>
  );
}
```

## Dialog + Toast (provider + hooks)

Every confirmation goes through `useDialog().confirm`. Feedback after an action goes through `useToast()`.

```tsx
// src/components/ui/feedback.tsx
import { createContext, useCallback, useContext, useEffect, useRef, useState, type ReactNode } from 'react';
import { CheckCircle2, AlertCircle } from 'lucide-react';
import { Button } from './Button';

type ConfirmOptions = { title: string; description?: string; confirmText: string; danger?: boolean };
type ToastOptions = { tone?: 'success' | 'error' };

const Ctx = createContext<{
  confirm: (o: ConfirmOptions) => Promise<boolean>;
  toast: (msg: string, o?: ToastOptions) => void;
} | null>(null);

export function FeedbackProvider({ children }: { children: ReactNode }) {
  const [dialog, setDialog] = useState<(ConfirmOptions & { resolve: (v: boolean) => void }) | null>(null);
  const [toast, setToast] = useState<{ msg: string; tone: 'success' | 'error'; key: number } | null>(null);
  const closeRef = useRef<HTMLButtonElement>(null);

  const confirm = useCallback((o: ConfirmOptions) => new Promise<boolean>(resolve => setDialog({ ...o, resolve })), []);
  const showToast = useCallback((msg: string, o?: ToastOptions) => setToast({ msg, tone: o?.tone ?? 'success', key: Date.now() }), []);

  const close = (v: boolean) => { dialog?.resolve(v); setDialog(null); };

  useEffect(() => {
    if (!dialog) return;
    closeRef.current?.focus();
    const onKey = (e: KeyboardEvent) => e.key === 'Escape' && close(false);
    window.addEventListener('keydown', onKey);
    return () => window.removeEventListener('keydown', onKey);
  }, [dialog]);

  useEffect(() => {
    if (!toast) return;
    const t = setTimeout(() => setToast(null), 2200);
    return () => clearTimeout(t);
  }, [toast]);

  return (
    <Ctx.Provider value={{ confirm, toast: showToast }}>
      {children}
      {dialog && (
        <div className="sol-dim" onClick={e => e.target === e.currentTarget && close(false)}>
          <div className="sol-dialog" role="alertdialog" aria-modal="true" aria-labelledby="sol-dlg-title">
            <h2 id="sol-dlg-title" className="t-title3 sol-dialog__title">{dialog.title}</h2>
            {dialog.description && <p className="sol-dialog__desc">{dialog.description}</p>}
            <div className="sol-dialog__actions">
              <Button ref={closeRef} variant="secondary" onClick={() => close(false)}>닫기</Button>
              <Button variant={dialog.danger ? 'danger' : 'primary'} onClick={() => close(true)}>{dialog.confirmText}</Button>
            </div>
          </div>
        </div>
      )}
      {toast && (
        <div key={toast.key} className="sol-toast" role="status" aria-live="polite">
          {toast.tone === 'success'
            ? <CheckCircle2 color="var(--g-400)" />
            : <AlertCircle color="var(--red-500)" />}
          {toast.msg}
        </div>
      )}
    </Ctx.Provider>
  );
}

export function useDialog() { const c = useContext(Ctx); if (!c) throw new Error('FeedbackProvider missing'); return { confirm: c.confirm }; }
export function useToast() { const c = useContext(Ctx); if (!c) throw new Error('FeedbackProvider missing'); return c.toast; }
```

Usage:

```tsx
const { confirm } = useDialog();
const toast = useToast();

async function onDelete(log: Log) {
  const ok = await confirm({ title: `${log.time} 기록을 지울까요?`, description: '지운 기록은 되돌릴 수 없어요', confirmText: '지우기', danger: true });
  if (!ok) return;
  await remove(log.id);
  toast('기록을 지웠어요');
}
```

## BottomSheet

Only ever opened by an explicit user tap. Never on page entry, never on back.

```tsx
export function BottomSheet({ open, onClose, children }: { open: boolean; onClose: () => void; children: ReactNode }) {
  if (!open) return null;
  return (
    <div className="sol-sheet-dim" onClick={e => e.target === e.currentTarget && onClose()}>
      <div className="sol-sheet" role="dialog" aria-modal="true">
        <div className="sol-sheet__handle" />
        {children}
      </div>
    </div>
  );
}
```

## EmptyState / Skeleton

```tsx
export const EmptyState = ({ children }: { children: ReactNode }) => <div className="sol-empty">{children}</div>;

export const SkeletonRow = () => (
  <div className="sol-row">
    <div className="sol-skeleton" style={{ width: 40, height: 40, borderRadius: 12 }} />
    <div style={{ flex: 1 }}>
      <div className="sol-skeleton" style={{ width: '50%', height: 16 }} />
      <div className="sol-skeleton" style={{ width: '30%', height: 12, marginTop: 8 }} />
    </div>
  </div>
);
```

Show skeletons immediately when data takes > 300ms. Show a busy button (`loading`) for submit actions.
