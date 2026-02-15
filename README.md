# PAYE Calculator MVP

This repository contains a client-side PAYE calculator MVP for New Zealand income tax, KiwiSaver, and ACC levy.

## Try it

1. Start a static server from the repository root:

```bash
python -m http.server 8000
```

2. Open the app:

```
http://localhost:8000/src/mvp/paye-calculator/index.html
```

You can also browse all MVP features here:

```
http://localhost:8000/src/mvp/index.html
```

## Tests

```bash
npm install
npm test
```

## Deployment

GitHub Pages deployment is configured via [deploy workflow](.github/workflows/deploy.yml). Push to `main` to publish.

For manual static hosting (Netlify, etc.), deploy the repository root as a static site.

## Specification

See [PAYE calculator spec](specs/functional/PAYE-calculator.spec.md) for requirements.