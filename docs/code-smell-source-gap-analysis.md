# Code-smell source inventory and gap analysis

Research date: 2026-09-27

## Executive conclusion

The current Smells catalog is not missing a second large catalog hidden inside
Mäntylä or Refactoring.Guru. The same lineage appears three times:

1. Fowler and Beck's first edition of *Refactoring* defined 22 smells.
2. Mäntylä and Lassenius's complete 2006 taxonomy groups those smells into
   five named categories, retains two ungrouped smells, and adds **Dead Code**.
3. Refactoring.Guru exposes those 23 concepts with a simplified five-category
   presentation. Those are the exact 23 concepts pinned by every current Smells
   v1 pack.

The genuinely new material is narrower and more useful than simply importing
every term as a new rule:

- Fowler's second edition adds four concepts absent from Smells:
  **Mysterious Name, Global Data, Mutable Data, and Loops**. It also updates
  four older concepts and drops two from its chapter.
- Lanza and Marinescu contribute 11 metric-oriented object-design
  disharmonies. Six are meaningfully new or stronger than the current catalog:
  **God Class, Brain Method, Brain Class, Intensive Coupling, Dispersed
  Coupling, and Tradition Breaker**. Their Significant Duplication is better
  treated as a stronger duplicate-code detector contract.
- DECOR contributes four higher-level composite design smells:
  **Blob/God Class, Functional Decomposition, Spaghetti Code, and Swiss Army
  Knife**. Its lower-level terms are ingredients of those composites, not a
  flat list that should become 15 independent rules.

The strongest candidates for deterministic, repository-wide work are
**Global Data**, **God Class/Blob**, **Brain Method/Brain Class**,
**Intensive/Dispersed Coupling**, and a broader **Lazy Element** contract.
Functional Decomposition, Spaghetti Code, Swiss Army Knife, Mutable Data, and
Tradition Breaker deserve prototypes before catalog admission. A generic
"Loops" rule and a fully automatic "Mysterious Name" rule are poor initial
fits: the former is largely a local style preference and the latter depends on
domain meaning.

Ruff, Pylint, Clippy, ESLint, PMD/CPD, and similar linters are deliberately
outside this source inventory. They remain companion tools and possible
implementation cross-checks; their lint catalogs are not upstream smell
catalogs for Smells.

## Scope and method

This report compares five requested source families against the exact catalog
and executable contracts in:

- [`python-v1`](../rules/python-v1.json)
- [`rust-v1`](../rules/rust-v1.json)
- [`typescript-v1`](../rules/typescript-v1.json)

All three pack files contain the same 23 concept IDs. Python and TypeScript map
all 23 to executable rules. Rust retains all 23 in its catalog, but correctly
marks `refused-bequest` and `parallel-inheritance-hierarchies` as
`not_applicable_native_rust`.

The comparison uses these labels:

| Label | Meaning |
| --- | --- |
| **Exact** | The current catalog contains the same conceptual smell. This does not claim that the local detector perfectly recognizes every real instance. |
| **Partial** | Smells contains a narrower, broader, renamed, or component-level signal, but not the full source concept. |
| **Absent** | Smells has neither the conceptual entry nor a detector that represents it. |
| **Not applicable** | The source concept depends on a language feature that the target language does not have natively. |

Catalog coverage and detector fidelity are kept separate. Fowler explicitly
declines to prescribe universal cutoffs and describes smells as indications for
human investigation rather than proofs. The current Smells rules are local,
versioned operationalizations, not executable rules copied from Fowler or
Refactoring.Guru
([Fowler and Beck's second-edition chapter](https://www.informit.com/articles/article.aspx?p=2952392),
[Fowler's definition](https://martinfowler.com/bliki/CodeSmell.html)).

## Current Smells baseline

The v1 packs pin this exact Refactoring.Guru catalog:

| Category | Current concept IDs |
| --- | --- |
| Bloaters | `long-method`, `large-class`, `primitive-obsession`, `long-parameter-list`, `data-clumps` |
| Object-orientation abusers | `alternative-classes-with-different-interfaces`, `refused-bequest`, `switch-statements`, `temporary-field` |
| Change preventers | `divergent-change`, `parallel-inheritance-hierarchies`, `shotgun-surgery` |
| Dispensables | `comments`, `duplicate-code`, `data-class`, `dead-code`, `lazy-class`, `speculative-generality` |
| Couplers | `feature-envy`, `inappropriate-intimacy`, `incomplete-library-class`, `message-chains`, `middle-man` |

The source registry records Refactoring.Guru as the single catalog origin. The
site's live catalog lists the same 23 names
([Refactoring.Guru catalog](https://refactoring.guru/refactoring/smells)).

## Source 1: Fowler and Beck, *Refactoring*

### Edition relationship

The publisher's first-edition sample contains the complete 22-name Chapter 3
table of contents
([first-edition sample](https://ptgmedia.pearsoncmg.com/images/9780201485677/samplepages/0201485672.pdf)).
The publisher also makes the complete second-edition smell chapter available as
24 linked pages
([second-edition chapter](https://www.informit.com/articles/article.aspx?p=2952392)).

The following crosswalk is a comparison of those two primary sources. The four
edition mappings are practical equivalences, not a formal alias table published
by Fowler.

| Fowler 1e term | Fowler 2e term | Current Smells coverage | Detector/evidence concept in Smells | Assessment |
| --- | --- | --- | --- | --- |
| Duplicated Code | Duplicated Code | **Exact**: `duplicate-code` | Similar normalized callable bodies; narrower than all forms of duplication | Keep concept; strengthen block/file-level evidence separately. |
| Long Method | Long Function | **Exact concept**, renamed | Body line count plus CRAP/complexity proxy | Treat `long-method` and Long Function as aliases; a pack migration may rename the display label without adding a peer smell. |
| Large Class | Large Class | **Exact**: `large-class` | Field, method, and summed method-line budgets | Keep; do not equate raw size with God Class. |
| Long Parameter List | Long Parameter List | **Exact** | Declared parameter count | Keep, with language-specific counting and thresholds. |
| Divergent Change | Divergent Change | **Exact** | Bounded Git-history change/responsibility evidence | Repository-wide historical signal is appropriate. |
| Shotgun Surgery | Shotgun Surgery | **Exact concept** | Bounded Git-history spread of a change across responsibilities | Keep; distinguish this historical contract from Lanza/Marinescu's static incoming-coupling strategy. |
| Feature Envy | Feature Envy | **Exact concept** | Foreign member-access count/share | Improve with resolved owner and data-provider evidence. |
| Data Clumps | Data Clumps | **Exact** | Repeated parameter name/type groups across callables | Strong repository-wide fit. |
| Primitive Obsession | Primitive Obsession | **Exact concept** | Primitive field concentration and domain-role naming | Keep as an indicator, not a claim that primitives are always wrong. |
| Switch Statements | Repeated Switches | **Partial edition update** | Repeated dispatch sites sharing case/arm structure | The existing detector is already closer to 2e: Fowler now focuses on repeated conditional structures, not every switch ([2e Repeated Switches](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=12)). Rename/alias; do not add a duplicate rule. |
| Parallel Inheritance Hierarchies | — | **Exact current legacy concept** | Parallel authored inheritance-family shapes; not applicable to native Rust | Fowler 2e omits it, but omission alone is not a reason to delete a still-useful, versioned legacy smell. |
| Lazy Class | Lazy Element | **Partial** | Current rule only evaluates class/type size and behavior | Broaden the existing concept to functions/modules before adopting the 2e name ([2e Lazy Element](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=14)). |
| Speculative Generality | Speculative Generality | **Exact concept** | Currently unused generic/type parameters | Current detector covers only one form; unused extension seams and unused abstractions remain outside scope. |
| Temporary Field | Temporary Field | **Exact concept** | Optional/nullable fields used in only a small share of methods | Keep; life-cycle context remains important. |
| Message Chains | Message Chains | **Exact concept** | Long dotted navigation expressions | Resolve domain-owner chains to avoid flagging fluent APIs. |
| Middle Man | Middle Man | **Exact concept** | High share of pure forwarding methods | Strong deterministic signal with adapter/proxy exceptions. |
| Inappropriate Intimacy | Insider Trading | **Partial edition update** | Private/forbidden cross-boundary access or project dependency contract | 2e broadens the discussion to modules and inheritance relationships; use an alias plus stronger module-boundary evidence, not a new peer smell ([2e Insider Trading](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=19)). |
| Alternative Classes with Different Interfaces | Same | **Exact concept** | Similar callable bodies combined with different method-name sets; port conformance | Keep; behavior/substitutability evidence is stronger than lexical similarity alone. |
| Incomplete Library Class | — | **Exact current legacy concept** | Workarounds such as prototype/foreign mutation or extension traits | Retain as a legacy concept if validated; Fowler 2e no longer lists it. |
| Data Class | Data Class | **Exact concept** | Fields with no non-accessor operations | Keep contextual exceptions for records, DTOs, messages, schemas, and configuration. |
| Refused Bequest | Refused Bequest | **Exact for Python/TypeScript; not applicable to native Rust** | Same-corpus inherited members explicitly rejected by subclasses | Keep language applicability explicit. |
| Comments | Comments | **Exact concept** | High in-body comment share | A ratio is only a review signal; documentation is not inherently a problem. |
| — | Mysterious Name | **Absent** | No domain-semantic naming model | New 2e concept. Suitable first for a review/AI evidence surface, not a deterministic blocking rule ([2e Mysterious Name](https://www.informit.com/articles/article.aspx?p=2952392)). |
| — | Global Data | **Absent** | No cross-file mutable-global ownership/read/write graph | New 2e concept and a strong repository-wide detector candidate ([2e Global Data](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=5)). |
| — | Mutable Data | **Absent** | No general mutation-scope, aliasing, or derived-state analysis | New 2e concept. Prototype narrower sub-signals before adding a broad rule ([2e Mutable Data](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=6)). |
| — | Loops | **Absent** | No loop-to-pipeline preference rule | New 2e concept, but a generic loop warning is local style and often language/domain dependent; keep out of the first expansion ([2e Loops](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=13)). |
| — | — | **Current-only `dead-code`** | Conservative unused private declaration evidence | Not a Fowler Chapter 3 smell in either edition. It enters this lineage through Mäntylä; "Remove Dead Code" is a 2e refactoring, not a listed smell. |

### What Fowler actually adds

The four absent concepts are real catalog gaps. They are not equally good
automatic rules:

- **Global Data** has an observable repository-wide core: mutable declarations
  with broad read/write reach and weak ownership boundaries.
- **Mutable Data** is much broader. A safe first contract should focus on
  mutation with large scope, multiple writers, aliasing, or duplicated derived
  state—not all assignment.
- **Mysterious Name** requires the tool to understand project language and
  intent. Deterministic lexical symptoms can supply evidence, but should not
  claim a semantic verdict.
- **Loops** expresses Fowler's preference for pipelines in languages with
  first-class functions. Treating every loop as a repository design problem
  would duplicate local style tooling and generate poor findings.

## Source 2: Refactoring.Guru

Refactoring.Guru is the exact current catalog source, not an independent source
of additional smells. Its 23 terms are:

| Refactoring.Guru category | Terms | Current coverage |
| --- | --- | --- |
| Bloaters | Long Method; Large Class; Primitive Obsession; Long Parameter List; Data Clumps | **Exact**, all five |
| Object-Orientation Abusers | Alternative Classes with Different Interfaces; Refused Bequest; Switch Statements; Temporary Field | **Exact**, all four |
| Change Preventers | Divergent Change; Parallel Inheritance Hierarchies; Shotgun Surgery | **Exact**, all three |
| Dispensables | Comments; Duplicate Code; Data Class; Dead Code; Lazy Class; Speculative Generality | **Exact**, all six |
| Couplers | Feature Envy; Inappropriate Intimacy; Incomplete Library Class; Message Chains; Middle Man | **Exact**, all five |

Consequences:

- There is no remaining Refactoring.Guru name to add to v1.
- Refactoring.Guru retains first-edition names and concepts rather than tracking
  Fowler 2e as an exact mirror.
- Its descriptions are conceptual guidance, not threshold specifications.
- Smells should record Refactoring.Guru as one conceptual reference, not the
  sole authority for every detector default.

Refactoring.Guru states that most site content is copyrighted. Its policy allows
limited quotation with a link to the relevant page and up to ten illustrations
across a publication. Rule packs should therefore store names, source URLs,
revision evidence, and independently authored summaries—not embedded copies of
whole pages or illustrations
([content usage policy](https://refactoring.guru/content-usage-policy)).

## Source 3: Mäntylä and Lassenius (2006)

Smells catalogs only the complete 2006 peer-reviewed paper for the Mäntylä
lineage. Section 3.5 introduces 23 smells: the 22 Fowler and Beck smells plus
Dead Code. The authors call its organization an improved version of their
earlier taxonomy and arrange the inventory into five named groups plus two
ungrouped smells. The 21-item website table is not a separate source because it
omits those two ungrouped smells
([Springer record and DOI](https://link.springer.com/article/10.1007/s10664-006-9002-8),
[author-hosted full text, §3.5](https://mmantyla.github.io/ESE_2006.pdf#page=13)).

| Mäntylä category | Source terms | Current Smells coverage | Contribution |
| --- | --- | --- | --- |
| Bloaters | Long Method; Large Class; Primitive Obsession; Long Parameter List; Data Clumps | **Exact**, all five | Groups size/growth-related Fowler smells. No new term. |
| Object-Orientation Abusers | Switch Statements; Temporary Field; Refused Bequest; Alternative Classes with Different Interfaces | **Exact**, all four | Groups incomplete or misused OO mechanisms. No new term. |
| Change Preventers | Divergent Change; Shotgun Surgery; Parallel Inheritance Hierarchies | **Exact**, all three | Relates one-class/many-change, one-change/many-class, and parallel-hierarchy problems. |
| Dispensables | Lazy Class; Data Class; Duplicate Code; **Dead Code**; Speculative Generality | **Exact**, all five | Dead Code is the substantive addition beyond Fowler 1e. |
| Couplers | Feature Envy; Inappropriate Intimacy; Message Chains; Middle Man | **Exact**, all four | Groups excessive coupling and delegation problems. |
| Other smells (ungrouped) | Comments; Incomplete Library Class | **Exact**, both | The paper retains both smells but states that they do not fit a named group. |

Mäntylä and Lassenius therefore contribute:

1. one current, complete 23-smell taxonomy view;
2. the Dead Code addition;
3. empirical evidence that human smell judgments are correlated and subjective;
4. a warning not to treat categories or thresholds as immutable truth.

It contributes **no remaining catalog gap** against the current 23.

## Source 4: Lanza and Marinescu, *Object-Oriented Metrics in Practice*

The book presents 11 object-oriented "design disharmonies" in three categories
and uses metric combinations and contextual thresholds to find candidates. The
publisher describes this as a pattern-oriented method for identifying identity,
collaboration, and classification problems—not a language-neutral lint catalog
([Springer book and DOI](https://link.springer.com/book/10.1007/3-540-39538-5)).

| Category and source term | Closest current concept | Catalog coverage | Detection concept from the source family | Recommended treatment |
| --- | --- | --- | --- | --- |
| Identity — God Class | Large Class | **Partial** | High class complexity plus foreign-data use and low cohesion | New composite detector/view. Do not alias every Large Class to God Class. |
| Identity — Feature Envy | Feature Envy | **Exact concept; partial detector fidelity** | Method accesses substantial foreign data, concentrates interest in few providers, and has low local-data access | Strengthen owner resolution and provider concentration under the existing concept. |
| Identity — Data Class | Data Class | **Exact concept; partial detector fidelity** | Data exposure/accessors dominate while functional behavior is scarce | Keep current concept; add richer visibility and client-use evidence. |
| Identity — Brain Method | Long Method | **Partial** | Method-level combination of size, complexity, nesting, and used variables | Add a composite review finding; do not make it an alias for every Long Method. |
| Identity — Brain Class | Large Class | **Partial** | A class accumulates excessive intelligence, often through one or more Brain Methods | Add only after Brain Method evidence exists; aggregate method signals with class cohesion/complexity. |
| Identity — Significant Duplication | Duplicate Code | **Partial** | Significant exact or modified clone chains rather than only one similar callable pair | Evolve duplicate-code coverage; avoid a second peer concept unless user-facing semantics differ materially. |
| Collaboration — Intensive Coupling | Inappropriate Intimacy/Feature Envy | **Absent as its own concept** | A method invokes many operations concentrated in a small number of provider classes | Strong call-graph detector candidate; distinct from private-member access. |
| Collaboration — Dispersed Coupling | Message Chains/Shotgun Surgery | **Absent as its own concept** | A method invokes many operations spread across many provider classes | Strong repository call-graph detector candidate; distinct from navigation depth and change history. |
| Collaboration — Shotgun Surgery | Shotgun Surgery | **Exact concept; different detector strategy** | High incoming impact: a changed service is used by many methods/classes | Keep the historical detector and consider static incoming-coupling evidence as a complementary variant. |
| Classification — Refused Parent Bequest | Refused Bequest | **Exact for Python/TypeScript; not applicable to native Rust** | A subclass uses too little of a meaningful inherited interface/implementation | Improve same-corpus inheritance usage analysis; keep Rust inapplicability explicit. |
| Classification — Tradition Breaker | Refused Bequest | **Absent** | A subclass departs from the inherited role/tradition and supplies substantial unrelated behavior | Prototype for Python/TypeScript only; requires hierarchy, role, override, and client evidence. |

The source-specific distinction matters:

- **God Class is not a synonym for Large Class.** Size is only one symptom.
- **Brain Method is not a synonym for Long Method.** It combines several forms
  of concentrated complexity.
- **Intensive and Dispersed Coupling are not Message Chains.** They depend on
  call intensity and provider dispersion, not dotted expression depth.
- **Shotgun Surgery can have multiple detector strategies.** Smells currently
  uses Git co-change spread; the book emphasizes static change-impact/caller
  evidence. Both can support one concept if their contracts remain explicit.

Because the book is copyrighted Springer material and its full text is
subscription content, Smells should cite it, record independently authored
metric definitions and boundary fixtures, and avoid reproducing prose, figures,
or threshold tables.

## Source 5: DECOR/DETEX

DECOR is primarily a method for converting literature descriptions into explicit,
reviewable detectors. Its five steps are description analysis, specification,
processing, detection, and manual validation. DETEX instantiates that method
with a vocabulary, taxonomy, DSL, generated algorithms, and contextual review.
The paper validates four design smells and says they are composed from 15
underlying code-smell terms
([author-hosted IEEE paper](https://www.ptidej.net/publications/documents/TSE09.doc.pdf),
[Ptidej DECOR overview and public rule cards](https://www.ptidej.net/research/designsmells),
[DOI](https://doi.org/10.1109/TSE.2009.50)).

### Four composite design smells

| DECOR design smell | Underlying detection idea | Current coverage | Recommendation |
| --- | --- | --- | --- |
| Blob / God Class | A large, low-cohesion controller centralizes processing around surrounding data classes | **Partial** through Large Class, Data Class, Feature Envy, and dependency evidence | High-value composite once cohesion, controller role, and client/provider relationships are available. DECOR explicitly identifies Blob and God Class as the same design smell. |
| Functional Decomposition | A procedural-style main/controller class, little inheritance/polymorphism, and associated small data/function classes | **Absent** | Prototype for OO-shaped Python/TypeScript code. Do not assume procedural organization is wrong in Rust or functional code. |
| Spaghetti Code | A class combines long parameterless methods, global state, procedural naming, and little OO structure | **Partial signals only** through Long Method; Global Data and the composite are absent | Prototype only after Global Data and structural context exist. Avoid a keyword-name verdict by itself. |
| Swiss Army Knife | One complex class exposes an excessive breadth of services/interfaces | **Absent** | Candidate for interface/service-surface breadth, but distinguish intentional façades, SDK clients, and adapters. |

### The 15 underlying terms are evidence, not 15 automatic new rules

DECOR's Figure 2 names the following unique vocabulary. Several are Fowler
smells; several are contextual ingredients. The parenthetical `bis` labels in
the paper indicate that some terms can occur in more than one classification.

| DECOR term | Current mapping | Coverage and use |
| --- | --- | --- |
| Comments | `comments` | **Exact concept**; not sufficient by itself to establish a composite design smell. |
| Duplicated Code | `duplicate-code` | **Exact concept**; may be composite evidence. |
| Long Method | `long-method` | **Exact concept**; component of Spaghetti Code. |
| Divergent Change | `divergent-change` | **Exact concept**; DECOR classification vocabulary, not a new rule. |
| Large Class | `large-class` | **Exact concept**; component of Blob/God Class. |
| Shotgun Surgery | `shotgun-surgery` | **Exact concept**; DECOR classification vocabulary. |
| Message Chain | `message-chains` | **Exact concept**; DECOR classification vocabulary. |
| No Inheritance | none | **Absent as a rule**, correctly: lack of inheritance is not independently a defect. Use only inside a contextual OO composite. |
| Low Cohesion | no direct rule | **Absent evidence metric**; valuable input to God/Brain Class without becoming a universal peer smell. |
| Controller Class | no direct rule | **Absent role evidence**; name heuristics alone are weak, so combine call graph, ownership, and processing concentration. |
| Procedural Class | no direct rule | **Absent role evidence**; use only where an OO design is expected. |
| DataClass | `data-class` | **Exact concept**; component/provider role in Blob. |
| No Polymorphism | none | **Absent as a rule**, correctly: use only as a contextual component where variation and OO polymorphism are expected. |
| Global Variable | none; overlaps Fowler 2e Global Data | **Absent**; supports prioritizing a repository-wide Global Data detector. |
| Long Parameter List | `long-parameter-list` | **Exact concept**; DECOR classification vocabulary. |

DECOR's lasting product lesson is the separation of:

1. a human-authored conceptual description;
2. measurable, structural, and lexical evidence;
3. an explicit composition rule;
4. generated or independently implemented detection;
5. contextual human validation.

That model aligns well with Smells' goal. Importing every ingredient as an
independent warning would not.

The paper is IEEE-copyrighted and the hosted copy states reuse restrictions.
Smells should cite and paraphrase the method, not copy its DSL grammar, figures,
tables, or substantial prose.

## Canonical alias and false-gap ledger

These terms should not create duplicate catalog entries:

| Source term | Canonical treatment |
| --- | --- |
| Long Function | Alias/display-name evolution of Long Method; expand beyond OO methods where needed. |
| Repeated Switches | Semantic refinement of Switch Statements. The current repeated-dispatch detector already follows the refined concept more closely. |
| Lazy Element | Broader successor to Lazy Class; expand the existing detector population rather than add a peer rule. |
| Insider Trading | Broader successor/near-equivalent to Inappropriate Intimacy; strengthen module and inheritance boundaries under one concept. |
| Refused Parent Bequest | Alias/metric-oriented version of Refused Bequest. |
| Significant Duplication | Stronger clone significance contract under Duplicate Code unless product research shows a distinct user decision. |
| Blob | DECOR explicitly equates it with God Class. |
| Brain Method | Not an alias for Long Method; it is a stricter composite that may include length. |
| Brain Class | Not an alias for Large Class; it aggregates concentrated method intelligence and class structure. |
| God Class | Not an alias for every Large Class; it adds responsibility, cohesion, and foreign-data/controller evidence. |
| Global Variable | A structural subcase/evidence source for Fowler 2e Global Data. |
| Low Cohesion, Controller Class, Procedural Class, No Inheritance, No Polymorphism | Evidence terms used within DECOR compositions, not automatically standalone defects. |

## Genuine gaps, prioritized

### Priority A: research and prototype next

#### 1. Global Data

Why it fits Smells:

- It is a genuine Fowler 2e gap and appears as a DECOR component.
- The important behavior is repository-wide: where mutable state is declared,
  which modules read it, which modules write it, and whether access is
  encapsulated.
- It complements rather than duplicates a syntax linter when it reports the
  ownership/read-write graph and change blast radius.

Minimum detector contract to investigate:

- language-specific definitions of module/global/class/singleton state;
- immutability and initialization-only exclusions;
- resolved writer and reader identities;
- number of writers, modules, packages, and change owners;
- generated/test/configuration exclusions;
- explicit evidence that no scan requires executing application code.

#### 2. God Class / Blob

Why it fits:

- It corrects the weakness of treating class size alone as a design diagnosis.
- It combines evidence Smells already owns—class size and foreign access—with
  new cohesion, processing-centrality, and data-provider relationships.
- The result is useful to an AI agent because it explains a responsibility hub,
  not merely a large file.

Do not ship it until false-positive fixtures cover orchestrators, façades,
framework registries, generated clients, and intentionally centralized adapters.

#### 3. Brain Method and Brain Class

Why they fit:

- Current Long Method and Large Class checks use simple budgets. Brain signals
  can combine size, cyclomatic complexity, nesting, variable/data access, and
  concentration inside a class.
- They can be composite findings that reuse existing measurements rather than
  duplicate low-level warnings.

Brain Class should depend on a stable Brain Method contract plus class-level
cohesion and distribution evidence.

#### 4. Intensive and Dispersed Coupling

Why they fit:

- They reveal how a callable depends on the rest of the repository.
- They distinguish many calls to a few providers from calls spread across many
  providers—information that Message Chains and private-access checks do not
  provide.
- They are well suited to repository dataflow and call-graph explanations for
  agents.

Required foundation: conservative symbol resolution, explicit unresolved-edge
handling, language-specific dynamic-dispatch limits, and deterministic resource
budgets.

#### 5. Lazy Element expansion

This is an evolution of `lazy-class`, not a new smell. Extend the population to
functions and possibly modules while preserving explicit roles that make small
elements valuable: ports, adapters, domain types, markers, schemas, transport
objects, and generated boundaries.

### Priority B: prototype only, then decide

#### Mutable Data

Start with narrow, explainable sub-signals: broadly scoped mutable state,
multiple writers, aliased mutable objects crossing module boundaries, or stored
derived values with independent update paths. Do not warn on every mutation.

#### Functional Decomposition and Spaghetti Code

These may be useful composite design views in OO-heavy Python/TypeScript
projects. They are risky as language-neutral rules because procedural and
functional structure can be intentional, and DECOR's original operationalization
was Java/OO specific.

#### Swiss Army Knife

Prototype service/interface breadth plus unrelated responsibility clusters.
Evaluate separately from Large Class and God Class, and protect intentional
façades and client libraries.

#### Tradition Breaker

Prototype only for languages and projects with meaningful class inheritance.
It needs more than unused inherited methods: it must show that a subtype presents
substantial behavior unrelated to the parent role. Native Rust should remain
not applicable unless a distinct trait-composition concept is intentionally
designed rather than translated by name.

#### Significant Duplication

Improve the existing duplicate-code contract to cover significant block-level,
cross-file, and near-miss clone groups. A clone group/chain with ownership and
change history is more useful than creating a second catalog label.

### Priority C: keep conceptual or review-only initially

#### Mysterious Name

Record it in the conceptual source manifest, but avoid a deterministic blocking
rule. A useful review signal could combine project glossary mismatches, repeated
renames, placeholder/generic names, and inconsistency among equivalent APIs.
These are evidence for a person or agent, not proof of a mysterious name.

#### Loops

Do not add a generic loop detector in the first expansion. It would largely be a
local language/style check and would conflict with cases where loops are clearer,
more efficient, or idiomatic. A future repository smell could target duplicated
complex traversal logic or loop bodies that combine unrelated responsibilities,
but that is a different, evidence-backed contract.

## Concepts outside the initial Smells scope

The following should not become automatic catalog entries merely because a
source mentions them:

- absence of inheritance;
- absence of polymorphism;
- private fields;
- parameterless methods;
- procedural names identified only by keyword lists;
- controller classes identified only by names;
- every loop;
- every mutable variable;
- every class implementing several interfaces.

These are either normal language features or weak component signals. They become
useful when combined with repository structure, responsibilities, coupling,
history, and explicit project context.

## Conceptual catalog expansion versus executable detector design

These are separate release decisions.

### Conceptual catalog work

For each accepted concept, record:

- stable Smells concept ID and display aliases;
- source work, edition, page/section URL or DOI, and checked date;
- relationship: exact concept, alias, refinement, component, or independent
  adaptation;
- applicability by language/paradigm;
- independently authored summary and review guidance;
- license/reuse boundary;
- lifecycle state: research, preview, stable, deprecated, or removed.

Adding a source reference does not require adding a detector. A source can
strengthen the rationale or naming of an existing concept.

### Executable detector work

Every detector still needs its own versioned contract:

- exact population and source scope;
- observations and units;
- symbol/type/call/history resolution semantics;
- threshold and comparator;
- exclusions and applicability;
- uncertainty and incomplete-analysis behavior;
- resource caps and deterministic ordering;
- boundary, counterexample, and false-positive fixtures;
- evidence fields that explain why a composite matched.

No literature source supplies universal thresholds for Python, Rust, and
TypeScript. Thresholds and syntax semantics remain Smells-owned, reviewed policy.

## Recommended source model

Use the sources in distinct roles:

| Source | Role in Smells | What not to do |
| --- | --- | --- |
| Fowler 2e and official Fowler material | Current conceptual catalog, aliases, rationale, refactoring direction | Do not convert prose into universal thresholds. |
| Fowler 1e | Legacy provenance for current concepts omitted or renamed in 2e | Do not treat legacy and successor names as duplicate smells. |
| Refactoring.Guru | Accessible explanatory reference and current v1 provenance | Do not embed substantial page copies or treat it as the only authority. |
| Mäntylä–Lassenius 2006 | Current taxonomy, Dead Code provenance, evidence of subjective human judgment | Do not present its categories as the only canonical ontology. |
| Lanza and Marinescu | Composite OO metric hypotheses and detector-design evidence | Do not transplant thresholds or OO assumptions unchanged across languages. |
| DECOR | Method for composing measurable, structural, and lexical evidence into auditable design-smell detectors | Do not flatten constituent signals into independent warnings. |

## Proposed next research tickets

1. **Source manifest and alias model:** represent edition, aliases, refinements,
   component relationships, applicability, and licensing without changing v1
   detector behavior.
2. **Global Data prototype:** create one language-specific contract per pack and
   evaluate cross-file writer/read ownership evidence.
3. **Composite-metrics foundation:** add cohesion, nesting, resolved data-owner,
   provider concentration, and call-graph measurements as evidence, not verdicts.
4. **God/Brain pilot:** evaluate God Class/Blob, Brain Method, and Brain Class on
   curated true/false examples and real repositories.
5. **Coupling pilot:** compare Intensive and Dispersed Coupling against current
   Feature Envy, Message Chains, Inappropriate Intimacy, and Shotgun Surgery to
   prove the new findings create distinct user decisions.
6. **Existing-rule evolution:** design migrations for Long Function,
   Repeated Switches, Lazy Element, Insider Trading, and Significant Duplication
   without creating alias duplicates.
7. **DECOR composite pilot:** only after the evidence foundation exists,
   prototype Functional Decomposition, Spaghetti Code, and Swiss Army Knife in
   an OO-heavy Python or TypeScript corpus.
8. **Catalog review gate:** approve concepts separately from detectors, then
   publish additions only in a new versioned rule pack.

## Source and access notes

| Source | Authoritative reference | Access/reuse limitation |
| --- | --- | --- |
| Fowler and Beck, *Refactoring*, 1e | [Pearson/Addison-Wesley sample](https://ptgmedia.pearsoncmg.com/images/9780201485677/samplepages/0201485672.pdf) | Copyrighted book; use names and paraphrased concepts with citation. |
| Fowler and Beck, *Refactoring*, 2e | [Official InformIT chapter](https://www.informit.com/articles/article.aspx?p=2952392) and [book record](https://www.informit.com/store/refactoring-improving-the-design-of-existing-code-9780134757711) | Copyrighted book/chapter; do not embed substantial prose. |
| Fowler, “Code Smell” | [Author's site](https://martinfowler.com/bliki/CodeSmell.html) | Cite and paraphrase; no permissive content license was identified for wholesale reuse. |
| Refactoring.Guru | [Catalog](https://refactoring.guru/refactoring/smells), [usage policy](https://refactoring.guru/content-usage-policy) | Most content copyrighted; only limited linked quotation and limited illustration reuse are expressly permitted. |
| Mäntylä and Lassenius (2006) | [Springer record](https://link.springer.com/article/10.1007/s10664-006-9002-8), [author-hosted full text](https://mmantyla.github.io/ESE_2006.pdf), DOI `10.1007/s10664-006-9002-8` | Copyrighted Springer paper; cite and paraphrase rather than reproduce tables or prose. |
| Lanza and Marinescu (2006) | [Springer record and DOI](https://link.springer.com/book/10.1007/3-540-39538-5) | Copyrighted, preview/subscription access; independently author detector contracts. |
| Moha et al., DECOR (2010) | [DOI](https://doi.org/10.1109/TSE.2009.50), [HAL open full text](https://inria.hal.science/inria-00538476/document), DOI `10.1109/TSE.2009.50` | IEEE-copyrighted paper; cite and paraphrase. Do not copy its grammar, figures, or tables wholesale. |

## Final recommendation

Do not enlarge the catalog by unioning every name from the five sources.

Adopt a layered model:

1. Keep the current 23 stable in v1.
2. Add source/edition/alias/component provenance first.
3. Treat Fowler 2e's four new concepts as the only direct catalog delta from
   the Fowler–Mäntylä–Refactoring.Guru lineage.
4. Treat Lanza/Marinescu and DECOR as sources for composite, repository-level
   design evidence.
5. Prototype and validate each candidate before admitting it to a new rule-pack
   version.

The immediate research order should be **Global Data**, **God Class/Blob**,
**Brain Method/Brain Class**, and **Intensive/Dispersed Coupling**. These make
the strongest case for Smells as a repository-level design-analysis layer rather
than another language linter.
