# Sprint 2 Planning

| Field | Value |
| --- | --- |
| Date | 16 November 2026 |
| Medium | Microsoft Teams (online) |
| Participants | Melih, Sıla, Mürsel, Mirac, Mehmet, Emir |
| Facilitator | Sıla (Scrum Master) |
| Sprint | Sprint 2 – Requirements & Architecture, 17–24 November 2026 |
| Milestone | [Sprint 2](https://github.com/bookademic-team/Bookademic/milestone/2) |

## Sprint goal

Turn the Sprint 1 foundation into a complete requirements and architecture baseline for implementation in Sprint 3: an SRS traceable to the backlog, measurable non-functional requirements, Figma prototypes of the main journey, and C4 and UML views with the database schema.

## Deliverables and owners

| # | Deliverable | Files | Owner | Issue |
| --- | --- | --- | --- | --- |
| 1 | SRS and refined product backlog | `docs/sprint2/srs.md`, `docs/sprint2/backlog.md` | Melih | #60, #61 |
| 2 | Non-functional requirements and quality measures | `docs/sprint2/nfr.md` | Mirac | #66 |
| 3 | Figma prototypes and user-flow diagrams | Figma, `docs/sprint2/ui-prototypes.md` | Emir | #67 |
| 4 | C4 and UML architecture, database schema | `docs/architecture/c4.md`, `uml.md`, `database.md`, `docs/adr/0002-*` | Mürsel | #62, #63, #64, #65 |
| 4 | C4 deployment diagram | `docs/architecture/deployment.md` | Mehmet | #71 |
| 4 | Sequence diagrams for owned stories | `docs/architecture/sequence/US-xx.md` | All | #68 |
| – | Issue and pull request templates | `.github/` | Mehmet | #59 |
| – | Meeting records, README, contributions | `docs/meetings/`, `README.md`, `CONTRIBUTIONS.md` | Sıla | #58, #69 |
| – | Release `v0.2.0-sprint2` | tag and GitHub Release | Mehmet | #70 |

## Decisions

| # | Decision | Rationale |
| --- | --- | --- |
| D1 | Melih (SRS, backlog) and Mürsel (C4, UML, ERD, ADR) finish their work and open their pull requests by **Friday 20 November 23:59**. Reviews and merges are done on **Saturday 21 November by 14:00**; dependent work starts afterwards from the merged `develop`. Intermediate dates are left to the owners | NFRs, Figma, the deployment diagram and the sequence diagrams use their requirement IDs and component names |
| D2 | While waiting, each dependent member reviews the document they depend on (Mirac: SRS and ERD, Emir: UML and backlog, Mehmet: C4) | They see early whether the information they need is there |
| D3 | Every member draws the sequence diagram of one of their own stories (US-02 Mehmet, US-06 Mürsel, US-09 Sıla, US-13 Melih, US-18 Emir, US-20 Mirac) | Complete UML views and visible individual contribution in the architecture work |
| D4 | Shared names are taken from OpenAPI v0.2.2 and ADR-0001: containers Web App, API, Database; components M1 Identity, M2 Booking Core, M3 Lab, M4 Office Hours; entity and enum names as in the OpenAPI schemas | One vocabulary across SRS, C4, UML, ERD and Figma |
| D5 | ID formats: `FR-<module>-<no>` for functional and `NFR-<category>-<no>` for non-functional requirements; existing UC, US and SCR IDs stay unchanged | Traceability from requirement to story, use case, endpoint, screen and test |
| D6 | Diagrams are written in Mermaid inside Markdown; Figma screens are also exported as PNG into the repository | Diagrams render on GitHub; evidence stays available without a Figma login |
| D7 | `docs/sprint1/` is kept as the Sprint 1 snapshot; updated documents go to `docs/sprint2/` and `docs/architecture/` | Sprint 1 submission stays reproducible |
| D8 | The OpenAPI contract stays the single source of truth; any new field or endpoint is added there first by Mürsel (version 0.2.3) | Avoids documents drifting from the API |

## Schedule

| Date | Milestone |
| --- | --- |
| 20 Nov 23:59 | SRS, backlog, C4, UML, ERD and ADR pull requests open on GitHub |
| 21 Nov 14:00 | These pull requests reviewed and merged |
| 21–22 Nov | NFR, Figma, deployment and sequence diagrams written from the merged documents; pull requests open by 22 Nov |
| 23 Nov | Cross-check of all documents, fixes, all pull requests merged |
| 24 Nov 12:00 | Release pull request, tag `v0.2.0-sprint2` |
| 24 Nov 18:00 | Submission on Microsoft Teams |

## Reviewers

| Pull request | Reviewer |
| --- | --- |
| #58 planning and retrospective notes | Mehmet |
| #59 templates | Sıla |
| #60 SRS | Mirac |
| #61 backlog | Emir, Sıla |
| #62 C4 | Mehmet |
| #63 UML | Emir |
| #64 ERD | Mirac |
| #65 ADR | Sıla |
| #66 NFR, #67 Figma | Melih |
| #71 deployment | Mürsel |
| #68 sequence diagrams | Mürsel (US-06: Emir) |
| #69 records | Mirac |
| #70 release | Sıla |

**Next meeting:** Friday 20 November, stand-up on WhatsApp – status of the SRS, backlog and architecture pull requests due that night.
