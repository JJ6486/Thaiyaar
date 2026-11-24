# Taiyaar architecture

## Recommended stack

Use Next.js with TypeScript in static-first mode, deployed on a free static host such as Cloudflare Pages or Vercel. Use React for UI, Zod for data validation, i18next/react-i18next for translation, Vitest for unit tests, Playwright for the critical mobile journey, and a lightweight GitHub Actions workflow for validation. Keep the first release client-only: browser storage avoids accounts and a database.

## Module boundaries

```
data/procedures -> procedure schema validator -> rules engine -> scoring engine -> checklist view
                                            \-> source/citation renderer
intake UI/free text -> AI adapter -> proposed typed answers -> user confirmation -> rules engine
browser storage <-> answers and completion state only
scripts/freshness-checker -> change report -> human review
```

- `data/procedures`: declarative, versioned YAML/JSON and source metadata.
- `lib/schema`: parses data at build/test time and rejects uncited or stale-shaped facts.
- `lib/rules`: evaluates constrained conditions against confirmed answers and outputs applicable obligations plus provenance.
- `lib/scoring`: pure, explainable score calculation from obligations and declared check state.
- `lib/ai`: an interface with `extractAnswers` and `explainRule`; provider implementation is optional and a deterministic fake supports tests.
- `lib/storage`: namespaced localStorage with explicit clear/export controls; no sensitive values permitted.
- `app` and `components`: accessible, mobile-first screens that render engine output rather than duplicating logic.

## AI isolation and testing

Define a typed adapter boundary. The adapter receives only approved intake fields and rule/source context, returns a schema-validated proposal, and cannot call the scoring or checklist renderer. Provider errors and absent configuration fall back to manual intake. Contract tests run the same eval cases against a fake adapter and, when available, a provider adapter. The evaluation gate rejects hallucinated IDs, sources, requirements, or field values.

## Deployment

Build static assets in CI, run data validation, unit tests, E2E smoke tests, and the AI eval gate before deployment. Store no user answers in deployment logs or analytics. Use cache-friendly immutable asset hashes and a small offline shell only after its update behaviour is tested. Publish a privacy notice and a source/version display before public launch.

## Freshness checker

A scheduled GitHub Actions job fetches each unique official source URL with polite rate limits, conditional requests (`ETag`/`Last-Modified`) where available, timeout/retry limits, and a descriptive user agent. It records status, retrieval time, redirect target, and a normalised content hash; it must not submit forms or bypass access controls. A changed hash, failed request, or redirect opens a review report/issue containing affected procedure IDs and a diff link or stored permitted snapshot. It never automatically changes rules or deploys new facts. A human validates the source, updates `lastVerified` and data version, then approves the change.
