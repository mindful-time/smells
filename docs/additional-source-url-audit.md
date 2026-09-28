# Additional source URL audit

Reviewed against `rules/sources-v1.json` and
`rules/concept-catalog-v1.json` on 2026-09-28. The two supplied URLs have
different roles: the HAL URL is the open full text for the existing DECOR
source, while the Mäntylä webpage is an abridged companion that is deliberately
not cataloged as a source. The complete 2006 paper is the sole Mäntylä source.

## 1. HAL/Inria document: DECOR

### Identity and metadata

`https://inria.hal.science/inria-00538476/document` is the HAL full text of
Naouel Moha, Yann-Gaël Guéhéneuc, Laurence Duchien, and Anne-Françoise Le
Meur, *DECOR: A Method for the Specification and Detection of Code and Design
Smells*. The published article is in *IEEE Transactions on Software
Engineering* 36(1), January/February 2010, pp. 20-36, DOI
[`10.1109/TSE.2009.50`](https://doi.org/10.1109/TSE.2009.50). The
[HAL resolver](https://inria.hal.science/inria-00538476/document) adds a cover
page, so HAL PDF p. 2 is printed p. 20. Title, authors, publisher, year, and DOI
in source `decor-2010` are correct.

### Inventory and definitions

The paper specifies four design smells in this order: **Blob / God Class**,
**Functional Decomposition**, **Spaghetti Code**, and **Swiss Army Knife**.
Its source-faithful descriptions are in §4.1.2, Table 1, printed p. 24 / HAL
PDF p. 6 ([page-level full text](https://inria.hal.science/inria-00538476/document#page=6)):

1. **Blob / God Class**: a large, low-cohesion controller that monopolizes
   processing and depends on surrounding data classes.
2. **Functional Decomposition**: a procedural main class, often procedurally
   named, with little inheritance or polymorphism and associations to small
   classes with many private fields and few methods.
3. **Spaghetti Code**: an unstructured class with long parameterless methods,
   global variables, procedural names, and little or no inheritance and
   polymorphism.
4. **Swiss Army Knife**: a complex class exposing many services, exemplified by
   implementing many interfaces; it is explicitly distinguished from a Blob.

Figure 2 on the same page names the 15 distinct underlying code smells:
**Message Chain, Shotgun Surgery, Duplicated Code, Comments, DataClass, No
Polymorphism, Global Variable, Controller Class, Procedural Class, Long Method,
Large Class, No Inheritance, Low Cohesion, Divergent Change,** and **Long
Parameter List**. Repeated appearances of Comments and Duplicated Code in the
figure do not create additional smell types. The paper prints the complete
Spaghetti Code rule card in Figure 6, printed p. 27 / HAL PDF p. 9
([page-level full text](https://inria.hal.science/inria-00538476/document#page=9));
it does not print equivalently complete rule cards for all four smells.

### URL role and catalog findings

- Keep the DOI as `decor-2010.canonical_url`. Add the supplied HAL resolver as
  an **alternate/open full-text URL**. For the four concept claims, prefer the
  HAL URL with `#page=6` and locator `§4.1.2, Table 1, printed p. 24; HAL PDF
  p. 6`. Use `#page=9` only for the Spaghetti Code rule-card claim.
- The current source schema has `canonical_url` and book-oriented
  `edition_url`, but no typed alternate-link collection. Do not overload
  `edition_url` for this paper. Until the schema gains link roles, put the HAL
  URL on the four claims; a later schema revision can add roles such as
  `open_full_text`, `publisher_record`, and `project_copy`.
- The current Ptidej `reference_url` is also a full-text copy, but the generic
  locator `Design-smell specification — …` is not auditable enough and implies
  a full printed specification where the paper sometimes supplies only the
  Table 1 description. This is a locator inaccuracy.
- `god-class` correctly records **Blob** as an equivalent name. The other three
  catalog summaries are broader syntheses, not exact paper definitions.
  `spaghetti-code` adds “control flow … entangled,” which Table 1 does not say,
  and `swiss-army-knife` adds “unrelated” capabilities, which the source does
  not require. `functional-decomposition` also omits most of the paper's
  operational characteristics. If these references remain `relationship:
  defines`, align the summaries with Table 1 or mark the summaries as catalog
  synthesis.

## 2. Mäntylä web taxonomy

### Identity and metadata

The supplied page is titled *Bad code smells - A Taxonomy* and headed *A
Taxonomy for “Bad Code Smells”*. It is hosted in
[Mika Mäntylä's GitHub Pages repository](https://github.com/mmantyla/mmantyla.github.io/blob/main/BadCodeSmellsTaxonomy.html),
uses first-person authorship, and directs feedback to Mika Mäntylä. It has no
author metadata, publication date, version, canonical tag, or license. Its
preferred scholarly citation is a different work: Mika V. Mäntylä and Casper
Lassenius, *Subjective Evaluation of Software Evolvability Using Code Smells:
An Empirical Study*, *Empirical Software Engineering* 11(3), 2006, pp. 395-431,
DOI [`10.1007/s10664-006-9002-8`](https://doi.org/10.1007/s10664-006-9002-8).
The page is therefore best attributed as an author-maintained companion
taxonomy, not assigned an inferred publication year.

### Exact web inventory

The page's [taxonomy table](https://mmantyla.github.io/BadCodeSmellsTaxonomy.html#table1)
contains **21 smells in five groups**, in this order:

| Group | Smells |
| --- | --- |
| Bloaters | Long Method; Large Class; Primitive Obsession; Long Parameter List; Data Clumps |
| Object-Orientation Abusers | Switch Statements; Temporary Field; Refused Bequest; Alternative Classes with Different Interfaces |
| Change Preventers | Divergent Change; Shotgun Surgery; Parallel Inheritance Hierarchies |
| Dispensables | Lazy Class; Data Class; Duplicate Code; Dead Code; Speculative Generality |
| Couplers | Feature Envy; Inappropriate Intimacy; Message Chains; Middle Man |

The page gives group-level discussions and selected examples, not a complete
standalone definition for every smell. In particular, it describes Bloaters as
oversized structures, OO Abusers as failures to exploit OO mechanisms, Change
Preventers as obstacles to one-change/one-class locality, Dispensables as
unnecessary code or abstractions, and Couplers as excessive coupling or
delegation.

### Why the 2006 paper is the single catalog source

The complete peer-reviewed 2006 paper presents all **23 smells** in §3.5. It
uses five named groups and then retains **Comments** and **Incomplete Library
Class** as ungrouped smells. The paper explicitly calls this organization an
improved version of the authors' earlier taxonomy. The catalog therefore uses
only source `mantyla-lassenius-2006`, with the
[Springer record](https://link.springer.com/article/10.1007/s10664-006-9002-8)
as its canonical URL and the
[author-hosted PDF](https://mmantyla.github.io/ESE_2006.pdf) as its direct full
text. The 2003 paper is not a second catalog source.

### Supersession / latest-only decision

**Decision: use the complete 2006 paper as the only Mäntylä catalog source.**
Do not create separate source records for either the 2003 predecessor or the
abridged website.

- **The 2006 paper identifies the revision itself.** In §3.5 (printed p. 408 /
  PDF p. 14), the
  [*Empirical Software Engineering* paper](https://mmantyla.github.io/ESE_2006.pdf#page=14)
  explicitly calls its six-category taxonomy an improved version of the earlier
  taxonomy. That is sufficient to select the complete 2006 paper without
  cataloging its predecessor separately.
- **The website is an abridged companion to that revision, not a complete new
  successor.** The [page](https://mmantyla.github.io/BadCodeSmellsTaxonomy.html)
  tells readers to cite the 2006 article, presents "a taxonomy of five groups,"
  and notes that Parallel Inheritance Hierarchies "was originally placed in
  OO-abusers" while now listing it under Change Preventers. That is explicit
  evidence of changed classification. It does not mention the 2003 paper by name
  or use replacement language. It also contains only 21 smells: the 2006 paper
  studies 23 and separately retains Comments and Incomplete Library Class as
  smells that could not be included in a named group (printed p. 408 / PDF p.
  14). Therefore the page is not a faithful latest-only substitute for either
  paper.
- **Repository dates do not date the taxonomy.** The GitHub
  [path history](https://api.github.com/repos/mmantyla/mmantyla.github.io/commits?path=BadCodeSmellsTaxonomy.html&per_page=100),
  checked 2026-09-28, contains only the repository's root commit, dated
  2021-02-08 with message `Root-folder`; the
  [commit](https://github.com/mmantyla/mmantyla.github.io/commit/c3684e524141a2f4f37420b166aecc8efd21d49b)
  adds the already-formed HTML page. It provides no earlier edit history and does
  not support treating 2021 as the taxonomy's publication or revision date.

The 2003 work remains part of the scholarly history described inside the 2006
paper, but it is not repeated in the source manifest or on concept claims. This
keeps the agent-facing catalog unambiguous.

### URL role and catalog findings

- Do **not** add the supplied webpage to the source manifest or concept claims.
- Use the 2006 Springer record as `canonical_url` and the complete author-hosted
  PDF as `full_text_url`.
- Give all 23 concept claims exact §3.5 locators: printed p. 408 / PDF p. 14 for
  Bloaters, Object-Orientation Abusers, and Change Preventers; printed p. 409 /
  PDF p. 15 for Dispensables, Couplers, and the two ungrouped smells.

## Recommended disposition

1. Add the HAL URL to the existing `decor-2010` source as its stable open
   full-text representation, with page-level claim links; keep the DOI as the
   source identity.
2. Keep the complete peer-reviewed 2006 paper as the only Mäntylä source.
3. Do not include the 2003 paper in the source manifest or concept references.
4. Do not create a separate 21-smell source from the website.
5. Correct the vague paper locators and the DECOR summary/source-definition
   mismatches before treating these references as exact definitions.
