# PAYE Calculator MVP

This repository contains a client-side PAYE calculator MVP for New Zealand income tax, KiwiSaver, and ACC levy.

## Try it

Option 1: Vite dev server (recommended)

```bash
cd src/mvp/paye-calculator
npm install
npm run dev
```

Open the app:

```
http://localhost:3000
```

Option 2: Static server

Start a static server from the repository root:

```bash
python -m http.server 8000
```

Open the app:

```
http://localhost:8000/src/mvp/paye-calculator/index.html
```

You can also browse all MVP features here:

```
http://localhost:8000/src/mvp/index.html
```

## Tests

```bash
cd src/mvp/paye-calculator
npm test
npm run test:coverage
npm run test:e2e
```

## ACC Configuration

Defaults live in `src/mvp/paye-calculator/src/services/payeService.ts` (`ACC_RATE`, `ACC_CAP`).

The functional spec contains inconsistent ACC values. This MVP uses the 1.53% rate with the $139,384 cap. Update these constants once authoritative values are confirmed.

## Deployment

GitHub Pages deployment is configured via [deploy workflow](.github/workflows/deploy-paye-calculator.yml). Push to `main` to publish.

For manual static hosting (Netlify, etc.), deploy the `src/mvp/paye-calculator/dist` output after running `npm run build` in the feature directory.

## Specification

See [PAYE calculator spec](specs/functional/PAYE-calculator.spec.md) for requirements.


# Using the Orchestrator Agent

Set the agent to the `Orchestrator` custom agent.

Try these two prompts:

```text
Create work items for the #file:PAYE-calculator.spec.md to build an MVP.
```

Check that all the work items have been created.

Then run the following prompt:

```text
Implement the work items for the #file:PAYE-calculator.spec.md to code the MVP.
```

That's it.