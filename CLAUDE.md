# Agent Guide — next-sarangoo-art

This document is the operating manual for an agentic coding assistant working in this repository. The stack is Next.js (App Router) + TypeScript + Tailwind CSS v4 + shadcn/ui patterns.

## Mission and guardrails

- Prefer small, incremental PR-sized changes. Keep edits minimal and scoped.
- Read before you write: scan related files fully, then change only what’s needed.
- Follow the conventions below (TypeScript strict, App Router, RSC by default, Tailwind + `cn`, shadcn-style components with `cva`).
- After changes, run lint and typecheck. If you add runtime-affecting code, also run a dev build locally.
- Do not introduce heavyweight dependencies without clear benefit.

## Tech summary

- Framework: Next.js 15 (App Router, React 19)
- Language: TypeScript (strict)
- Styling: Tailwind CSS v4 (+ `tw-animate-css`), utility merger via `cn()`
- UI: shadcn/ui patterns (Lucide icons, `class-variance-authority`)
- Linting: ESLint (flat config) extending `next/core-web-vitals` + `next/typescript`

## Core files and utilities

- `app/layout.tsx`: Root layout, global fonts (`Geist`, `Geist_Mono`) and CSS; set HTML skeleton.
- `app/page.tsx`: Home page example. Use as starting point for routes.
- `app/globals.css`: Tailwind v4 entrypoint and theme tokens; includes `tw-animate-css` and CSS variables.
- `components/ui/button.tsx`: shadcn-style `Button` built with `cva` variants and `Slot`; reference for new UI components.
- `lib/utils.ts`: Exports `cn(...inputs)` combining `clsx` + `tailwind-merge`; use for className composition everywhere.
- `components.json`: shadcn/ui config (style: "new-york", RSC: true, aliases: `@/components`, `@/lib`, `@/components/ui`).
- `eslint.config.mjs`: Flat config using `next/core-web-vitals` and `next/typescript`; includes ignores for build artifacts.
- `tsconfig.json`: Strict TS, bundler module resolution, path alias `@/*`.
- `next.config.ts`: Placeholder for Next config.
- `postcss.config.mjs`: Tailwind v4 via `@tailwindcss/postcss` plugin.

## Common bash commands (macOS, zsh)

Optional, run one per line:

```bash
# Install deps
npm install

# Start dev server (Turbopack)
npm run dev

# Type-check only
npx tsc -p tsconfig.json --noEmit

# Lint all files
npm run lint

# Production build (Turbopack) and start
npm run build
npm run start

# Clean caches (safe local cleanup)
rm -rf .next
rm -rf node_modules
rm -rf out

# Next.js environment info (useful for debugging)
npx next info

# Add a shadcn/ui component (example: button)
npx shadcn@latest add button
```

Notes:
- Tailwind v4 is configured via PostCSS; classes live in `app/globals.css` and component files.
- Aliases: import local modules as `@/lib/*`, `@/components/*`, `@/components/ui/*`.

## Code style and conventions

- TypeScript
  - Strict enabled; add precise types and narrow where helpful.
  - Prefer explicit return types for exported functions/components.
- Next.js App Router
  - Server Components by default. Add `"use client"` only when you need browser-only APIs, state, refs, or event handlers.
  - Route and feature colocation under `app/`.
- Styling
  - Prefer Tailwind utility classes. Compose with `cn()` from `lib/utils` to merge conditional classes safely.
  - Avoid inline styles unless necessary; prefer semantic class names and Tailwind tokens defined in `globals.css`.
- Components
  - Follow shadcn/ui patterns: use `cva` for variants, `Slot` for `asChild` patterns when needed.
  - Co-locate variants and export both the component and its `...Variants` when useful (like `buttonVariants`).
  - Accessibility: ensure interactive elements have proper roles/labels; `alt` text for images.
- Imports
  - Use path alias `@/*` for internal modules. Group imports: external libs, then internal aliases, then relative.
- Files
  - Use named exports where reasonable. Default exports are acceptable for Next.js pages/layouts.

## UI patterns (shadcn/ui)

- Use `class-variance-authority` (`cva`) to define component variants (size, intent, etc.).
- Merge consumer classes via `className` prop combined with `cn(buttonVariants({ ... }))` pattern.
- Keep components headless and accessible; styling via Tailwind utilities and CSS variables defined in `globals.css`.

## Testing instructions

There is no testing setup yet. Choose one of the following paths.

### Option A — Vitest + React Testing Library (recommended for unit tests)

```bash
# Add dev dependencies
npm i -D vitest @testing-library/react @testing-library/user-event @testing-library/jest-dom jsdom @types/jsdom

# (optional) Type checks in tests
npm i -D @types/testing-library__jest-dom
```

Create `vitest.config.ts` with JSDOM, alias `@`, and TS paths resolution. Add a `setupTests.ts` that imports `@testing-library/jest-dom`.

Run tests:

```bash
npx vitest
```

### Option B — Jest (via next/jest)

```bash
npm i -D jest @types/jest next/jest @testing-library/react @testing-library/user-event @testing-library/jest-dom jsdom
```

Create `jest.config.ts` with `next/jest`, set test environment to `jsdom`, and a `setupTests.ts` importing `@testing-library/jest-dom`.

Run tests:

```bash
npx jest
```

### E2E — Playwright

```bash
npm i -D @playwright/test
npx playwright install
```

Add basic flows (homepage renders, navigation works). Start the dev server in one terminal and run Playwright in another.

## Assistant workflow checklist

1) Read context
   - Scan target file(s) and related dependencies.
   - Confirm conventions (RSC vs client, `cn`, cva usage, aliases).

2) Implement
   - Make the smallest viable change. Keep diffs tight and scoped.
   - If creating components, follow the `components/ui/button.tsx` pattern.

3) Validate locally
   - Lint: `npm run lint`
   - Types: `npx tsc -p tsconfig.json --noEmit`
   - If applicable: `npm run dev` and smoke-test the affected page.

4) Document
   - Update or add minimal usage notes in the relevant file(s) or README.

5) Submit
   - Ensure no stray console logs, no unused deps, and minimal bundle impact.

## Quick references

- Aliases: `@/components`, `@/components/ui`, `@/lib`, `@/*`
- Utility: `cn(...classes)` from `lib/utils`
- Example variants: `buttonVariants` in `components/ui/button.tsx`

That’s it—keep it clean, typed, and consistent.
