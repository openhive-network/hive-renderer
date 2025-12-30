# CLAUDE.md - Hive Content Renderer

## Project Overview

**hive-renderer** is a TypeScript library that renders Hive blockchain posts and comments to HTML. Published on NPM as `@hiveio/content-renderer`, it handles markdown parsing, HTML sanitization, media embedding (YouTube, Vimeo, Twitter, Spotify, Twitch, 3Speak), and link processing with XSS protection.

## Tech Stack

- **Language:** TypeScript 5.5 (strict mode)
- **Runtime:** Node.js >= 20
- **Core:** Remarkable (markdown), sanitize-html, @xmldom/xmldom
- **Build:** Webpack 5 (UMD browser bundle)
- **Testing:** Mocha + Chai, TestCafe (browser), NYC (coverage)
- **Linting:** ESLint 8 + Prettier (Engrave configs)
- **Release:** Semantic Release (automated versioning)

## Directory Structure

```
src/
├── index.ts                    # Main exports
├── Log.ts                      # Singleton logger
├── renderers/default/
│   ├── DefaultRenderer.ts      # Core renderer class
│   ├── Localization.ts         # i18n strings
│   ├── StaticConfig.ts         # Iframe whitelist patterns
│   ├── embedder/
│   │   ├── AssetEmbedder.ts    # Embedder orchestration
│   │   ├── HtmlDOMParser.ts    # DOM processing
│   │   ├── embedders/          # YouTube, Twitter, Vimeo, etc.
│   │   └── utils/              # Links.ts, BadActorList.ts
│   └── sanitization/           # HTML sanitizers
└── security/
    └── SecurityChecker.ts      # Script tag validation

browser-test/                   # TestCafe integration tests
sample/                         # Demo files and live-demo.html
dist/                           # Build output (gitignored)
```

## Development Commands

```bash
npm run build          # Full build (clean + node + browser)
npm run build:node     # TypeScript compilation only
npm run build:browser  # Webpack UMD bundle only
npm test               # Run Mocha unit tests
npm run test:coverage  # Tests with Cobertura coverage
npm run lint           # ESLint check
npm run lint:fix       # Auto-fix lint issues
npm run verify:chrome  # TestCafe browser tests (Chrome)
npm run verify:firefox # TestCafe browser tests (Firefox)
```

## Key Files

| File | Purpose |
|------|---------|
| `src/renderers/default/DefaultRenderer.ts` | Main renderer with full pipeline |
| `src/renderers/default/StaticConfig.ts` | Iframe domain whitelist patterns |
| `src/renderers/default/embedder/embedders/` | Media embedder implementations |
| `src/renderers/default/sanitization/TagTransformingSanitizer.ts` | HTML sanitization logic |
| `webpack.config.js` | Browser bundle configuration |
| `tsconfig.build.json` | Production TypeScript config |

## Coding Conventions

### Patterns
- **Strict validation:** All public constructors validate inputs with `ow()` library
- **Abstract base classes:** `AbstractEmbedder` for pluggable embedders
- **Co-located tests:** Each module has `.test.ts` in same directory

### Naming
- PascalCase for classes: `DefaultRenderer`, `TwitterEmbedder`
- camelCase for methods/properties
- UPPER_CASE for static regex patterns
- Suffix `Embedder` for media embed classes, `Sanitizer` for sanitizers

### Security
- HTML sanitization before rendering
- Script tag rejection (unless explicitly allowed)
- Link safety validation via callback

### Commits
Conventional Commits required:
```
feat: add new feature
fix: bug fix
chore: maintenance
test: add tests
```

## CI/CD Notes

**.gitlab-ci.yml** stages:

**Test Stage:**
- `lint:code` - ESLint validation
- `lint:commit message` - CommitLint check
- `test:unit` - Mocha + coverage (Cobertura)
- `test:browser` - TestCafe in Docker

**Release Stage (master only):**
- `release` - Semantic-release to NPM
- `pages` - Deploy demo to GitLab Pages

**Pre-commit hook:** Runs `npm test` via Husky

**NPM Package:** `@hiveio/content-renderer`
**Live Demo:** https://hive.pages.syncad.com/hive-renderer/
