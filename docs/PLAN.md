# Three-week plan

The project remains data-first: no procedure is called complete until it has reviewed sources, a schema-valid data file, rules/scoring fixtures, translations, and a human review record.

| Stage | Timing and milestone | Recommended model / effort | Parallel work |
| --- | --- | --- | --- |
| Spec | Week 1, days 1–2: confirm procedures, second income-certificate state, score policy, privacy notice, and source-review workflow | Strong reasoning model / high — decisions constrain all downstream work | Procedure source inventory can begin after jurisdictions are chosen |
| Design | Week 1, days 2–4: wireframes, accessibility/i18n content plan, schema, rule expression grammar, test/eval design approved | Strong reasoning model / high — architecture and failure modes matter | UX wireframes; dataset schema fixtures; source inventory |
| Implementation | Week 1 end–Week 2: static app shell, local storage, deterministic engine, scoring UI, i18n, first procedures | Feature-capable model / medium — bounded implementation with tests | UI shell; rules engine; individual reviewed procedure files |
| Testing | Week 2 end: unit/integration tests, mobile E2E, accessibility pass, AI adapter mocks and eval set | Strong reasoning model / high — catches non-obvious correctness and safety gaps | Rules/scoring tests; E2E/accessibility; AI evaluations |
| Review | Week 3, days 1–2: content/source review, privacy review, score calibration, Hindi copy review | Strong reasoning model / high — published guidance needs careful judgement | Content review; UX review; technical threat/privacy review |
| Deploy | Week 3, days 3–4: CI, static-host preview, production checklist, source/version display | Feature-capable model / medium — operational integration | CI/deployment; release notes; final smoke test |
| Maintenance | Week 3 day 5 onward: scheduled freshness checker, reviewer triage, issue templates, periodic regression runs | Boilerplate model / low for scripts; strong reasoning model / high for flagged-source decisions | Automation setup; documentation; reviewer runbook |

## Weekly milestones

- **Week 1 — foundation approved:** product boundaries, schema, source inventory, UX prototype, and test/eval fixtures are agreed; no unreviewed rule content is shipped.
- **Week 2 — working private prototype:** a phone-friendly local-only journey produces sourced checklists and explainable scores from deterministic test data; four procedure data sets are in review.
- **Week 3 — release candidate:** reviewed v1 coverage, English/Hindi UI, passing quality gates, static deployment, freshness workflow, and launch review are complete.

## Suggested implementation order

1. Establish the schema, source provenance requirements, rule evaluator, and score tests.
2. Build manual intake and checklist/score rendering with fixtures.
3. Add storage, i18n, accessibility, and each reviewed procedure data file.
4. Add the optional AI adapter behind the confirmed-answer flow and run its eval gate.
5. Add CI, freshness checks, deployment, and final review.
