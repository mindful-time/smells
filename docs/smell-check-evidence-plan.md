# Smell-check evidence and catalog plan

Status: proposed
Date: 2026-09-27

## Decision summary

Every smell check must produce a deterministic, reviewable argument rather than
only a label. A finding must answer:

1. **What was checked?** The concept, detector, subject, language, and source
   scope.
2. **What was observed?** The exact measurements, relationships, and source
   locations used by the detector.
3. **Why did it match?** The versioned comparison or composite expression, with
   every clause shown as passed, failed, or unavailable.
4. **How complete was the analysis?** Resolution coverage, unresolved evidence,
   budgets, exclusions, and failed prerequisites.
5. **What should be reviewed?** Counterexamples and contextual questions, not an
   automatic instruction to refactor.
6. **Where did the concept come from?** Canonical Smells concept identity and
   cited source records, without copying substantial source prose.

The scanner owns observations and the deterministic match. A human or AI agent
owns the contextual design decision.

## Product model

Keep these identities separate:

```text
Source record
    supports/names/classifies
Canonical concept
    is operationalized by
Language detector
    evaluates
Evidence observations
    produces
Measurement(s) and optional Finding
```

- A **source record** identifies Fowler, Refactoring.Guru, Mäntylä,
  Lanza–Marinescu, DECOR, or a particular edition.
- A **concept** is the stable user-facing idea, such as `god-class`.
- A **detector** is a versioned language-specific operationalization, such as
  `python.god_class`.
- An **evidence primitive** is an observation such as class cohesion, outgoing
  providers, a write site, or function nesting.
- A **measurement** records one completed evaluation, including nonmatches.
- A **finding** is a matched detector result referencing the measurements and
  evidence that support it.

Taxonomy categories are browse-only views. Execution groups are explicit rule-
pack configuration. Changing a taxonomy must never change which rules run.

## Concrete catalog inventory

The attached screenshot is Fowler's second-edition Chapter 3 table of contents.
It contains exactly 24 smells. Organize the counts as source views over one
deduplicated catalog:

| View | Count | Meaning |
| --- | ---: | --- |
| Current Smells v1 / Refactoring.Guru | 23 | The currently supported concepts and detectors. |
| Fowler second edition | 24 | Twenty concepts overlap v1, sometimes under newer names, and four are new to Smells. |
| Union of current v1 and Fowler 2e | 27 | The current 23 plus Mysterious Name, Global Data, Mutable Data, and Loops. |
| Lanza–Marinescu and DECOR additions after deduplication | 9 | God Class, Brain Method, Brain Class, Intensive Coupling, Dispersed Coupling, Tradition Breaker, Functional Decomposition, Spaghetti Code, and Swiss Army Knife. |
| Total researched canonical concepts | 36 | Twenty-three currently supported plus thirteen without stable detectors. |

The Fowler 2e view must render the 24 names exactly as that source organizes
them. The Refactoring.Guru view must render its 23. Neither source view defines
the runtime pack inventory.

The three current concepts outside Fowler 2e's Chapter 3 list are Dead Code,
Parallel Inheritance Hierarchies, and Incomplete Library Class. They remain in
the catalog with their own provenance; Fowler 2e's omission does not silently
delete them.

The following are aliases or refinements, not additional canonical concepts:

- Long Method / Long Function;
- Switch Statements / Repeated Switches;
- Lazy Class / Lazy Element;
- Inappropriate Intimacy / Insider Trading;
- God Class / Blob;
- Duplicate Code / Significant Duplication, unless later product research
  proves a distinct user decision.

The catalog can therefore know about 36 concepts while a language pack supports
only a subset. A catalog count must never be presented as an active-detector
count.

## Separate concept and detector states

Do not use one `lifecycle` field for both literature acceptance and executable
support.

Concept state:

```text
candidate -> accepted -> deprecated -> retired
```

Detector support, independently for each language:

```text
none -> research -> preview -> stable -> deprecated -> removed
```

For example, `global-data` can be an accepted catalog concept while
`python.global_data` is preview, `typescript.global_data` is research, and
`rust.global_data` is not yet implemented. `loops` can remain an accepted
Fowler concept with detector support `none` because a generic loop warning is
not currently a good executable rule.

## Deep analysis module

The external seam should remain small:

```text
analyze(captured_input, rule_pack, policy) -> analysis_result
```

The analysis module hides:

- source and history collection;
- symbol, owner, call, inheritance, and write/read resolution;
- reusable evidence calculation;
- simple and composite detector evaluation;
- completeness accounting;
- deterministic ordering and normalized evidence IDs.

Collectors and detector evaluators can retain internal seams for testing, but
their implementation details should not leak into the caller interface. Tests
should primarily assert on `analysis_result` at this seam.

## Common evidence envelope

Every detector, existing or new, must return the following fields.

### Identity and provenance

- concept ID and preferred name;
- detector ID and detector contract version;
- rule-pack, scanner, catalog, and source-manifest versions/digests;
- language and applicability state;
- concept source references and relationship types;
- concept state and language-specific detector support state.

### Subject and locations

- stable subject ID and symbol;
- primary repository-relative location;
- related symbols, locations, and typed relationships;
- bounded source excerpts for the primary location and the most important
  related locations;
- explicit count of omitted excerpts, while retaining all locations.

### Observations

- typed evidence primitive ID and version;
- observed value and unit;
- population and source scope;
- resolution method;
- evidence locations or graph edges;
- exclusions applied, with reason codes;
- provider identity when evidence came from an external provider.

### Deterministic evaluation

- metric or composite expression ID;
- parameters and configured thresholds;
- comparator or composition operator;
- per-clause result;
- final matched result;
- certainty classification;
- policy mode and blocking status.

Composite detectors must expose their expression tree. An opaque score is not
sufficient. For example, a God Class result should say which size, cohesion,
foreign-data, and centrality clauses matched.

### Completeness and limitations

- files and implementations examined;
- resolved and unresolved edge counts;
- unavailable prerequisites;
- resource budgets used and whether any limit was reached;
- generated, vendor, test, configuration, and explicit project exclusions;
- known language/model limitations;
- coverage status.

If required evidence is unavailable, the result is `incomplete`; it must not be
reported as a clean nonmatch.

### Review guidance

- Smells-authored explanation of why the evidence may matter;
- detector-specific review questions;
- known legitimate counterexamples;
- behavior-preserving remediation direction;
- cited source records.

Guidance informs review. It does not change the deterministic match and does not
claim the source prescribed Smells' thresholds.

## Normalized report shape

Full evidence does not mean duplicating everything in every finding. Extend the
schema-7 direction already recorded in Decision 21:

```text
analysis_scope      captured inputs, ownership, exclusions, budgets
source_manifest     normalized source identities and digests
concept_catalog     normalized concepts and guidance references
evidence            normalized reusable observations and graph edges
measurements        compact detector evaluations, matching and nonmatching
findings            matched results referencing evidence/measurement IDs
coverage            concept and detector completion states
guidance            normalized detector review guidance
```

Stable IDs let several findings reference the same class-shape, call-edge, or
history observation without copying it. Human logs resolve those references and
render the complete argument for one finding. JSON retains the normalized form.

## Evidence required for the first new detector work

### Global Data

Required evidence:

- declaration, owner, visibility, and mutability;
- initialization-only versus runtime writes;
- every resolved reader and writer location;
- distinct functions, modules, packages, and implementations involved;
- write count, writer count, reader count, and cross-owner reach;
- aliases or indirect access that were resolved;
- unresolved references and resolution coverage;
- exclusions for constants, generated code, test fixtures, and explicit
  configuration entry points.

The finding should render an ownership/read-write graph. A global declaration
alone is not enough.

### God Class / Blob

Required evidence:

- class/type size and complexity measurements;
- method-to-field access matrix and cohesion measurement;
- foreign-data access grouped by provider;
- incoming/outgoing dependency centrality;
- responsibility or method clusters;
- associated Data Class/provider evidence when used;
- counterexample checks for façades, orchestrators, registries, generated
  clients, and intentionally centralized adapters.

Large Class may be supporting evidence, but cannot prove God Class by itself.

### Brain Method

Required evidence:

- authored lines or statements;
- cyclomatic complexity;
- maximum nesting depth;
- local-variable and data-access counts;
- control-flow regions and exits;
- per-clause composite result;
- generated/test exclusions and parser completeness.

Long Method may be supporting evidence, but cannot prove Brain Method by
itself.

### Brain Class

Required evidence:

- referenced Brain Method findings or measurements;
- class-level complexity and cohesion;
- concentration of complex behavior among methods;
- method/field responsibility clusters;
- distribution of behavior across the class;
- the same intentional-centralization counterexamples as God Class.

Brain Class work starts only after the Brain Method contract is stable enough
to reuse.

### Intensive Coupling

Required evidence:

- subject callable;
- resolved outgoing calls and call-site locations;
- total calls and distinct operations;
- provider identities and calls per provider;
- concentration calculation;
- dynamic/unresolved call counts;
- exclusions for generated forwarding and framework glue.

The rendered finding must show that call intensity is concentrated in a small
number of providers.

### Dispersed Coupling

Required evidence:

- the same resolved call set as Intensive Coupling;
- number of distinct providers;
- calls per provider and dispersion calculation;
- package/implementation spread;
- unresolved provider count;
- exclusions for intentional composition roots and adapters.

The rendered finding must show that substantial call activity is spread across
many providers.

## Work plan

### Phase 1 — organize without behavioral change

1. Add `rules/sources-v1.json` and its schema.
2. Add `rules/concept-catalog-v1.json` and its schema.
3. Populate the current 23 supported concepts and the 13 researched additions,
   with concept acceptance and detector support represented independently.
4. Keep the current policy IDs, names, and categories; attach attributed
   aliases plus exact HTTPS source references and locators to those identities.
5. Amend Decision 20 so embedded guidance is independently authored and
   source-cited rather than copied verbatim.
6. Preserve the current v1 regression coverage for group membership, rule
   results, report schema, findings, and exit codes; treat the implementation
   digest as release identity rather than a byte-compatibility promise.

Acceptance: detector execution, policy selection, report schema, findings, and
exit codes remain unchanged and offline. The release and implementation digest
may change because the packaged artifact has changed. Invalid embedded catalog
metadata fails closed before scanning; the catalog does not otherwise become a
detector input.

### Phase 2 — explicit evidence contracts

1. Define the common evidence envelope and normalized identifiers.
2. Inventory every current detector against required fields.
3. Fill evidence gaps in existing detectors before adding composites.
4. Make taxonomy memberships and execution groups independent.
5. Validate that incomplete evidence cannot become a passing result.

Acceptance: every current finding answers the six questions in the Decision
summary, and every nonmatch states its measured scope.

### Phase 3 — reusable repository evidence

1. Add mutable-state ownership/read/write facts.
2. Add method-to-field access and cohesion facts.
3. Add nesting and class/method complexity facts.
4. Add conservative resolved call/provider facts.
5. Record unresolved edges and analysis budgets as first-class coverage facts.

Acceptance: evidence primitives are deterministic, reusable across detectors,
and tested through the analysis-module interface.

### Phase 4 — preview detectors

Implement as report-only, explicitly selected preview rules in this order:

1. Global Data
2. God Class / Blob
3. Brain Method
4. Brain Class
5. Intensive Coupling
6. Dispersed Coupling

Each detector needs positive, boundary, counterexample, incomplete-analysis,
budget, determinism, and real-repository false-positive fixtures.

Acceptance: no detector becomes stable until its evidence creates a distinct,
actionable design decision beyond an existing rule.

### Phase 5 — catalog and release lifecycle

1. Add scheduled source monitoring that opens review work only.
2. Publish reviewed manifest/catalog changes in versioned releases.
3. Add an explicit signed `smells update --check` path later.
4. Keep normal scans offline and report `freshness_unknown` on update-check
   network or verification failure.
5. Publish new `*-v2` packs only when stable concept coverage or default
   behavior changes.

## Release boundary

- Metadata organization and evidence-envelope work can ship in scanner `0.5.x`
  while retaining `python-v1`, `rust-v1`, and `typescript-v1` behavior.
- Preview detectors may be added only as explicit opt-ins and report-only.
- A changed stable/default smell inventory requires `python-v2`, `rust-v2`,
  and `typescript-v2` rather than silently altering v1.

## Definition of done for one smell check

A detector is complete only when:

- its canonical concept and source relationships are registered;
- applicability is explicit per language;
- its evidence population and observations are precisely defined;
- all match clauses and thresholds are exposed;
- the report includes primary and related locations;
- exclusions and counterexamples are documented and tested;
- unresolved evidence and resource exhaustion fail closed;
- repeated identical inputs produce identical output;
- the finding contains enough evidence for a reviewer to reproduce the match;
- guidance states that the match is evidence to investigate, not proof of a
  design defect.
