# CLAUDE.md — Designers Italia

Developer guide for AI assistants working in this repository.

## Project Overview

**designers.italia.it** is the official website of the Italian government's design reference for public digital services. It hosts documentation for the [Design system .italia](https://designers.italia.it/design-system/) (built on top of Bootstrap Italia), community content (news, events, media), templates, training resources, and project info.

- **Framework**: Gatsby v5 (React 18, Node.js)
- **Design system**: [Bootstrap Italia](https://italia.github.io/bootstrap-italia/) v2.x
- **Content format**: YAML files (data-driven pages)
- **Language**: Italian (UI and content)
- **License**: BSD-3-Clause
- **Live site**: https://designers.italia.it

---

## Repository Structure

```
/
├── gatsby-config.js          # Gatsby plugins, site metadata, search config
├── gatsby-node.js            # Schema customization, remote asset handling
├── gatsby-browser.js         # Scroll restoration behavior
├── gatsby-ssr.js             # SSR hooks
├── package.json              # Dependencies and npm scripts
├── .eslintrc                 # ESLint rules (Airbnb + React + a11y + Prettier)
├── .editorconfig             # Code style (2-space indent, LF, UTF-8)
├── scripts/                  # Build utilities (content prep, example generation)
├── server/                   # Node.js utilities used during build
├── static/                   # Static assets (images, fonts, SVGs, dist)
│   ├── images/
│   ├── fonts/                # Auto-copied from bootstrap-italia at build time
│   ├── svg/                  # Auto-copied from bootstrap-italia at build time
│   └── dist/                 # Auto-copied from bootstrap-italia at build time
└── src/
    ├── components/           # Reusable React components (30+ modules)
    ├── templates/            # Gatsby page templates (level-1 through level-4, archives, etc.)
    ├── pages/                # Dynamic page files ({content.relativePath}.js, 404.js)
    ├── data/
    │   ├── content/          # All site content as YAML files
    │   ├── editorial-board/  # Editorial team YAML data
    │   └── components_json/  # Bootstrap Italia component examples (generated)
    ├── scss/                 # Global SCSS styles
    ├── images/               # Source images
    └── js/                   # Global JS utilities (globals.js)
```

### Content directory layout (`src/data/content/`)

```
content/
├── index.yaml                # Homepage
├── design-system/            # Design system documentation (nested YAML)
├── community/
│   ├── notizie/              # News articles (YAML per item)
│   ├── eventi/               # Events (YAML per item)
│   └── media/                # Media (YAML per item)
├── formazione/               # Training resources
├── modelli/                  # Page/service templates
├── progetto/                 # About the project
├── normativa/                # Legal/regulatory content
├── argomenti.yaml            # Topics/tags index
├── dsnav.yaml                # Design system sidebar navigation + version tag
├── header.yaml               # Site header data
├── footer.yaml               # Site footer data
└── skiplinks.yaml            # Skip navigation links
```

### Component structure

Each component lives in its own folder:
```
src/components/component-name/
├── component-name.js         # React component
└── component-name.scss       # Scoped styles (optional)
```

---

## Development Workflow

### Prerequisites

- Node.js 18
- npm

### Quick start

```bash
# 1. Install dependencies
npm i

# 2. Copy Bootstrap Italia assets (fonts, SVGs, dist)
# This is included in npm run start automatically

# 3. Start local development server
npm run dev
# → http://localhost:8000
```

### Prepare Design System component previews (optional)

Run this to download and generate Bootstrap Italia component examples used in Design System cards:

```bash
npm run prepare-content
```

This downloads examples from the Bootstrap Italia API, generates HTML previews, and prepares component status data.

### Production build

```bash
npm run build
# Output in /public
```

### Development build (faster, less optimization)

```bash
npm run build:dev
```

### CI build (skips clean, relies on cache)

```bash
npm run build:ci
```

---

## Code Quality

### Linting

```bash
npm run lint          # Check only
npm run lint:fix      # Auto-fix issues
```

ESLint config (`.eslintrc`):
- Extends: `eslint:recommended`, `plugin:react/recommended`, `plugin:jsx-a11y/recommended`, `airbnb`, `airbnb/hooks`, `plugin:prettier/recommended`
- Parser: `@babel/eslint-parser` with `babel-preset-gatsby`
- Key rules enforced:
  - `react/react-in-jsx-scope: "error"` — React must be in scope for JSX
  - `jsx-a11y/*` — Comprehensive accessibility checks (all as warnings)
  - `no-plusplus` — Disallowed except in for-loop afterthoughts
  - `no-shadow: "warn"`
  - `react/jsx-props-no-spreading: "off"` — Spreading allowed for now
  - `react/no-array-index-key: "off"` — Array index keys allowed for now
  - `import/extensions: "off"` — No extension requirement on imports
  - `no-underscore-dangle: "off"` — Underscore-prefixed vars allowed

### Formatting

```bash
npm run prettier          # Check only
npm run prettier:fix      # Auto-format
```

Prettier v3.0.3 (no explicit `.prettierrc` — defaults apply). Ignored: `.cache/**`, `**/*.min*` files.

### Editor config (`.editorconfig`)

- Indent: 2 spaces
- Line endings: LF
- Encoding: UTF-8
- Trim trailing whitespace: true
- Final newline: required

---

## Content Management

### Content as YAML

All pages are data-driven YAML files in `src/data/content/`. The YAML structure maps to React component props via Gatsby templates.

**YAML comment conventions** (used throughout content files):
- `#C` — Editable content (text, copy)
- `#M` — Links and navigation
- `#I` — Images/media
- `#AUTO` — Parts that will be automated
- Comment out with `#` to hide something
- **Delete with caution** — structure drives page rendering

**Typical YAML structure:**
```yaml
metadata:
  template:
    name: level1          # Template to use (level1–level4, home, etc.)
  activeLabel: "Label"    # Highlights this nav item as active

seo:
  name: "Page title"      # Max 60 chars; append "- Designers Italia" for SEO
  description: "..."      # Max 160 chars, end with period
  image: https://designers.italia.it/images/...  # 1200×630 px for social
  pathname: /section/     # Must start and end with /

components:
  hero:
    title: "..."
    subtitle: "..."
    # ...component-specific props
```

### Editorial content (news, events, media)

Files go in:
- News: `src/data/content/community/notizie/YYYYMM-slug.yaml`
- Events: `src/data/content/community/eventi/YYYYMM-slug.yaml`
- Media: `src/data/content/community/media/YYYYMM-slug.yaml`

File name format: `YYYYMM-titolo-tutto-minuscolo.yaml`

For full editorial content specs, see [HOW-TO-MANAGE-EDITORIAL-CONTENT.md](HOW-TO-MANAGE-EDITORIAL-CONTENT.md).

### Last-modified date tracking

The `@colliercz/gatsby-transformer-gitinfo` plugin automatically derives the last-modified date from git commit history for YAML files.

To update a file **without** changing its displayed modification date, include `(last-update-skip)` in the commit message.

---

## Page Templates

Templates are in `src/templates/`. Each corresponds to a hierarchy level:

| Template | Used for |
|---|---|
| `home.js` | Homepage |
| `level-1.js` | Top-level sections (design-system, community, etc.) |
| `level-2.js` | Section sub-pages |
| `level-3.js` | Deeper content pages |
| `level-4.js` | Deepest content pages |
| `design-system-component.js` | Design system component cards |
| `design-system-index.js` | Design system section index |
| `archive-news.js`, `archive-events.js`, `archive-media.js` | Content archives |
| `tag.js` | Tag pages |
| `search-results.js` | Search results |
| `base.js` | Base template |

The template used for a page is specified in its YAML `metadata.template.name`.

---

## Gatsby Plugins (key ones)

| Plugin | Purpose |
|---|---|
| `gatsby-plugin-breadcrumb` | Auto-generated breadcrumbs with Italian labels |
| `gatsby-plugin-sass` | SCSS compilation (includes `node_modules/`) |
| `gatsby-plugin-sharp` / `gatsby-transformer-sharp` | Image optimization (webp, avif) |
| `gatsby-plugin-image` | Gatsby Image component |
| `gatsby-plugin-local-search` | FlexSearch full-text search |
| `gatsby-plugin-matomo` | Analytics (env vars: MATOMO_SITE_ID, MATOMO_SITE_URL) |
| `gatsby-plugin-satorare` | Social card (OG image) generation |
| `gatsby-plugin-meta-redirect` | Page-level redirects |
| `gatsby-plugin-htaccess` | Apache `.htaccess` file generation |
| `@colliercz/gatsby-transformer-gitinfo` | Git-based last-modified dates |
| `gatsby-transformer-yaml` | YAML → GraphQL nodes |

---

## CI/CD

GitHub Actions workflows in `.github/workflows/`:

| Workflow | Trigger | What it does |
|---|---|---|
| `tests.yml` | Pull requests | `npm ci` → `build:dev` → `lint` (Node 18) |
| `deploy.yml` | Manual | Full production build + deploy to GitHub Pages |
| `prepare-deploy.yml` | Manual | Generates SEO card images before deploy |
| `bsi-update.yml` | Daily / manual | Updates Bootstrap Italia and regenerates previews |
| `lighthouse.yml` | Scheduled | Lighthouse performance audits |
| `lighthouse-pr-comment.yml` | PRs | Posts Lighthouse scores as PR comments |

**Production deploy sequence** (maintainers only):
1. Run `prepare-deploy` (if new images exist, ~5 min)
2. Run `deploy` (5–30 min, uses Gatsby cache)

**Required secrets**: `MATOMO_SITE_ID`, `MATOMO_SITE_URL`, `GITHUB_TOKEN`

---

## Design System Release Process

For releasing new versions of the Design system documentation, the key files to update are:

| File | What to change |
|---|---|
| `src/data/content/dsnav.yaml` | `tag.label` → new version (e.g., `"v1.X.Y"`) |
| `src/data/content/design-system/fondamenti/versionamento.yaml` | Versions table + new changelog entry |

Branch naming convention: `release-v1-X-Y`

PR title format: `chore(release): design system .italia v1.X.Y 🚀 🇮🇹`

For the full release process, see [HOW-TO-RELEASE-DESIGN-SYSTEM.md](HOW-TO-RELEASE-DESIGN-SYSTEM.md).

---

## Commit Message Conventions

The project uses semantic commit messages:

```
type(scope): short description

Examples:
feat: add new component
fix: correct breadcrumb label
chore(deps): update bootstrap italia v2.16.2
chore(release): design system .italia v1.9.1 🚀 🇮🇹
content: update highlight cards
refactor: new BI card design
```

Special: include `(last-update-skip)` anywhere in the commit message to skip updating the displayed last-modified date for changed YAML content files.

---

## Environment Variables

| Variable | Used in | Purpose |
|---|---|---|
| `MATOMO_SITE_ID` | Production build | Matomo analytics site ID |
| `MATOMO_SITE_URL` | Production build | Matomo analytics URL |
| `GATSBY_BUILD` | Dev build | Set to `dev` for development builds |
| `NODE_ENV` | Gatsby | `development` or `production` |

`.env.development` and `.env.production` files exist (empty by default — add secrets locally, never commit them).

---

## Key Conventions

1. **YAML is the source of truth for content** — components render whatever the YAML provides; avoid hardcoding content in JSX.
2. **Accessibility is mandatory** — `jsx-a11y` rules are enforced. All images need alt text, all interactive elements need keyboard support.
3. **Bootstrap Italia components** are preferred over custom UI — use existing design system classes and components.
4. **Run lint before committing** — `npm run lint:fix && npm run prettier:fix`.
5. **Font and SVG assets are auto-managed** — don't manually edit files in `static/fonts/`, `static/svg/`, or `static/dist/`; they are overwritten by `npm run prepare-lib`.
6. **Search index** is built at Gatsby build time using FlexSearch. Indexed fields: title, description, body.
7. **Images for content**: Hero images 1920×923 px, SEO/social images 1200×630 px, max 800 KB, JPG or PNG.
8. **No TypeScript** — the project uses plain JavaScript with Babel.
9. **No test suite** — validation is done via build (`build:dev`) + lint in CI.

---

## Common Tasks

### Add a news article

Create `src/data/content/community/notizie/YYYYMM-slug.yaml` following the YAML schema in [HOW-TO-MANAGE-EDITORIAL-CONTENT.md](HOW-TO-MANAGE-EDITORIAL-CONTENT.md).

### Update Bootstrap Italia version

1. Update version in `package.json`
2. Run `npm install`
3. Run `npm run prepare-content` to regenerate component examples
4. Update `src/data/content/dsnav.yaml` tag label if it's a design system release

### Add or update a page

1. Find or create the corresponding YAML file in `src/data/content/`
2. Follow the existing YAML structure and comment conventions
3. Reference the appropriate template in `metadata.template.name`

### Fix a component

1. Edit `src/components/<component-name>/<component-name>.js`
2. Run `npm run dev` to preview changes at http://localhost:8000
3. Run `npm run lint:fix` before committing

### Clear Gatsby cache (when builds behave unexpectedly)

```bash
npm run clean
```
