# Organizing the multi-source smell catalog and rule packs

Research date: 2026-09-27

## Recommendation

Smells should not merge Fowler, Refactoring.Guru, Mäntylä,
Lanza–Marinescu, and DECOR into one flat list of warnings. They play different
roles:

- Fowler and Refactoring.Guru name and explain conceptual smells.
- Mäntylä and Lassenius contribute the current six-category taxonomy, Dead
  Code, and evidence about the subjectivity of smell evaluation.
- Lanza and Marinescu contribute metric-oriented design disharmonies.
- DECOR contributes a method for composing lower-level evidence into
  higher-level design-smell detectors.

Organize the project as six linked layers:

```text
source observation
       ↓ reviewed interpretation
canonical concept ← aliases and source-specific taxonomies
       ↓ implemented by
language detector ← evidence primitives and composition contract
       ↓ selected by
versioned rule pack ← explicit execution groups
       ↓ configured by
project policy → finding/report
```

The first release should add this metadata without changing any v1 scan.
New concepts should enter as research records; new executable checks should
enter as opt-in preview rules; stable coverage changes should eventually use
new `*-v2` packs.

Fowler supports this separation: a smell is an indication that may point to a
deeper problem, not proof of a defect, and the second-edition chapter explicitly
declines to give universal precise criteria
([Fowler, “Code Smell”](https://martinfowler.com/bliki/CodeSmell.html),
[Fowler and Beck, second-edition chapter](https://www.informit.com/articles/article.aspx?p=2952392)).

## Current repository constraints

The current design is deliberately closed and deterministic, but it couples
concepts, sources, categories, execution, and guidance too tightly.

### One source stands for the whole catalog

Every language pack contains one `catalog` object with only `source`,
`checked_on`, and `item_count`. Runtime validation requires the exact
Refactoring.Guru URL, the date `2026-09-19`, and count 23
([Python pack](../rules/python-v1.json),
[Rust pack](../rules/rust-v1.json),
[TypeScript pack](../rules/typescript-v1.json),
[`validate_catalog`](../src/policy.rs)).

This cannot represent multiple sources, editions, aliases, components, or a
source change awaiting review.

### The canonical 23 are hardcoded

`canonical_smells()` hardcodes every ID, name, and Refactoring.Guru category.
`validate_smell_catalog()` requires every pack to equal that set exactly
([current validation](../src/policy.rs)). Adding provenance or a research-state
concept currently looks like a runtime catalog break.

### Concepts are copied into three packs

The same 23 concept records appear in all language packs. Applicability and
detectors legitimately vary by language; canonical names and provenance should
not.

### A taxonomy category becomes a policy selector

`group_definitions()` creates execution groups from `smell.category`. That is
safe with one taxonomy, but not with Refactoring.Guru's five groups,
Mäntylä's taxonomy, Lanza–Marinescu's Identity/Collaboration/Classification
organization, and DECOR's composite vocabulary
([Refactoring.Guru catalog](https://refactoring.guru/refactoring/smells),
[Lanza–Marinescu contents](https://link.springer.com/book/10.1007/3-540-39538-5)).

Browsing “Identity Disharmonies” is conceptual. Selecting `--group evidence`
changes execution. Those must be separate namespaces.

### Guidance is rule-level and Refactoring.Guru-only

Guidance repeats the concept name/category for every rule and requires one URL
derived from the Refactoring.Guru smell ID
([guidance validation](../src/report/guidance.rs)). It cannot cleanly express
plural provenance or distinguish conceptual review guidance from the exact
language detector contract.

## Source roles

| Source | Role in Smells | Do not do |
| --- | --- | --- |
| Fowler 1e/2e | Concept lineage, current names, review direction | Do not convert prose into universal thresholds. |
| Refactoring.Guru | Accessible catalog and current v1 presentation | Do not keep it as the sole provenance source. |
| Mäntylä–Lassenius 2006 | Current Mäntylä taxonomy, Dead Code provenance, and empirical evidence about subjective evaluation | Do not turn its categories or metrics into universal detector truth. |
| Lanza–Marinescu | Composite metric hypotheses | Do not transplant OO thresholds unchanged across languages. |
| DECOR/DETEX | Evidence-composition and validation method | Do not flatten evidence terms into standalone warnings. |

DECOR explicitly describes a unified vocabulary and detection method, with
separate examples for Blob, Functional Decomposition, Spaghetti Code, and
Swiss Army Knife. This supports storing evidence inputs separately from the
composite verdict
([Ptidej DECOR overview](https://www.ptidej.net/research/designsmells)).

Ruff, ESLint, rustc, and Clippy are organizational precedents only. They remain
companion tools, not smell sources.

## Recommended model

### 1. Source manifest

A source record identifies a work or edition, not a smell. Use immutable IDs:

- `fowler-beck-refactoring-1e`
- `fowler-refactoring-2e`
- `refactoring-guru-smells-web`
- `mantyla-lassenius-2006`
- `lanza-marinescu-2006`
- `decor-2010`

Minimum fields:

- ID, title, authors, publisher, and kind;
- edition, publication date, ISBN, or DOI where applicable;
- canonical URL and, when available, one direct `full_text_url`;
- date last reviewed by maintainers;
- reviewed revision/fingerprint;
- monitoring mode: `stable_identifier`, `web_content`, or `manual`;
- reuse boundary such as `cite_and_paraphrase`.

Prefer an author, publisher, DOI, or institutional repository URL as the
canonical public reference. A locally supplied or third-party PDF may verify
printed and physical PDF page numbers during review, but it is not promoted to
canonical provenance unless its publication authority and reuse status are
clear. Record both page systems in `source_locator` when they differ.

For books and papers, edition plus DOI/ISBN is the revision. A new edition is a
new source record. For a live page, hash a normalized narrow observation such
as catalog names and canonical links, not navigation, advertisements, or page
chrome.

For manifest v1, `catalog_fingerprint` is the lowercase SHA-256 of the source's
reference rows sorted by ordinal. Each UTF-8 row is
`ordinal<TAB>source_term<TAB>source_category-or-empty<TAB>relationship<TAB>reference_url<TAB>source_locator<TAB>access_scope<TAB>reference_role<LF>`.
This pins the exact embedded source slice—including page evidence—without
hashing unrelated page chrome.

### 2. Canonical concepts

A concept is Smells' stable identity for a user-facing design idea. Its ID
survives source terminology changes. Accepted entries intentionally retain only
the current v1 policy `id`; their display name and category remain owned by the
language packs. For example, `long-method` remains displayed as Long Method by
policy while Long Function is an attributed alias from Fowler's second
edition. This catalog augments the existing policies instead of copying or
redefining them.

Each concept needs:

- immutable policy-compatible `id`; candidates also carry a proposed `name`,
  while accepted names and categories remain in the existing packs;
- Smells-authored summary and review questions;
- lifecycle state and introduction/stabilization versions;
- attributed aliases;
- plural typed source claims, each with an explicit HTTPS `reference_url` and
  a human-readable `source_locator`;
- source-specific taxonomy memberships;
- relationships to other concepts;
- structured replacement/tombstone information.

Use a small relationship vocabulary:

- `defines`
- `names`
- `legacy_alias`
- `refines`
- `equates`
- `classifies`
- `component`
- `motivates`

This prevents false equivalence. Blob can be an attributed equivalent of God
Class, while Brain Method remains distinct from Long Method.

### 3. Taxonomies

A taxonomy is a browse-only view over concepts. A concept may belong to several
taxonomies, and node IDs are scoped by taxonomy.

Taxonomy membership must never create an execution group automatically.
The v1 names `bloaters`, `couplers`, and so on may remain explicit compatibility
groups, but adding another source taxonomy must not alter rule selection.

### 4. Evidence primitives

Evidence is an observation, not a smell verdict. Examples include function
line count, complexity, nesting, method-to-field access sets, outgoing provider
counts, mutable-state readers/writers, and Git co-change owners.

Evidence semantics are language-specific. Use IDs such as
`python.class.method_field_cohesion`, with:

- version, scope, population, and unit;
- collector/input requirements;
- resolution and exclusion semantics;
- incomplete-analysis behavior;
- deterministic resource limits;
- contract link.

One primitive may feed several detectors without producing a user-facing smell
on its own.

### 5. Detectors

A detector belongs to one concept and one language pack. Preserve the current
`rule_id` and integer `rule_version`, then add:

- lifecycle;
- evidence dependencies;
- direct versus compiled-composite implementation;
- parameter schema and defaults;
- certainty and exact claim;
- known false-positive/false-negative limitations;
- structured applicability;
- introduction, stabilization, deprecation, and replacement versions.

For a composite, metadata lists inputs while compiled Rust and a versioned
contract remain authoritative. Smells does not need a second rules DSL.

### 6. Applicability and implementation support

Do not combine “makes sense for this language” with “implemented.”

Applicability states:

- `applicable`
- `contextual`
- `not_applicable`
- `unsupported`

Detector support states:

- `none`
- `research`
- `preview`
- `stable`
- `deprecated`

This preserves the distinction between Rust inheritance inapplicability and a
valid concept whose Rust detector simply has not been built.

### 7. Explicit execution groups and policy

Rule packs declare execution groups directly: `all`, `source`, `evidence`, and
any compatibility groups. Policy continues to control `off`, `report`, and
`required` independently of concept or detector maturity.

This matches the strongest shared precedent from Ruff, ESLint, rustc, and
Clippy: identity, purpose/category, lifecycle, applicability, default selection,
and project enforcement are distinct metadata axes
([Ruff rules](https://docs.astral.sh/ruff/rules/),
[ESLint rule metadata](https://eslint.org/docs/latest/extend/custom-rules#rule-structure),
[rustc lint groups](https://doc.rust-lang.org/rustc/lints/groups.html),
[Clippy categories](https://doc.rust-lang.org/clippy/lints.html)).

### 8. Split guidance

Store conceptual guidance once in the catalog:

- what the smell means;
- why it may matter;
- review questions;
- aliases and source claims.

Keep detector guidance in the pack:

- exact signal and match condition;
- threshold and scope;
- limitations and exclusions;
- evidence to inspect;
- remediation direction;
- detector contract.

A finding references both records by ID/digest. The existing v1
`reference_url` remains during migration.

## Minimal file layout

Avoid a large file split. Add two sources of truth and keep existing packs in
place:

```text
rules/
  sources-v1.json
  concept-catalog-v1.json
  rust-v1.json
  python-v1.json
  typescript-v1.json
  rust-v1-guidance.json
  portable-v1-guidance.json
schemas/
  source-manifest.schema.json
  concept-catalog.schema.json
  rule-pack-v2.schema.json       # only when pack shape changes
```

One catalog file is sufficient at this scale. Split per concept only if real
merge contention or generation cost appears.

## MVP schema example

The two new files can start with this minimal shape:

```json
{
  "schema_version": 1,
  "manifest_id": "smells-sources",
  "manifest_version": "1.1.0",
  "reviewed_on": "2026-09-27",
  "sources": [
    {
      "source_id": "fowler-refactoring-2e",
      "kind": "book_edition",
      "title": "Refactoring: Improving the Design of Existing Code",
      "authors": ["Martin Fowler"],
      "contributors": ["Kent Beck (Chapter 3 coauthor)"],
      "publisher": "Addison-Wesley Professional",
      "publication_year": 2018,
      "edition": "2",
      "isbn": "9780134757599",
      "canonical_url": "https://www.informit.com/articles/article.aspx?p=2952392",
      "edition_url": "https://martinfowler.com/books/refactoring.html",
      "reviewed_on": "2026-09-27",
      "revision": "edition:2",
      "catalog_fingerprint": "sha256:<normalized-source-view-sha256>",
      "monitoring": "stable_identifier",
      "reuse": "cite_and_paraphrase"
    }
  ]
}
```

```json
{
  "schema_version": 1,
  "catalog_id": "smells-core",
  "catalog_version": "1.1.0",
  "source_manifest": "smells-sources@1.1.0",
  "item_count": 36,
  "concepts": [
    {
      "id": "long-method",
      "catalog_state": "accepted",
      "summary": "<Smells-authored summary>",
      "aliases": [
        {
          "name": "Long Function",
          "source_ids": ["fowler-refactoring-2e"]
        }
      ],
      "references": [
        {
          "source_id": "fowler-refactoring-2e",
          "source_term": "Long Function",
          "relationship": "refines",
          "reference_url": "https://www.informit.com/articles/article.aspx?p=2952392&seqNum=3",
          "source_locator": "Chapter 3 — Long Function; printed p. 73; PDF p. 95; InformIT page 3 of 24",
          "ordinal": 3,
          "access_scope": "full_text",
          "reference_role": "concept_guidance"
        }
      ],
      "language_support": {
        "rust": {"applicability": "applicable", "detector_support": "stable"},
        "python": {"applicability": "applicable", "detector_support": "stable"},
        "typescript": {"applicability": "applicable", "detector_support": "stable"}
      }
    }
  ]
}
```

The catalog stores each source's term and category on the reference record.
Aliases only represent alternate names; they list every reference source that
uses that exact term. The policy name is not duplicated as an alias—its
supporting sources are still present in `references`.

The eventual schema-2 language pack references the catalog instead of copying
concepts and adds `concept_support`, `evidence_primitives`, explicit
`selection_groups`, and detector lifecycle metadata. Canonical JSON hashing
must be specified so whitespace changes do not create false drift.

## Lifecycle and versioning

Use separate state machines for concepts and detectors:

```text
concept:  candidate → accepted → deprecated → retired
detector: none → research → preview → stable → deprecated
```

- `candidate`: researched concept, not an active policy smell.
- `accepted`: canonical concept identity used by an existing policy.
- `research`: documented detector hypothesis; not selectable.
- `preview`: explicitly opt-in and report-only by default.
- `stable`: supported behavior contract.
- `deprecated`: still resolvable and names a replacement or states none.
- `removed`: does not execute, but keeps documentation and migration history.

Ruff exposes stable/preview/deprecated/removed status and requires new rules to
spend time in preview. ESLint records replacement, deprecation version, and
availability horizon. Clippy keeps deprecated compatibility entries
([Ruff versioning](https://docs.astral.sh/ruff/versioning/),
[ESLint deprecation metadata](https://eslint.org/docs/latest/extend/rule-deprecation),
[Clippy deprecated lints](https://doc.rust-lang.org/clippy/lints.html#deprecated)).

Keep independent versions for scanner, schema, source manifest, concept
catalog, pack, rule, and project policy. Increment `rule_version` whenever the
detector's observable contract changes, even if the concept does not.

Compatibility guidance:

- provenance or wording only: catalog/pack patch;
- additive opt-in preview detector: pack minor;
- stable detector bug fix: release patch and rule-version increment when output
  changes;
- substantial stable scope/intent change: pack minor plus migration;
- changed stable concept inventory/default execution: new `*-v2` line.

## Freshness and update checking

Keep two states separate:

1. `source_review_pending`: monitoring observed an upstream difference, but no
   reviewed pack is outdated yet;
2. `update_available`: maintainers reviewed the change and published a newer
   pack.

A scheduled release workflow should fingerprint monitored live sources and
open an issue or draft PR on change. It must never update detectors or guidance
automatically. Stable books/papers are checked for new editions, not repeatedly
copied or hashed wholesale.

Every release embeds scanner, pack, catalog, and source-manifest versions and
digests. An explicit `smells update --check` may fetch a small signed/attested
release index. Normal scans remain offline. If verification or networking
fails, report `freshness_unknown`, never “current.”

GitHub recommends signing manifests containing detailed asset hashes; its
artifact attestations bind artifacts to workflow and repository provenance
([GitHub artifact attestations](https://docs.github.com/en/actions/concepts/security/artifact-attestations)).

## Browsing

Generate CLI and static documentation from the same metadata. Proposed
read-only views:

```text
smells catalog list
smells catalog show god-class
smells catalog list --source decor-2010 --lifecycle preview
smells catalog taxonomies
smells sources show fowler-refactoring-2e
smells rules list --language python --lifecycle preview
smells rules show python.god_class
smells update --check --format json
```

Concept pages show aliases, sources, taxonomies, lifecycle, per-language
support, detectors, and review guidance. Rule pages show exact evidence,
parameters, limitations, groups, and contracts. Label policy groups as
**Execution groups**, separately from taxonomy filters.

Ruff's generated browser is a useful precedent because users can filter
category, origin, and lifecycle independently while deprecated/removed pages
remain discoverable
([Ruff rule browser](https://docs.astral.sh/ruff/rules/)).

## Decision 20 copyright conflict

Decision 20 currently says Refactoring.Guru text is quoted verbatim when a page
publishes an explicit section. That conflicts with the safer multi-source model
and with Refactoring.Guru's published usage policy. The site permits limited
linked quotation but does not grant permission to embed substantial page
content as a reusable rule-pack corpus
([Refactoring.Guru content policy](https://refactoring.guru/content-usage-policy)).

Amend that part of Decision 20 before expanding sources:

> Guidance is independently authored by Smells and cites the applicable source
> records. Short quotations may be used only when allowed and necessary;
> substantial prose, tables, illustrations, and detector grammars are not
> embedded. Scans remain offline because citations, source identities, and
> Smells-authored guidance are packaged with the release.

This keeps the offline guarantee while removing the need to copy copyrighted
material.

## Low-risk migration

### Phase 0 — freeze v1 behavior

Fixture the three registries, 23 concept results, 28 rules, group memberships,
policy validation, output ordering, exit codes, and report bytes.

### Phase 1 — add metadata only

Add and schema-validate `sources-v1.json` and `concept-catalog-v1.json`.
Populate the current 23 as accepted concepts with stable detector support where
the existing language pack has a rule. Add the 13 researched concepts as
candidates with detector support `none`. Validate the embedded metadata
fail-closed when a v1 registry is loaded, but do not use it as detector input
or alter rule selection. Record candidate applicability independently: an
applicable or contextual concept can still have no detector.

### Phase 2 — validate against the catalog

Replace hardcoded `canonical_smells()` data with the embedded catalog while
asserting that every v1 pack resolves to the exact same 23 IDs, names, and
legacy categories. Keep pack JSON, report schema, findings, and exit behavior
unchanged; artifact identity digests may change with the release contents.

### Phase 3 — make execution groups explicit

Materialize current `all`, `source`, `evidence`, and five category groups with
identical memberships. Change group resolution to read those definitions. Add
a regression test proving taxonomy edits cannot change selected rules.

### Phase 4 — introduce pack schema 2

New packs reference the shared catalog and contain only language support,
evidence primitives, detectors, and groups. Continue parsing schema-1 v1 packs.

### Phase 5 — split guidance

Move conceptual guidance to the catalog and retain detector guidance in packs.
Preserve v1 `reference_url`; expose plural provenance only through a versioned
report schema.

### Phase 6 — source monitoring

Add the scheduled review workflow and attested release index, then the explicit
update-check command. Never add network access to normal scans.

### Phase 7 — preview detectors

Prototype, in order:

1. Global Data
2. God Class / Blob
3. Brain Method and Brain Class
4. Intensive and Dispersed Coupling

Require curated positive/negative fixtures, real-repository false-positive
review, deterministic output, incomplete-analysis behavior, and resource
budgets before preview can become stable.

### Phase 8 — new stable pack line

Publish `rust-v2`, `python-v2`, and `typescript-v2` only when stable concept
coverage or required policy inventory differs from v1. A scanner release may
support both generations simultaneously.

## Required invariants

Validation must reject:

- unknown or unreviewed source references;
- aliases that resolve to multiple concepts;
- unknown taxonomy nodes;
- detector references to unknown concepts/evidence;
- stable detectors without applicability and a contract;
- composites that omit evidence dependencies;
- groups containing unknown rules;
- taxonomy changes that alter policy selection;
- manifest/catalog/pack digest mismatches;
- removed IDs without tombstones and migration records;
- preview rules entering stable defaults accidentally.

## Next-release MVP

The next release should organize before adding active smells:

1. Add the source manifest and concept catalog.
2. Amend Decision 20's verbatim-copy requirement.
3. Preserve every v1 detector and output.
4. Generate a read-only catalog view from the new metadata.
5. Make existing execution groups explicit and compatibility-tested.
6. Add source monitoring, but defer installed update checking until the signed
   release-index contract is finalized.

Metadata-only organization can remain on the `*-v1` compatibility line. New
stable default coverage requires `*-v2`.

## Primary sources

- Fowler, [Code Smell](https://martinfowler.com/bliki/CodeSmell.html).
- Fowler and Beck,
  [second-edition smell chapter](https://www.informit.com/articles/article.aspx?p=2952392).
- Refactoring.Guru, [Code Smells](https://refactoring.guru/refactoring/smells)
  and [Content Usage Policy](https://refactoring.guru/content-usage-policy).
- Mäntylä and Lassenius,
  [Springer publication record](https://link.springer.com/article/10.1007/s10664-006-9002-8)
  and [author-hosted full text](https://mmantyla.github.io/ESE_2006.pdf),
  DOI `10.1007/s10664-006-9002-8`.
- Lanza and Marinescu,
  [*Object-Oriented Metrics in Practice*](https://link.springer.com/book/10.1007/3-540-39538-5).
- Ptidej Team, [DECOR/DETEX](https://www.ptidej.net/research/designsmells),
  DOI `10.1109/TSE.2009.50`.
- Ruff, [rules](https://docs.astral.sh/ruff/rules/),
  [preview](https://docs.astral.sh/ruff/preview/), and
  [versioning](https://docs.astral.sh/ruff/versioning/).
- ESLint, [rule metadata](https://eslint.org/docs/latest/extend/custom-rules#rule-structure),
  [deprecation metadata](https://eslint.org/docs/latest/extend/rule-deprecation), and
  [releases](https://eslint.org/docs/latest/maintain/manage-releases).
- rustc, [lint levels](https://doc.rust-lang.org/rustc/lints/levels.html) and
  [groups](https://doc.rust-lang.org/rustc/lints/groups.html).
- Clippy, [lint categories](https://doc.rust-lang.org/clippy/lints.html) and
  [lint development](https://doc.rust-lang.org/clippy/development/adding_lints.html).
- GitHub, [artifact attestations](https://docs.github.com/en/actions/concepts/security/artifact-attestations).

## Final decision

Use one canonical multi-source concept catalog, source-specific browse
taxonomies, language-specific evidence and detector contracts, and versioned
packs whose execution groups and lifecycle are independent of how literature
classifies a smell.
