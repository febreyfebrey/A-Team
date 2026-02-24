---
name: React Patterns
description: Provides React component composition, hooks, state management, and Tailwind CSS styling patterns
---

# React Patterns

## Compound Components

Share implicit state between related components via Context:

```tsx
"use client";
import { createContext, useContext, useState, type ReactNode } from "react";

type AccordionCtx = { openItem: string | null; toggle: (id: string) => void };
const Ctx = createContext<AccordionCtx | null>(null);
function useAccordion() {
  const ctx = useContext(Ctx);
  if (!ctx) throw new Error("Must be used within <Accordion>");
  return ctx;
}

function Accordion({ children }: { children: ReactNode }) {
  const [openItem, setOpenItem] = useState<string | null>(null);
  const toggle = (id: string) => setOpenItem((prev) => (prev === id ? null : id));
  return (
    <Ctx.Provider value={{ openItem, toggle }}>
      <div className="divide-y divide-gray-200 rounded border">{children}</div>
    </Ctx.Provider>
  );
}

function AccordionItem({ id, title, children }: { id: string; title: string; children: ReactNode }) {
  const { openItem, toggle } = useAccordion();
  const isOpen = openItem === id;
  return (
    <div>
      <button onClick={() => toggle(id)} aria-expanded={isOpen}
        className="flex w-full items-center justify-between px-4 py-3 text-left font-medium">
        {title}
      </button>
      {isOpen && <div className="px-4 pb-3 text-gray-600">{children}</div>}
    </div>
  );
}
Accordion.Item = AccordionItem;
export { Accordion };
```

## Custom Hooks

```tsx
// useDebounce
export function useDebounce<T>(value: T, delayMs: number): T {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delayMs);
    return () => clearTimeout(timer);
  }, [value, delayMs]);
  return debounced;
}

// useLocalStorage
export function useLocalStorage<T>(key: string, initial: T): [T, (v: T | ((p: T) => T)) => void] {
  const [stored, setStored] = useState<T>(() => {
    if (typeof window === "undefined") return initial;
    const item = localStorage.getItem(key);
    return item ? (JSON.parse(item) as T) : initial;
  });
  useEffect(() => { localStorage.setItem(key, JSON.stringify(stored)); }, [key, stored]);
  return [stored, setStored];
}
```

## State Management

- **Local**: `useState` for single-component state, `useReducer` for complex transitions
- **Shared**: Context + `useReducer` for cross-component state
- **Server**: SWR or React Query for API data (never store server data in React state manually)

## Performance

Use `React.memo` only when profiling confirms unnecessary re-renders. Use `useMemo` for expensive computations and `useCallback` for stable function references passed to memoized children. Do not apply them to every value -- measure first.

## Form Handling with Zod

```tsx
"use client";
import { useState } from "react";
import { z } from "zod";

const schema = z.object({
  name: z.string().min(1, "Name is required"),
  email: z.string().email("Enter a valid email"),
});
type Form = z.infer<typeof schema>;
type Errors = Partial<Record<keyof Form, string>>;

export function ContactForm() {
  const [form, setForm] = useState<Form>({ name: "", email: "" });
  const [errors, setErrors] = useState<Errors>({});
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    const result = schema.safeParse(form);
    if (!result.success) {
      const errs: Errors = {};
      result.error.issues.forEach((i) => { errs[i.path[0] as keyof Form] = i.message; });
      setErrors(errs);
      return;
    }
    setErrors({});
  };
  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <input value={form.name} onChange={(e) => setForm((p) => ({ ...p, name: e.target.value }))}
        className="block w-full rounded border px-3 py-2" />
      {errors.name && <p className="text-sm text-red-600">{errors.name}</p>}
      <button type="submit" className="rounded bg-blue-600 px-4 py-2 text-white">Send</button>
    </form>
  );
}
```

## Tailwind Patterns

**Mobile-first responsive**: `className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3"`

**Dark mode**: `className="bg-white dark:bg-gray-900"` (configure `darkMode: "class"`)

**Component variants with cva:**

```tsx
import { cva, type VariantProps } from "class-variance-authority";
import { clsx } from "clsx";

const btn = cva("inline-flex items-center justify-center rounded font-medium", {
  variants: {
    variant: { primary: "bg-blue-600 text-white", secondary: "bg-gray-100 text-gray-900" },
    size: { sm: "px-3 py-1.5 text-sm", md: "px-4 py-2", lg: "px-6 py-3 text-lg" },
  },
  defaultVariants: { variant: "primary", size: "md" },
});
type ButtonProps = React.ButtonHTMLAttributes<HTMLButtonElement> & VariantProps<typeof btn>;
export function Button({ variant, size, className, ...props }: ButtonProps) {
  return <button className={clsx(btn({ variant, size }), className)} {...props} />;
}
```

## Error Boundaries

```tsx
"use client";
import { Component, type ReactNode } from "react";
export class ErrorBoundary extends Component<{ children: ReactNode; fallback: ReactNode }, { hasError: boolean }> {
  state = { hasError: false };
  static getDerivedStateFromError() { return { hasError: true }; }
  render() { return this.state.hasError ? this.props.fallback : this.props.children; }
}
```

## Accessibility

Use semantic HTML (`button`, `nav`, `main`). Add `aria-label` to icon-only buttons. Ensure keyboard navigation (`Tab`, `Enter`, `Escape`). Never remove focus outlines without an alternative.

## Example

**Input**: Create a reusable Accordion with Tailwind styling and TypeScript props.

**Output -- Usage:**

```tsx
<Accordion>
  <Accordion.Item id="faq-1" title="What is your return policy?">
    Return items within 30 days for a full refund.
  </Accordion.Item>
  <Accordion.Item id="faq-2" title="How long does shipping take?">
    Standard shipping takes 5-7 business days.
  </Accordion.Item>
</Accordion>
```

**Output -- Rendered HTML:**

```html
<div class="divide-y divide-gray-200 rounded border">
  <div>
    <button class="flex w-full items-center justify-between px-4 py-3 text-left font-medium"
      aria-expanded="true">What is your return policy?</button>
    <div class="px-4 pb-3 text-gray-600">Return items within 30 days for a full refund.</div>
  </div>
  <div>
    <button class="flex w-full items-center justify-between px-4 py-3 text-left font-medium"
      aria-expanded="false">How long does shipping take?</button>
  </div>
</div>
```
