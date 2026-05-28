# Sam Ayres — Portfolio

Personal portfolio site built with [Astro](https://astro.build). Single scrolling home page with case study sub-pages. Dark theme, editorial typography, designed for senior engineering leadership roles.

## Quick start

```bash
npm install
npm run dev
```

Site runs at `http://localhost:4321`.

## Available scripts

| Command            | Description                                  |
| ------------------ | -------------------------------------------- |
| `npm run dev`      | Start dev server with hot reload             |
| `npm run build`    | Build production site to `./dist/`           |
| `npm run preview`  | Preview production build locally             |

## Project structure

```
src/
├── components/
│   ├── Hero.astro          Name, tagline, contact row
│   ├── Numbers.astro       "Receipts" metrics grid
│   ├── Work.astro          Tooling section (internal tools + infra)
│   ├── Websites.astro      Websites section (locc.it + commercial)
│   ├── WorkCard.astro      Individual case study card (clickable)
│   ├── SectionHead.astro   Reusable section heading
│   ├── Footer.astro        CTA footer with "Hiring?" prompt
│   ├── Diagram.astro       Wrapper for architecture diagrams
│   └── DiagramNode.astro   Individual labeled box in a diagram
├── layouts/
│   ├── Base.astro          HTML shell, meta tags, font loading
│   └── CaseStudy.astro     Case study page wrapper
├── pages/
│   ├── index.astro         Home page
│   ├── clients/
│   │   └── index.astro     Private client work (gated, noindexed)
│   └── work/
│       ├── production-pipeline.astro
│       ├── site-watch.astro
│       ├── timeline-generator.astro
│       ├── aws-topology.astro
│       └── locc-it.astro
└── styles/
    └── global.css          Design tokens and global styles

public/
├── favicon.svg
└── robots.txt              Disallows /clients
```

## Customizing content

Most content lives directly in component files.

- **Hero copy** — `src/components/Hero.astro`
- **Receipts metrics** — `src/components/Numbers.astro` (edit the `numbers` array)
- **Tooling cards** — `src/components/Work.astro` (edit the `workItems` array)
- **Websites cards** — `src/components/Websites.astro` (edit the `websiteItems` array)
- **Client work (private)** — `src/pages/clients/index.astro` (edit the `brandItems` array)
- **Footer CTA** — `src/components/Footer.astro`

Case study content lives in each page file under `src/pages/work/`.

## Design tokens

All colors, fonts, and layout values live in `src/styles/global.css` as CSS variables:

```css
--bg: #0e0f10;       /* page background */
--bg-2: #16181a;     /* card background */
--ink: #f5f3ee;      /* primary text */
--ink-soft: #8a8680; /* secondary text */
--teal: #5fc8c8;     /* accent */
--line: #26282c;     /* borders/rules */

--font-display: 'Canela Deck', 'Fraunces', serif;
--font-sans: 'IBM Plex Sans', sans-serif;
--font-mono: 'IBM Plex Mono', monospace;
```

The display font is **Canela Deck** with **Fraunces** as a free fallback. Until Canela Deck is licensed and loaded, visitors see Fraunces, which is included via Google Fonts.

## Visual features

- **Horizon beam** — a fixed teal glowing line at the top of the viewport with a soft fade band beneath it. Always visible, content fades into it as you scroll.
- **Scroll-triggered card highlights** — Tooling and Websites cards light up as they pass through the center of the viewport (uses IntersectionObserver, respects `prefers-reduced-motion`).
- **Architecture diagrams** — code-only diagrams built from CSS grid + labeled box components. No external assets.

## Adding case studies

To add a new case study:

1. Create a new file in `src/pages/work/` (e.g., `new-thing.astro`)
2. Use this skeleton:

```astro
---
import CaseStudy from '../../layouts/CaseStudy.astro';
import Diagram from '../../components/Diagram.astro';
import DiagramNode from '../../components/DiagramNode.astro';
---

<CaseStudy
  title="New Thing"
  tag="Internal"
  role="Director of Engineering"
  timeframe="2024"
  stack="Node · React"
  oneliner="One-sentence summary of the project."
>
  <p class="lede">Opening paragraph.</p>
  <h2>The problem</h2>
  <p>...</p>
  <!-- etc. -->
</CaseStudy>
```

3. Add a card to `Work.astro` or `Websites.astro` with a matching `slug: 'new-thing'`

## Deployment

Astro produces a fully static site. Any static host works:

- **Vercel**: connect repo, default settings work
- **Netlify**: build command `npm run build`, publish dir `dist`
- **Cloudflare Pages**: same as Netlify
- **Any static host**: run `npm run build`, deploy `dist/` directory

### DigitalOcean + nginx (current target)

Build with `npm run build` and serve the `dist/` directory with nginx.

The `/clients` route holds private commercial write-ups. It must be protected
with HTTP Basic Auth — the `noindex` tag and `robots.txt` keep it out of search,
but Basic Auth is the only real access control.

1. Create a credentials file (repeat without `-c` to add more users):

   ```bash
   sudo htpasswd -c /etc/nginx/.htpasswd recruiter
   ```

2. In the site's nginx `server` block, add a protected location:

   ```nginx
   location /clients {
       auth_basic           "Restricted — Client Work";
       auth_basic_user_file /etc/nginx/.htpasswd;
       try_files $uri $uri/ =404;
   }
   ```

3. Test and reload:

   ```bash
   sudo nginx -t && sudo systemctl reload nginx
   ```

Hand the credentials out in job applications or first replies — never link them publicly.

## Status

| Page                  | Status                                                  |
| --------------------- | ------------------------------------------------------- |
| Home                  | Complete — reframed for Director of Technology          |
| Production Pipeline   | Complete — fact-check specifics before publishing       |
| Site-Watch            | Complete — fact-check specifics before publishing       |
| Timeline Generator    | Complete — reframed; implementation detail kept light   |
| AWS Topology          | Complete — fact-check specifics before publishing       |
| locc.it               | Complete — rewritten and verified against the repo      |
| Client work           | Complete — gated /clients; protect with nginx Basic Auth |

The four case studies (`production-pipeline.astro`, `site-watch.astro`, `timeline-generator.astro`, `aws-topology.astro`) have been polished and reframed for the Director of Technology positioning. `timeline-generator.astro` was rewritten to focus on the problem, the decision to build, and the outcome rather than implementation internals. Production Pipeline, Site-Watch, and AWS Topology still contain specifics worth fact-checking against real project details before publishing.

The `locc-it.astro` page has been rewritten to match the actual `samayres1992/locc.it` repo — client-side encryption, one-time access, and required expiry. The GitHub handle is corrected to `samayres1992` site-wide, including in both résumés.

The healthcare brand work now lives on a private `/clients` page — one card per brand (Sequel, Axsome, Novo Nordisk, Servier, Journey, Acadia) with live sites and build stacks. Brand names appear only there, never on public pages; the public Websites section links to it with a no-names teaser. `/clients` carries a `noindex` tag and is disallowed in `robots.txt`, and must be protected with nginx Basic Auth (see Deployment).

The build stack for Servier's oncology sites (voranigo, voranigohcp, deeperthanmds, asparlas, takeoncca) has not yet been supplied, and deeper per-brand write-ups are still to be written.
