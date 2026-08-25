# Frontend Web

Le frontend Vapalape est l'application web du comparateur. Il présente le catalogue, les résultats de recherche, les comparaisons de prix, les historiques et les contenus éditoriaux, tout en intégrant les parcours de compte et l'assistant.

**Code source local :** `../front-vapalape`

## Stack technique

| Couche | Technologie |
| --- | --- |
| Framework | Next.js 16, App Router |
| UI | React 19, TypeScript strict |
| Styles | Tailwind CSS 4, shadcn/ui, Radix UI |
| Données | Client API TypeScript, `fetch`, SWR selon les usages |
| Formulaires | React Hook Form, Zod |
| Internationalisation | `next-intl`, français et anglais |
| Contenu | Markdown, unified, remark-gfm, rehype, gray-matter |
| Diagrammes | Mermaid côté client |
| Graphiques | Recharts |
| Assistant | AI SDK et `@ai-sdk/react` |
| Tests | Vitest, React Testing Library, Happy DOM |
| Qualité | ESLint, TypeScript, Prettier |
| Déploiement | PM2 sur VPS, GitHub Actions |

## Parcours couverts

L'App Router contient des pages pour :

- accueil, recherche, catégories, produits, marques et boutiques ;
- comparaison de produits et consultation des historiques de prix ;
- promotions, baisses de prix, coupons et offres ;
- favoris, alertes de prix, inscription et connexion ;
- guides, FAQ, méthodologie, mentions légales et pages de confidentialité ;
- assistant IA grand public et espace assistant professionnel ;
- administration et vérification de majorité.

Les routes sont localisées sous `app/[locale]/`. Le français est la locale par défaut et l'anglais est géré par `next-intl`.

## Intégration avec l'API

Le client situé dans `lib/api/` transforme les réponses du backend Rails en modèles TypeScript utilisés par les composants React. Il gère notamment les relations JSON:API et les ressources `included`, par exemple pour résoudre les images associées à un produit.

```ts
const data = await apiFetch<{
  data: Array<{ id: string; attributes: Product }>;
  meta: ApiMeta;
}>("/products", { q, category_id, page, per_page });
```

Les fonctionnalités couvertes par le client incluent :

- produits et résultats de recherche ;
- catégories imbriquées via `/categories/tree` ;
- marques, boutiques et statistiques de plateforme ;
- détail produit, variantes, prix, historique, attributs et market intelligence ;
- authentification, favoris, alertes et profil utilisateur ;
- historique des conversations de l'assistant professionnel.

Les tokens JWT sont gérés dans `lib/auth.ts`, avec support de `localStorage` pour la session persistante et de `sessionStorage` pour la session courante.

## Blog Markdown et SEO

Le blog est alimenté par des fichiers Markdown lus depuis `BLOG_CONTENT_PATH`, sans rebuild obligatoire à chaque publication. Le pipeline unified prend en charge :

- frontmatter avec titre, slug, date, publication, tags et image de couverture ;
- Markdown GitHub, tableaux et task lists ;
- sanitation HTML avec `rehype-sanitize` ;
- liens de titres, coloration des blocs de code et Mermaid ;
- versions françaises et anglaises avec convention `<slug>.en.md` ;
- sitemap et URLs canoniques multilingues.

La route `POST /api/revalidate` permet d'invalider un article ou la mise en page globale du blog après publication. Les images passent par une route dédiée qui protège contre la traversée de répertoires et applique une durée de cache longue.

## Organisation du code

```text
app/
├── [locale]/             # pages localisées du produit
├── blog/                 # routes et images du blog
├── revalidate/           # invalidation contrôlée du contenu
└── image-proxy/          # proxy d'images

components/
├── search/               # recherche, filtres et cartes produit
├── product/              # fiche, prix, galerie et alertes
├── brands/               # grille des marques
└── ...

lib/
├── api/                  # client et types de l'API Rails
├── auth.ts               # gestion des tokens
├── blog.ts               # chargement et rendu Markdown
└── product-attributes.ts # attributs métier
```

## Développement et vérification

Prérequis : Node.js 24.15 ou supérieur et pnpm.

```bash
pnpm install
pnpm dev

pnpm lint
pnpm typecheck
pnpm test:run
pnpm build
```

La CI enchaîne lint, vérification TypeScript, tests et build. La production est déployée sur le VPS sous `/home/deploy/vapalape-front` et gérée par PM2.

Pour approfondir : [`README.md`](../front-vapalape/README.md), [`docs/HOOKS.md`](../front-vapalape/docs/HOOKS.md) et [`DESIGN.md`](../front-vapalape/DESIGN.md).
