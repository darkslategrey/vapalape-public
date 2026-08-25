# Frontend Web

[Version française](README.md) | **English version**: [README.en.md](README.en.md)

The Vapalape frontend is the web application for the comparison platform. It presents the catalogue, search results, price comparisons, price histories, and editorial content, while also integrating account journeys and the assistant.

**Local source repository:** `../front-vapalape`

## Technical stack

| Layer | Technology |
| --- | --- |
| Framework | Next.js 16, App Router |
| UI | React 19, strict TypeScript |
| Styling | Tailwind CSS 4, shadcn/ui, Radix UI |
| Data access | TypeScript API client, `fetch`, SWR where appropriate |
| Forms | React Hook Form, Zod |
| Internationalisation | `next-intl`, French and English |
| Content | Markdown, unified, remark-gfm, rehype, gray-matter |
| Diagrams | Client-side Mermaid |
| Charts | Recharts |
| Assistant | AI SDK and `@ai-sdk/react` |
| Tests | Vitest, React Testing Library, Happy DOM |
| Quality | ESLint, TypeScript, Prettier |
| Deployment | PM2 on a VPS, GitHub Actions |

## User journeys

The App Router contains pages for:

- home, search, categories, products, brands, and retailers;
- product comparison and price history;
- promotions, price drops, coupons, and deals;
- favourites, price alerts, registration, and sign-in;
- guides, FAQ, methodology, legal, and privacy pages;
- public AI assistant and professional assistant area;
- administration and age verification.

Routes are localised under `app/[locale]/`. French is the default locale and English is handled with `next-intl`.

## API integration

The client in `lib/api/` maps Rails backend responses to TypeScript models consumed by React components. It handles JSON:API relationships and `included` resources, for example when resolving images associated with a product.

```ts
const data = await apiFetch<{
  data: Array<{ id: string; attributes: Product }>;
  meta: ApiMeta;
}>("/products", { q, category_id, page, per_page });
```

The client covers:

- products and search results;
- nested categories through `/categories/tree`;
- brands, retailers, and platform statistics;
- product details, variants, prices, history, attributes, and market intelligence;
- authentication, favourites, alerts, and user profile;
- professional assistant conversation history.

JWT tokens are managed in `lib/auth.ts`, with `localStorage` for persistent sessions and `sessionStorage` for the current session.

## Markdown blog and SEO

The blog is powered by Markdown files read from `BLOG_CONTENT_PATH`, without requiring a rebuild for every publication. The unified pipeline supports:

- frontmatter with title, slug, date, publication state, tags, and cover image;
- GitHub Flavored Markdown, tables, and task lists;
- HTML sanitisation with `rehype-sanitize`;
- heading links, code blocks, and Mermaid diagrams;
- French and English versions using the `<slug>.en.md` convention;
- sitemap and canonical multilingual URLs.

The `POST /api/revalidate` route invalidates one article or the whole blog layout after publication. Images are served through a dedicated route that protects against path traversal and applies a long cache lifetime.

## Code organisation

```text
app/
├── [locale]/             # localised product pages
├── blog/                 # blog routes and images
├── revalidate/           # controlled content invalidation
└── image-proxy/          # image proxy

components/
├── search/               # search, filters, and product cards
├── product/              # product page, prices, gallery, and alerts
├── brands/               # brand grid
└── ...

lib/
├── api/                  # Rails API client and types
├── auth.ts               # token management
├── blog.ts               # Markdown loading and rendering
└── product-attributes.ts # domain attributes
```

## Development and verification

Prerequisites: Node.js 24.15 or later and pnpm.

```bash
pnpm install
pnpm dev

pnpm lint
pnpm typecheck
pnpm test:run
pnpm build
```

CI runs linting, TypeScript checks, tests, and the production build. Production is deployed to `/home/deploy/vapalape-front` on the VPS and managed with PM2.

For further details: [`README.md`](../front-vapalape/README.md), [`docs/HOOKS.md`](../front-vapalape/docs/HOOKS.md), and [`DESIGN.md`](../front-vapalape/DESIGN.md).
