# Taiyaar contributor guide

## Project overview

Taiyaar is a mobile-first India document-readiness assistant. It helps a person prepare *before* applying for a service by turning a selected, structured procedure and their answers into a sourced, personalised checklist and explainable Readiness Score. It is a rules engine, not a chatbot.

v1 procedures: income certificate (Kerala and Tamil Nadu), driving licence (learner and permanent), JEE Main registration, and passport. Never present unverified government requirements as fact. Jinash Jaleel is the designated reviewer for substantive procedure-data and freshness changes.

## Recommended stack

Next.js (static-first) + TypeScript + React, Tailwind CSS, Zod, Vitest, Playwright, and i18next, deployed on Vercel. Procedures are versioned YAML/JSON content compiled and validated at build time. See `docs/ARCHITECTURE.md` before implementation.

## Planned repository layout

```
app/                 Next.js routes and UI
components/          accessible reusable UI
lib/                 rules engine, scoring, i18n, browser storage
data/procedures/     versioned procedure data files
data/evals/          AI intake/explanation evaluation fixtures
tests/               unit, integration, and browser tests
scripts/             validation and freshness-checker scripts
docs/                product and engineering decisions
```

## Commands (to be added with the implementation)

```bash
npm install
npm run dev
npm run test
npm run lint
npm run build
npm run freshness:check
```

Until the app exists, do not add dependencies or generated boilerplate merely to make these commands work.

## Coding conventions

- TypeScript strict mode; prefer small pure functions for rules and scoring.
- Keep rule data declarative. Adding a procedure must be a data-file change, not a code change.
- Validate every procedure against a schema in CI. Keep display text translatable; store stable IDs, not translated keys, in answers and tests.
- Use semantic HTML, keyboard access, visible focus, sufficient contrast, and responsive layouts from the smallest viewport upward.
- Keep network use optional and minimal; design the core journey to work after first load on a weak connection.
- Write tests beside or alongside all rules-engine and score changes. Avoid hidden business logic in UI components.

## Definition of done

A change is done when it is scoped, typed, linted, tested, accessible at mobile width, localised in English and Hindi where user-facing, and documented if it changes rules, score behaviour, privacy, or architecture. A data change additionally meets the rules below. No AI output may become a requirement without a matching dataset rule and citation.

## Rules-data changes

- Every requirement, condition, fee, validity constraint, and upload specification needs a direct official `sourceUrl` and `lastVerified` date.
- Include source title, stable rule/item ID, jurisdiction, effective/version metadata where known, and a review note when wording is ambiguous.
- Do not infer a rule from an unofficial summary. If official material is unavailable or unclear, mark it `TO VERIFY` and exclude it from definitive checklist/scoring behaviour until reviewed.
- Update fixtures and rules/scoring tests with every data change. The freshness checker only flags possible change; Jinash Jaleel approves substantive updates.

## Privacy and safety

- Never ask for, accept, transmit, log, or store Aadhaar numbers, ID numbers, passwords, OTPs, or identity-document uploads.
- Use self-attestation and client-side metadata checks only (for example, selected file size/type); do not inspect identity-document content.
- Keep answers in browser localStorage by default. Do not add accounts, server-side profiles, analytics containing personal data, or data sharing without an explicit approved reason and privacy review.
- Collect the minimum contextual answers needed for a rule; show users what is stored and provide a clear-data control.
- Design for India’s DPDP Act principles: notice, consent/purpose limitation, data minimisation, reasonable security, retention limits, and user control. Legal review is required before claims of compliance.
