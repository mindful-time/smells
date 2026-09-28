# Fowler 2e Chapter 3 page audit

Audit date: 2026-09-27

## Result

The 24 `fowler-refactoring-2e` references in
[`rules/concept-catalog-v1.json`](../rules/concept-catalog-v1.json) have the
correct second-edition terms, unique ordinals 1-24, matching InformIT
`seqNum` values, and matching "article page _n_ of 24" locators. All 24
InformIT URLs returned HTTP 200 during this audit. The publisher's
[book record](https://www.informit.com/store/refactoring-improving-the-design-of-existing-code-9780134757599)
identifies the second edition and links the authorized
[Chapter 3 sample](https://www.informit.com/articles/article.aspx?p=2952392),
whose live contents give the same 24-term order.

The audit initially found one completeness gap: the 24 catalog locators did
not record printed-book or physical-PDF pages. The catalog now records both
page systems alongside the InformIT article page for every Fowler 2e claim.

## Verification basis

Primary page evidence was the supplied second-edition PDF:

- Local file: `/Users/akshobhya/Downloads/Refactoring.Improving.the.Design.of.Existing.Code.2nd.Edition.2018.11.pdf`
- SHA-256: `4c95426002b0c157978f54a0f38bdcc01b5e6d24293aa3f43b9a5e007c031ec9`
- Physical length: 445 PDF pages
- Chapter 3 begins on printed page 71, physical PDF page 93
- For the smell pages, `physical PDF page (1-based) = printed page + 22`;
  the zero-based page index is one less than that.

Text extraction was checked against rendered pages 93-106. A page number in
the `PDF page` column is therefore the value a conforming `#page=N` PDF viewer
would use for this exact binary; `index` is included to remove zero/one-based
ambiguity.

## Exact Chapter 3 inventory

| InformIT page | Catalog concept ID | Exact 2e source term and current URL | Printed page where heading begins | PDF page (1-based) | PDF index (0-based) |
| ---: | --- | --- | ---: | ---: | ---: |
| 1 | `mysterious-name` | [Mysterious Name](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=1) | 72 | 94 | 93 |
| 2 | `duplicate-code` | [Duplicated Code](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=2) | 72 | 94 | 93 |
| 3 | `long-method` | [Long Function](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=3) | 73 | 95 | 94 |
| 4 | `long-parameter-list` | [Long Parameter List](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=4) | 74 | 96 | 95 |
| 5 | `global-data` | [Global Data](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=5) | 74 | 96 | 95 |
| 6 | `mutable-data` | [Mutable Data](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=6) | 75 | 97 | 96 |
| 7 | `divergent-change` | [Divergent Change](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=7) | 76 | 98 | 97 |
| 8 | `shotgun-surgery` | [Shotgun Surgery](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=8) | 76 | 98 | 97 |
| 9 | `feature-envy` | [Feature Envy](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=9) | 77 | 99 | 98 |
| 10 | `data-clumps` | [Data Clumps](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=10) | 78 | 100 | 99 |
| 11 | `primitive-obsession` | [Primitive Obsession](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=11) | 78 | 100 | 99 |
| 12 | `switch-statements` | [Repeated Switches](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=12) | 79 | 101 | 100 |
| 13 | `loops` | [Loops](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=13) | 79 | 101 | 100 |
| 14 | `lazy-class` | [Lazy Element](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=14) | 80 | 102 | 101 |
| 15 | `speculative-generality` | [Speculative Generality](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=15) | 80 | 102 | 101 |
| 16 | `temporary-field` | [Temporary Field](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=16) | 80 | 102 | 101 |
| 17 | `message-chains` | [Message Chains](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=17) | 81 | 103 | 102 |
| 18 | `middle-man` | [Middle Man](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=18) | 81 | 103 | 102 |
| 19 | `inappropriate-intimacy` | [Insider Trading](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=19) | 82 | 104 | 103 |
| 20 | `large-class` | [Large Class](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=20) | 82 | 104 | 103 |
| 21 | `alternative-classes-with-different-interfaces` | [Alternative Classes with Different Interfaces](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=21) | 83 | 105 | 104 |
| 22 | `data-class` | [Data Class](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=22) | 83 | 105 | 104 |
| 23 | `refused-bequest` | [Refused Bequest](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=23) | 83 | 105 | 104 |
| 24 | `comments` | [Comments](https://www.informit.com/articles/article.aspx?p=2952392&seqNum=24) | 84 | 106 | 105 |

## Catalog result

No unresolved catalog discrepancy remains. Every Fowler 2e `source_locator`
now records the printed page, the one-based page for the audited PDF binary,
and the InformIT article page. The PDF SHA-256 remains in this audit because
front matter and alternate ebook builds can change the physical offset.

The four intentional second-edition mappings remain correctly named:
`long-method` -> Long Function, `switch-statements` -> Repeated Switches,
`lazy-class` -> Lazy Element, and `inappropriate-intimacy` -> Insider Trading.

## GitHub mirror disposition

**Recommendation: omit the GitHub blob mirror from the source manifest and
from per-concept references.** Keep the publisher-hosted InformIT chapter as
the canonical URL.

The supplied
[GitHub blob](https://github.com/wuzhouhui/misc2/blob/master/Refactoring.Improving.the.Design.of.Existing.Code.2nd.Edition.2018.11.pdf)
was downloaded during the audit and is currently byte-identical to the local
PDF (same SHA-256 above) at `master` commit
`80953ab4e06c990e8006eab83d99de1422027b7b`. That makes it useful as a
transient verification aid, but not as registry provenance:

- **Authority:** it is an unrelated user repository, not Fowler, Pearson, or
  Addison-Wesley. The official InformIT record is the authoritative edition
  source and already exposes the complete chapter as authorized HTML.
- **Stability:** the proposed URL follows mutable `master`; even a commit-pinned
  URL remains dependent on an unaffiliated repository and possible removal.
- **Reuse risk:** the publisher describes its PDF ebook as a purchased,
  personalized product and marks the work as copyrighted. The mirror does not
  establish redistribution permission. Recording it as a reusable source URL
  would create avoidable provenance and takedown risk.

If exact-page reproducibility is needed, retain the printed page in
`source_locator` and the verified PDF hash in audit evidence. Do not make the
third-party mirror canonical or a supported secondary dependency.
