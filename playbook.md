# Agent Playbook: Artist Portfolio Website (Next.js + shadcn)

A step‑by‑step blueprint designed for an **agentic coding assistant** to build a production‑ready artist portfolio with excellent UX, accessibility, and SEO. Written as executable tasks with inputs, commands, and acceptance criteria.

---

## 0) Goals & Non‑Goals

**Goals**

* Showcase original artworks in a fast, elegant gallery with detail pages.
* Bilingual content (EN/JA) with SEO‑friendly routing.
* Simple content editing (MDX or CMS‑ready), image optimization, and contact form.
* Clean design system using Tailwind + shadcn/ui; dark/light themes.

**Non‑Goals**

* Full e‑commerce (link out to external shop if needed).
* Complex CMS migration (scaffold MDX; keep CMS adapters optional).

**Success Metrics**

* Lighthouse ≥ 95 (Perf/SEO/Best Practices/Accessibility) on key pages.
* CLS < 0.1; Interaction to Next Paint < 200ms on mid devices.
*

---

## 1) Information Architecture

**Sitemap**

* `/` Home (hero, featured works, CTA to Gallery / About / Exhibitions)
* `/gallery` (filters: medium, size, color, year; masonry grid; infinite load)
* `/gallery/[slug]` Artwork Detail (zoom, metadata, share OG)
* `/exhibitions` Past & Upcoming (timeline/cards)
* `/about` Bio, artist statement, CV, press highlights
* `/contact` Form + social links
* `/legal` (Privacy/Terms/Imprint; optional)

**i18n routes**

* `/(en|ja)/...` locale segments; default locale configurable.

**Content Sources**

* MDX files versioned in repo (default), pluggable to CMS later (Sanity/Contentful/Shopify for prints) via adapter.

---

## 2) Visual Design System

**Brand Personality**: minimal, gallery‑like, airy whitespace, focus on art.

**Color Tokens** (CSS variables; adapt later to brand):

* `--bg: hsl(0 0% 100%); --fg: hsl(222 47% 11%);` (dark mode inverted)
* `--muted: hsl(210 10% 96%); --muted-fg: hsl(215 16% 46%);`
* `--accent: hsl(260 84% 54%); --accent-fg: white;`
* `--border: hsl(214 32% 91%);`

**Type Scale** (Tailwind utilities):

* Display: `text-5xl/none md:text-6xl`
* H1: `text-4xl md:text-5xl`
* H2: `text-2xl md:text-3xl`
* H3: `text-xl md:text-2xl`
* Body: `text-base leading-7`; Small: `text-sm`

**Spacing & Layout**

* Container: `max-w-7xl mx-auto px-4 md:px-6`
* Section rhythm: `py-12 md:py-20`; Grid gap `gap-6`

**Components (shadcn/ui)**

* `button, card, dialog, dropdown-menu, input, textarea, label, select, tabs, tooltip, sheet, navigation-menu, breadcrumb, badge, separator, skeleton, toast`

**Imagery**

* Use `next/image` with `sizes` and `placeholder="blur"`; keep frames subtle. Lightbox on artwork pages.

---

## 3) Project Setup

**Prereqs**: Node 20+, pnpm/yarn, Git, VSCode with ESLint/Prettier.

**Task 3.1 — Initialize Project**

* **Commands**

  ```bash
  npx create-next-app@latest artist-portfolio \
    --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" --no-experimental
  cd artist-portfolio
  pnpm add -D prettier prettier-plugin-tailwindcss @types/node @types/react
  ```
* **Accept**: App Router scaffold builds & runs; Tailwind present.

**Task 3.2 — Install shadcn/ui**

* **Commands**

  ```bash
  pnpm dlx shadcn@latest init -d
  pnpm dlx shadcn@latest add button card input textarea label dialog dropdown-menu \
    select tabs tooltip sheet navigation-menu breadcrumb badge separator skeleton toast
  ```
* **Accept**: `components/ui/*` generated; `tailwind.config.js` updated; `lib/utils.ts` present.

**Task 3.3 — Base Theming**

* **Actions**

  * Add CSS variables and dark mode class to `globals.css`.
  * Implement ThemeToggle using shadcn pattern (use `next-themes`).
* **Commands**

  ```bash
  pnpm add next-themes
  ```

**Task 3.4 — Linting & Formatting**

* Configure ESLint/Prettier; add scripts: `lint`, `format`, `typecheck`.

---

## 4) Directory Layout

```
src/
  app/
    (marketing)/
      layout.tsx
      page.tsx                # Home
    (site)/
      gallery/
        page.tsx
        [slug]/page.tsx
      exhibitions/page.tsx
      about/page.tsx
      contact/page.tsx
      legal/page.tsx
    api/
      contact/route.ts        # email handler (Resend or Formspree proxy)
    sitemap.ts
    robots.ts
  components/
    site-header.tsx
    site-footer.tsx
    locale-switcher.tsx
    gallery/
      artwork-card.tsx
      masonry-grid.tsx
      filters.tsx
      lightbox.tsx
  content/
    artworks/*.mdx
    exhibitions/*.mdx
    pages/*.mdx
  lib/
    i18n.ts
    mdx.ts
    contentlayer.ts (optional)
    seo.ts
  styles/globals.css
```

---

## 5) Content Model (MDX‑first)

**Artwork frontmatter (example)**

```mdx
---
slug: "pink-composition-01"
title: "Pink Composition"
year: 2024
medium: "Oil on canvas"
dimensions: "100×150 cm"
colors: ["pink","magenta","cream"]
collection: "Garden of Colors"
price: null
images:
  - /images/artworks/pink-composition-01/hero.jpg
  - /images/artworks/pink-composition-01/detail-1.jpg
featured: true
lang: "en"
---

Artist’s note in markdown...
```

**Exhibition frontmatter**

```mdx
---
slug: "national-art-center-2025"
name: "National Art Center Group Show"
location: "Tokyo, Japan"
startDate: "2025-03-01"
endDate: "2025-03-30"
press: [
  { label: "Press Release", url: "https://..." }
]
lang: "en"
---

Short description...
```

**Pages (About, Legal) frontmatter**

```mdx
---
slug: "about"
title: "About the Artist"
lang: "en"
---
```

**Contentlayer (optional)** — generate types for MDX; otherwise write a small loader that reads `/content` at build time.

---

## 6) i18n (EN/JA)

**Choice**: `next-intl` or minimal custom i18n.

**Task 6.1 — Add next-intl**

```bash
pnpm add next-intl
```

* Create `messages/en.json`, `messages/ja.json`.
* Implement locale segment routes `src/app/[locale]/...` with middleware redirect.
* **Accept**: switching locales updates strings, routes are `/en/...` and `/ja/...`.

**Tip**: For artwork MDX, duplicate localized files or add `lang` in frontmatter with parallel MDX per locale.

---

## 7) Core UI Shell

**Task 7.1 — Header & Nav**

* Components: `site-header.tsx` (logo/wordmark), nav links, locale switcher, theme toggle.
* Use `navigation-menu` for desktop; `sheet` for mobile menu.
* Acceptance: Keyboard navigable, visible focus, 44px touch targets.

**Task 7.2 — Footer**

* Minimal links, social icons, copyright, email.

---

## 8) Pages & Features

### 8.1 Home

* Hero with headline, sub, CTA to Gallery.
* Featured works (3–6) in responsive grid.
* Exhibition teaser + About teaser.
* Skeletons for loading; lazy images with `sizes`.

### 8.2 Gallery

* Filters: `medium`, `year`, `color`, `collection` (client state in URL query).
* Masonry layout using CSS columns for simple, or grid with `row-span` based on image aspect ratio.
* Infinite scroll (IntersectionObserver) or `/gallery?page=` pagination.
* Artwork Card: cover image, title, year, medium badges.

### 8.3 Artwork Detail `/gallery/[slug]`

* Large image with lightbox; thumbnails below.
* Metadata table (year, medium, size, collection, colors).
* Share (copy link); optional “Enquire” CTA → `/contact?art=slug`.
* Structured data (JSON‑LD `VisualArtwork`).

### 8.4 Exhibitions

* Timeline cards grouped by year; Upcoming pinned.

### 8.5 About

* Bio, artist statement, CV (education, shows, press). Downloadable PDF resume.

### 8.6 Contact

* Form with name, email, message, consent checkbox.
* Option A: serverless email via Resend; Option B: Formspree POST.
* Success state + translation.

### 8.7 Legal

* Privacy, Terms. Generate simple pages from MDX.

---

## 9) SEO, Metadata, and Social

* Use Next Metadata API in layouts/pages (title, description, alternates, locale).
* `sitemap.ts`, `robots.ts` with locale variants.
* Open Graph images via `@vercel/og` for artwork detail (title + thumbnail).
* JSON‑LD: `VisualArtwork`, `WebSite`, `BreadcrumbList`.
* Favicons & `manifest.webmanifest`.

---

## 10) Images & Performance

* Store sources under `/public/images/...` or remote loader (e.g., Cloudinary) with `next/image`.
* Generate blur placeholders (precompute or via `plaiceholder`).
* Provide `sizes` per layout, e.g. hero `"(max-width: 768px) 100vw, 1200px"`.
* Preload critical hero image with priority; defer others.

---

## 11) Analytics & Monitoring

* GA4 via `@vercel/analytics` or `gtag` snippet; consent banner optional using `dialog`.
* Simple event: artwork detail view, filter usage, contact submission.
* Error boundary & 404/500 pages.

---

## 12) Deployment

* Vercel: connect repo, add env vars.
* Preview deployments for PRs; protect `main` with CI checks.
* Set `NEXT_IMAGES_ALLOW_FALLBACK` if using remote images (as needed).

---

## 13) Optional Integrations

* **Shop link** to external store (e.g., Shopify at `kumogallery.com` or `shop.kumogallery.com`).
* **CMS Adapters**: implement `ContentSource` interface with methods `getArtworks()`, `getArtwork(slug)`, `getExhibitions()`, etc.

---

## 14) Agent Taskboard (Executable Steps)

> Each task lists **Inputs**, **Commands/Edits**, and **Acceptance Criteria** for automation.

### T1 — Repo Bootstrap

* **Inputs**: repo name `artist-portfolio`
* **Commands**: see Task 3.1
* **Accept**: `pnpm dev` serves at `localhost:3000`.

### T2 — Add shadcn & Theme

* **Commands**: see Tasks 3.2–3.3
* **Edits**: inject CSS variables, create `ThemeToggle.tsx`.
* **Accept**: dark/light toggle persists via localStorage.

### T3 — Layout & Shell

* **Edits**: create `site-header.tsx`, `site-footer.tsx`, base layout with container.
* **Accept**: keyboard nav works; responsive menu via `sheet`.

### T4 — Content System (MDX)

* **Commands**: `pnpm add next-mdx-remote gray-matter remark-gfm` (or Contentlayer)
* **Edits**: `lib/mdx.ts` loader that parses frontmatter and caches at build.
* **Accept**: two sample artworks & exhibitions render.

### T5 — i18n Routing

* **Commands**: `pnpm add next-intl`
* **Edits**: locale segment, middleware, `messages/{en,ja}.json`.
* **Accept**: `/ja` shows Japanese strings; fallback works.

### T6 — Gallery

* **Edits**: `masonry-grid.tsx`, `artwork-card.tsx`, filter state in URL.
* **Accept**: filter chips update counts; deep links shareable.

### T7 — Artwork Detail

* **Edits**: lightbox, metadata table, JSON‑LD.
* **Accept**: OG image unique per artwork.

### T8 — Contact Form

* **Commands**: `pnpm add resend` (if using Resend)
* **Edits**: API route, server action, validation; success toast.
* **Accept**: email received in test inbox.

### T9 — SEO & Sitemaps

* **Edits**: metadata functions, `sitemap.ts`, `robots.ts` with locales.
* **Accept**: Search Console fetch succeeds; no index errors.

### T10 — QA & Deploy

* **Commands**: `pnpm build && pnpm start`; Lighthouse CI.
* **Accept**: all metrics pass; deploy to Vercel.

---

## 15) Content & Copy Guidance

**Tone**: clear, warm, confident; minimal jargon; present tense.

**Home Hero (template)**

* Eyebrow: *Original Artworks*
* Title: *Paintings that breathe color*
* Subtitle: *Explore collections, exhibitions, and new works.*
* CTA: *View Gallery*

**About (outline)**

* Short bio (80–120 words)
* Artist statement (180–300 words)
* CV bullets: Education, Exhibitions, Press, Collections

**Exhibition Entry (template)**

* Name — Location, Dates
* 2–3 sentence description + one photo

**Contact (copy)**

* “For inquiries about exhibitions, commissions, or press, please use the form or email …”

**JA Translations** (examples)

* Eyebrow: *オリジナル作品*
* Title: *色彩が息づく絵画*
* CTA: *ギャラリーを見る*

---

## 16) Example Snippets

**Theme provider in `layout.tsx`**

```tsx
import { ThemeProvider } from "next-themes";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" suppressHydrationWarning>
      <body>
        <ThemeProvider attribute="class" defaultTheme="system" enableSystem>
          {children}
        </ThemeProvider>
      </body>
    </html>
  );
}
```

**ArtworkCard (simplified)**

```tsx
import Image from "next/image";
import { Card } from "@/components/ui/card";
import Link from "next/link";

type Props = { slug: string; title: string; year: number; image: string };
export function ArtworkCard({ slug, title, year, image }: Props) {
  return (
    <Link href={`/gallery/${slug}`} className="block group">
      <Card className="overflow-hidden">
        <div className="relative aspect-[4/3]">
          <Image src={image} alt={title} fill sizes="(max-width:768px) 100vw, 33vw" className="object-cover transition-transform duration-300 group-hover:scale-[1.02]" />
        </div>
        <div className="p-4 flex items-center justify-between">
          <h3 className="text-sm font-medium">{title}</h3>
          <span className="text-xs text-muted-foreground">{year}</span>
        </div>
      </Card>
    </Link>
  );
}
```

**Masonry (CSS columns approach)**

```tsx
export function Masonry({ children }: { children: React.ReactNode }) {
  return (
    <div className="[column-fill:_balance] columns-1 sm:columns-2 lg:columns-3 gap-6">
      <div className="[&>*]:mb-6 break-inside-avoid">{children}</div>
    </div>
  );
}
```

**Basic JSON‑LD for Artwork**

```tsx
export function ArtworkJsonLd({ title, year, image }: { title: string; year: number; image: string }) {
  const data = {
    "@context": "https://schema.org",
    "@type": "VisualArtwork",
    name: title,
    dateCreated: String(year),
    image,
  };
  return <script type="application/ld+json" dangerouslySetInnerHTML={{ __html: JSON.stringify(data) }} />;
}
```

---

## 17) Env & Config

`.env.example`

```
NEXT_PUBLIC_DEFAULT_LOCALE=en
NEXT_PUBLIC_SUPPORTED_LOCALES=en,ja
RESEND_API_KEY=
CONTACT_TO=email@example.com
```

`package.json` scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "format": "prettier --cache --write .",
    "typecheck": "tsc --noEmit"
  }
}
```

---

## 18) QA Checklist (Agent)

* [ ] Lighthouse ≥ 95 on Home, Gallery, Artwork Detail
* [ ] No console errors; network 0 3rd‑party blocking
* [ ] `alt` text present; color contrast AA+
* [ ] Focus order logical; skip link present
* [ ] Images have `sizes`; no layout shift
* [ ] i18n switch preserves route; `<html lang>` updates
* [ ] OG images render in link debuggers
* [ ] Contact form rate‑limited & validated

---

## 19) Future Enhancements

* Client‑side color/size filters powered by WASM palette extraction for “search by color”.
* Generative OG posters per artwork (title + palette swatches).
* CMS adapter (Sanity) with image pipelines and alt text QA.

---

### Done ✅

Use this playbook as your single source of truth. Execute tasks in order; commit early, open PRs with preview URLs, and verify acceptance criteria before merging.
