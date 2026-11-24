# Taiyaar product specification

## User stories

- As an applicant, I can select a procedure and jurisdiction so I see only relevant preparation steps.
- As an applicant, I can answer short, plain-language questions (or describe my situation) and review/correct structured answers before rules are applied.
- As an applicant, I can see a checklist whose every item links to an official source, plus why it applies to me.
- As an applicant, I can understand a Readiness Score, detected risks, and the highest-impact next action.
- As an applicant, I can use the product on a low-bandwidth phone in English or Hindi, and clear my local data.
- As a content reviewer, I can add or revise a procedure through validated data and trace every rule to a source.

## Scope and non-goals

v1 includes Kerala and Tamil Nadu income certificates, learner/permanent driving licence, JEE Main registration, and passport. It provides preparation guidance—not application submission.

Non-goals: collecting documents or ID numbers; uploading identity documents; submitting government forms; legal advice; eligibility determination beyond declared facts and explicit rules; live government-portal automation; and a general-purpose chatbot.

## Procedure data schema

One data file represents one jurisdiction-specific procedure version. IDs are stable and machine-readable. Any factual field must have official provenance and a verification date.

```yaml
id: passport.india.standard.v1                 # TO VERIFY example identifier
titleKey: procedures.passport.title
jurisdiction: { country: IN, state: null }
version: "TO VERIFY"
lastVerified: "TO VERIFY (YYYY-MM-DD)"
officialSources:
  - id: passport-official
    title: "TO VERIFY official source title"
    url: "TO VERIFY official URL"
intakeQuestions:
  - id: applicant_age_band
    type: singleSelect
    promptKey: intake.ageBand
    options: [under_18, adult]                 # values are neutral examples
documents:
  - id: identity-proof
    labelKey: documents.identityProof
    requiredWhen: "true"
    importance: critical                       # critical | high | standard
    sourceRef: passport-official
    sourceAnchor: "TO VERIFY section/reference"
    validity: { kind: none }
    checks:
      - kind: selfAttestedAvailable
    explanationKey: documents.identityProof.why
conditions:
  - id: minor-consent
    when: "applicant_age_band == 'under_18'"
    addDocumentIds: ["TO VERIFY"]
fees:
  - id: standard-fee
    amount: "TO VERIFY"
    currency: INR
    requiredWhen: "true"
    sourceRef: passport-official
uploadSpecs:
  - id: photo
    appliesWhen: "TO VERIFY"
    fileTypes: ["TO VERIFY"]
    maxSizeKb: "TO VERIFY"
    dimensions: "TO VERIFY"
    sourceRef: passport-official
```

The example intentionally contains no government requirement. `requiredWhen` uses a constrained expression language over reviewed intake answers; it cannot execute code. A schema validator requires sources, `lastVerified`, translations, and source references for all externally visible factual rules.

### `requiredWhen` expression grammar

Conditions are parsed, not evaluated as JavaScript. Whitespace may appear between tokens.

```text
expression   ::= orExpression
orExpression ::= andExpression ( "||" andExpression )*
andExpression::= unaryExpression ( "&&" unaryExpression )*
unaryExpression ::= "!" unaryExpression | primary
primary      ::= "true" | "false" | comparison | "(" expression ")"
comparison   ::= identifier ( "==" | "!=" ) literal
identifier   ::= /[a-z][a-z0-9_]* /
literal      ::= string | "true" | "false"
string       ::= "'" characters "'"
```

An `identifier` must exactly match a declared intake-question ID; string literals must exactly match an allowed option value for that question. Boolean literals are permitted only for boolean questions. No property access, arrays, arithmetic, functions, regex, implicit coercion, or undeclared identifiers are allowed. Invalid expressions fail schema validation at build time; evaluation is deterministic and side-effect free.

## Readiness Score

Keep score policy in a versioned scoring-config file, not UI code. Start with 100. Generate applicable checklist obligations first, then deduct unresolved weighted items: critical 25, high 15, standard 8 (cap aggregate deductions at 100). Apply issue penalties only once per underlying issue: expired or wrong-period evidence 20, declared name mismatch 15, category mismatch 20, upload-spec violation 10, and required fee/budget not ready 10. The base score is `max(0, 100 - deductions)`. If any applicable critical item is missing, the final score is capped at 60 and the status is `not ready`; otherwise status is derived from the configured score bands.

The UI must show the equation in human terms: fulfilled items, missing items, issues, deductions, and the ordered actions that recover points. A score is preparation readiness, not approval probability or eligibility. Values are initial product policy and must be covered by fixtures before implementation.

## AI-layer boundaries

The deterministic rules engine is authoritative. AI may convert voluntary free text into a proposed, typed answer object and produce plain-language explanations *from supplied rule IDs and source snippets*. The user confirms all extracted answers. AI may not create requirements, sources, fees, conditions, eligibility conclusions, or checklist items. On uncertainty it asks a neutral question or returns no answer. Every rendered checklist requirement comes from the data file and carries its source link.

Eval fixtures will assert that known scenarios produce only permitted answer fields and that explanations cite supplied rules without adding claims. The app must work with a mocked AI adapter or no AI at all.

## Internationalisation

Use stable translation keys in procedure data, with English and Hindi resource files from day one. Do not translate IDs or condition values. Localise UI strings, source labels, dates, number formatting, and explanations while preserving official source URLs and precise requirement wording. Hindi copy should be professionally reviewed before release; fallback is English with a clear indicator.
