# Bookademic – Individual Contributions

Repository: https://github.com/bookademic-team/Bookademic · Course: SENG 321 Software Engineering, Fall 2026

Every item below links to its GitHub record. The newest sprint comes first.

## Sprint 2 – Requirements & Architecture

Release: `v0.2.0-sprint2` · Milestone: [Sprint 2](https://github.com/bookademic-team/Bookademic/milestone/2?closed=1) · Counts cover the pull requests merged into `develop` for Sprint 2 (#72 … #98). Pull request #69 (this record) and the release pull request are listed under their owners but not counted.

### Summary

| Member | Role | Commits | PRs merged | PRs reviewed |
|---|---|:-:|:-:|:-:|
| Melih Adıgüzel | Product Owner & Requirements Engineer | 13 | 6 | 5 |
| Sıla Yıldırım | Scrum Master & Agile Process Coach | 4 | 2 | 3 |
| Mürsel Debbağ | Lead Software Architect & Technical Lead | 17 | 7 | 8 |
| Muhammed Mirac Kocadağ | Quality Assurance & Test Automation Engineer | 7 | 2 | 4 |
| Mehmet Çalışkan | DevOps & CI/CD Infrastructure Engineer | 7 | 3 | 2 |
| Emir Altunbulak | Implementation & Integration Engineer | 16 | 4 | 2 |

### Melih Adıgüzel

- **GitHub:** [@melihdgzl](https://github.com/melihdgzl) · [Sprint 2 commits](https://github.com/bookademic-team/Bookademic/commits/develop?author=melihdgzl&since=2026-11-16)

**Sprint 2 work**

- Software Requirements Specification: functional requirements, use cases UC-1 … UC-8, data and interface requirements, traceability matrix ([`srs.md`](docs/sprint2/srs.md)), with QA clarifications C1–C5 and the glossary fix for appointments
- Refined product backlog: Sprint 3 MVP selection, refined acceptance criteria, implementation order ([`backlog.md`](docs/sprint2/backlog.md), [`issues.csv`](docs/sprint2/issues.csv)); story Issues synced with `sync_issues.py`
- US-13 lab access approval sequence diagram ([`US-13.md`](docs/architecture/sequence/US-13.md))
- Product Owner review and approval of the Figma prototypes (#92, #97, #98) and the NFRs (#80)

**Issues**

- [#60](https://github.com/bookademic-team/Bookademic/issues/60) Software Requirements Specification (SRS), [#61](https://github.com/bookademic-team/Bookademic/issues/61) Refine product backlog for Sprint 3
- Shared: [#68](https://github.com/bookademic-team/Bookademic/issues/68) Sequence diagrams for owned stories

**Pull requests (authored, merged)**

- [#82](https://github.com/bookademic-team/Bookademic/pull/82) docs(srs): add Software Requirements Specification (#60)
- [#83](https://github.com/bookademic-team/Bookademic/pull/83) docs(backlog): refine product backlog for Sprint 3 (#61)
- [#84](https://github.com/bookademic-team/Bookademic/pull/84) docs(srs): QA clarifications for testability (#60)
- [#85](https://github.com/bookademic-team/Bookademic/pull/85) docs(sequence): US-13 lab access approval (#68)
- [#93](https://github.com/bookademic-team/Bookademic/pull/93) docs(srs): clarify Resource glossary entry (#60)
- [#95](https://github.com/bookademic-team/Bookademic/pull/95) docs(srs): update OpenAPI references to v0.2.3 (#60)

**Reviews given**

- [#78](https://github.com/bookademic-team/Bookademic/pull/78) (approved → approved), [#80](https://github.com/bookademic-team/Bookademic/pull/80) (changes requested → approved), [#92](https://github.com/bookademic-team/Bookademic/pull/92) (changes requested → approved), [#97](https://github.com/bookademic-team/Bookademic/pull/97) (approved), [#98](https://github.com/bookademic-team/Bookademic/pull/98) (approved)

### Sıla Yıldırım

- **GitHub:** [@silayildrm06-cell](https://github.com/silayildrm06-cell) · [Sprint 2 commits](https://github.com/bookademic-team/Bookademic/commits/develop?author=silayildrm06-cell&since=2026-11-16)

**Sprint 2 work**

- Sprint 1 retrospective and Sprint 2 planning, including the "dependent work in order" rule ([`retrospective`](docs/meetings/2026-11-16-sprint1-retrospective.md), [`planning`](docs/meetings/2026-11-16-sprint2-planning.md))
- Sprint 2 Issues, owners, reviewers and deadlines; daily GitHub status checks and follow-up in the team chat
- Stand-up records of 20 and 22 November ([`docs/meetings/`](docs/meetings/)), README documentation table and this contributions record
- US-09 cancellation sequence diagram ([`US-09.md`](docs/architecture/sequence/US-09.md))
- Issue and pull request hygiene: closing keywords, milestones, wrongly named branch removed

**Issues**

- [#58](https://github.com/bookademic-team/Bookademic/issues/58) Sprint 1 retrospective and Sprint 2 planning notes, [#69](https://github.com/bookademic-team/Bookademic/issues/69) Stand-up records, README and Sprint 2 contributions
- Shared: [#68](https://github.com/bookademic-team/Bookademic/issues/68) Sequence diagrams for owned stories

**Pull requests (authored, merged)**

- [#72](https://github.com/bookademic-team/Bookademic/pull/72) docs: Sprint 1 retrospective and Sprint 2 planning notes (#58)
- [#86](https://github.com/bookademic-team/Bookademic/pull/86) docs(architecture): US-09 cancellation sequence diagram (#68)
- #69 stand-up records, README and this file (open at the time of writing; reviewer: Mirac)

**Reviews given**

- [#76](https://github.com/bookademic-team/Bookademic/pull/76) (approved), [#83](https://github.com/bookademic-team/Bookademic/pull/83) (approved), [#90](https://github.com/bookademic-team/Bookademic/pull/90) (approved)

### Mürsel Debbağ

- **GitHub:** [@murseldbbg](https://github.com/murseldbbg) · [Sprint 2 commits](https://github.com/bookademic-team/Bookademic/commits/develop?author=murseldbbg&since=2026-11-16)

**Sprint 2 work**

- C4 context, container and component diagrams ([`c4.md`](docs/architecture/c4.md))
- UML use case, domain class and reservation state machine diagrams ([`uml.md`](docs/architecture/uml.md))
- Database schema with ERD and `EXCLUDE USING gist` constraint ([`database.md`](docs/architecture/database.md))
- ADR-0002 JWT authentication and ADR-0003 notification events ([`docs/adr/`](docs/adr/))
- OpenAPI contract v0.2.3 ([`openapi.yaml`](docs/api/openapi.yaml)); US-06 lab reservation sequence diagram ([`US-06.md`](docs/architecture/sequence/US-06.md))

**Issues**

- [#62](https://github.com/bookademic-team/Bookademic/issues/62) C4 context, container and component diagrams, [#63](https://github.com/bookademic-team/Bookademic/issues/63) UML diagrams, [#64](https://github.com/bookademic-team/Bookademic/issues/64) Database schema (ERD), [#65](https://github.com/bookademic-team/Bookademic/issues/65) ADR-0002 and later architecture decisions, [#77](https://github.com/bookademic-team/Bookademic/issues/77) OpenAPI v0.2.3
- Shared: [#68](https://github.com/bookademic-team/Bookademic/issues/68) Sequence diagrams for owned stories

**Pull requests (authored, merged)**

- [#73](https://github.com/bookademic-team/Bookademic/pull/73) docs: C4 context, container and component diagrams (#62)
- [#74](https://github.com/bookademic-team/Bookademic/pull/74) docs: UML diagrams (use case, class, state machine) (#63)
- [#75](https://github.com/bookademic-team/Bookademic/pull/75) docs: database schema (ERD) (#64)
- [#76](https://github.com/bookademic-team/Bookademic/pull/76) docs: ADR-0002 JWT authentication and ADR-0003 notification events (#65)
- [#78](https://github.com/bookademic-team/Bookademic/pull/78) docs(api): OpenAPI v0.2.3 – description-only rules from SRS C1 (#77)
- [#87](https://github.com/bookademic-team/Bookademic/pull/87) docs(sequence): add US-06 diagram (#68)
- [#96](https://github.com/bookademic-team/Bookademic/pull/96) docs(db): reference OpenAPI v0.2.3 (#64)

**Reviews given**

- [#81](https://github.com/bookademic-team/Bookademic/pull/81) (approved), [#85](https://github.com/bookademic-team/Bookademic/pull/85) (approved), [#86](https://github.com/bookademic-team/Bookademic/pull/86) (approved), [#88](https://github.com/bookademic-team/Bookademic/pull/88) (approved), [#91](https://github.com/bookademic-team/Bookademic/pull/91) (approved), [#93](https://github.com/bookademic-team/Bookademic/pull/93) (approved), [#94](https://github.com/bookademic-team/Bookademic/pull/94) (approved), [#95](https://github.com/bookademic-team/Bookademic/pull/95) (approved)

### Muhammed Mirac Kocadağ

- **GitHub:** [@muhammedkocadag](https://github.com/muhammedkocadag) · [Sprint 2 commits](https://github.com/bookademic-team/Bookademic/commits/develop?author=muhammedkocadag&since=2026-11-16)

**Sprint 2 work**

- Non-functional requirements and quality measures with metrics, targets and test methods ([`nfr.md`](docs/sprint2/nfr.md))
- US-20 office-hour day cancellation sequence diagram with test mapping ([`US-20.md`](docs/architecture/sequence/US-20.md))
- QA reviews of the SRS (testability clarifications) and the database schema

**Issues**

- [#66](https://github.com/bookademic-team/Bookademic/issues/66) Non-functional requirements and quality measures
- Shared: [#68](https://github.com/bookademic-team/Bookademic/issues/68) Sequence diagrams for owned stories

**Pull requests (authored, merged)**

- [#80](https://github.com/bookademic-team/Bookademic/pull/80) docs(qa): add non-functional requirements (#66)
- [#81](https://github.com/bookademic-team/Bookademic/pull/81) docs(architecture): US-20 office-hour day cancellation sequence diagram (#68)

**Reviews given**

- [#75](https://github.com/bookademic-team/Bookademic/pull/75) (changes requested → approved), [#82](https://github.com/bookademic-team/Bookademic/pull/82) (approved), [#84](https://github.com/bookademic-team/Bookademic/pull/84) (approved), [#96](https://github.com/bookademic-team/Bookademic/pull/96) (approved)

### Mehmet Çalışkan

- **GitHub:** [@Mhmt-Clskn](https://github.com/Mhmt-Clskn) · [Sprint 2 commits](https://github.com/bookademic-team/Bookademic/commits/develop?author=Mhmt-Clskn&since=2026-11-16)

**Sprint 2 work**

- C4 deployment view: Docker Compose services, ports, health checks and environment variables, incl. `JWT_SIGNING_KEY` ([`deployment.md`](docs/architecture/deployment.md))
- Issue and pull request templates ([`.github/`](.github/)); Git author name fixed (retrospective action A6)
- US-02 login and JWT sequence diagram ([`US-02.md`](docs/architecture/sequence/US-02.md))
- Sprint 2 release `v0.2.0-sprint2` (#70)

**Issues**

- [#59](https://github.com/bookademic-team/Bookademic/issues/59) Issue and pull request templates, [#70](https://github.com/bookademic-team/Bookademic/issues/70) Release v0.2.0-sprint2, [#71](https://github.com/bookademic-team/Bookademic/issues/71) C4 deployment diagram
- Shared: [#68](https://github.com/bookademic-team/Bookademic/issues/68) Sequence diagrams for owned stories

**Pull requests (authored, merged)**

- [#88](https://github.com/bookademic-team/Bookademic/pull/88) docs(deployment): C4 deployment view and JWT key variable (#71)
- [#90](https://github.com/bookademic-team/Bookademic/pull/90) chore(github): issue and pull request templates (#59)
- [#94](https://github.com/bookademic-team/Bookademic/pull/94) docs(sequence): add US-02 diagram (#68)
- Release pull request `develop` → `main` for `v0.2.0-sprint2` (#70; reviewer: Sıla)

**Reviews given**

- [#72](https://github.com/bookademic-team/Bookademic/pull/72) (approved), [#73](https://github.com/bookademic-team/Bookademic/pull/73) (approved)

### Emir Altunbulak

- **GitHub:** [@emiraltunbulak-dotcom](https://github.com/emiraltunbulak-dotcom) · [Sprint 2 commits](https://github.com/bookademic-team/Bookademic/commits/develop?author=emiraltunbulak-dotcom&since=2026-11-16)

**Sprint 2 work**

- Figma prototypes for SCR-01 … SCR-11, CMP-01 and the 403 page, clickable MVP journey, PNG exports and one user flow per role ([`ui-prototypes.md`](docs/sprint2/ui-prototypes.md), [`figma/`](docs/sprint2/figma/))
- Screen list updated to OpenAPI v0.2.3 ([`ui-screens.md`](docs/ui-screens.md))
- US-18 appointment booking sequence diagram ([`US-18.md`](docs/architecture/sequence/US-18.md))
- Architecture reviews of the UML diagrams and the US-06 sequence diagram

**Issues**

- [#67](https://github.com/bookademic-team/Bookademic/issues/67) Figma prototypes and user-flow diagrams
- Shared: [#68](https://github.com/bookademic-team/Bookademic/issues/68) Sequence diagrams for owned stories

**Pull requests (authored, merged)**

- [#91](https://github.com/bookademic-team/Bookademic/pull/91) docs(sequence): add US-18 sequence diagram (#68)
- [#92](https://github.com/bookademic-team/Bookademic/pull/92) docs(sprint2): add ui-prototypes skeleton (Refs #67)
- [#97](https://github.com/bookademic-team/Bookademic/pull/97) docs(figma): rename frames, remove "/" from names, re-export PNGs (#67)
- [#98](https://github.com/bookademic-team/Bookademic/pull/98) docs(ui-screens): update OpenAPI references to v0.2.3 (#67)

**Reviews given**

- [#74](https://github.com/bookademic-team/Bookademic/pull/74) (changes requested → approved), [#87](https://github.com/bookademic-team/Bookademic/pull/87) (changes requested → approved)

## Sprint 1 – Project Foundation

Release: `v0.1.0-sprint1` · Counts cover the `develop` branch up to the Sprint 1 release.

### Summary

| Member | Student no. | GitHub | Role | Commits | PRs merged | Reviews given |
|---|---|---|---|:-:|:-:|:-:|
| Melih Adıgüzel | 230204060 | [@melihdgzl](https://github.com/melihdgzl) | Product Owner & Requirements Engineer | 2 | 2 | 6 |
| Sıla Yıldırım | 220201015 | [@silayildrm06-cell](https://github.com/silayildrm06-cell) | Scrum Master & Agile Process Coach | 8 | 3 | 2 |
| Mürsel Debbağ | 230204037 | [@murseldbbg](https://github.com/murseldbbg) | Lead Software Architect & Technical Lead | 9 | 3 | 4 |
| Muhammed Mirac Kocadağ | 220201033 | [@muhammedkocadag](https://github.com/muhammedkocadag) | Quality Assurance & Test Automation Engineer | 4 | 4 | 4 |
| Mehmet Çalışkan | 230204011 | [@Mhmt-Clskn](https://github.com/Mhmt-Clskn) | DevOps & CI/CD Infrastructure Engineer | 4 | 3 | 2 |
| Emir Altunbulak | 220205048 | [@emiraltunbulak-dotcom](https://github.com/emiraltunbulak-dotcom) | Implementation & Integration Engineer | 5 | 3 | 3 |

### Melih Adıgüzel

- **Student number:** 230204060
- **GitHub:** [@melihdgzl](https://github.com/melihdgzl)
- **Role:** Product Owner & Requirements Engineer
- **Responsibilities:** Product vision, requirements engineering, backlog prioritisation and stakeholder validation.

**Sprint 1 work**

- Project proposal and problem definition, including the MIT license rationale ([`proposal.md`](docs/sprint1/proposal.md))
- System vision and boundary with the context diagram ([`vision.md`](docs/sprint1/vision.md))
- Initial product backlog: 10 epics, 21 features, 23 user stories, 4 enablers, 95 points ([`backlog.md`](docs/sprint1/backlog.md))
- Student survey and analysis plan ([`survey.md`](docs/sprint1/survey.md)); Issue source and sync script ([`issues.csv`](docs/sprint1/issues.csv), [`sync_issues.py`](docs/sprint1/sync_issues.py))

**Issues**

- Sprint 1: [#34](https://github.com/bookademic-team/Bookademic/issues/34) Product docs: Sprint 1 fixes and English translation, [#52](https://github.com/bookademic-team/Bookademic/issues/52) Product docs: survey wording, feature level and table fixes
- Assigned stories (implemented from Sprint 3): [US-11](https://github.com/bookademic-team/Bookademic/issues/15), [US-13](https://github.com/bookademic-team/Bookademic/issues/17), [US-14](https://github.com/bookademic-team/Bookademic/issues/18)

**Pull requests (authored, merged)**

- [#38](https://github.com/bookademic-team/Bookademic/pull/38) Sprint 1 product docs: proposal, vision, backlog, survey (#34)
- [#53](https://github.com/bookademic-team/Bookademic/pull/53) docs(sprint1): fix product docs (#52)

**Reviews given**

- [#30](https://github.com/bookademic-team/Bookademic/pull/30) (approved), [#36](https://github.com/bookademic-team/Bookademic/pull/36) (approved), [#37](https://github.com/bookademic-team/Bookademic/pull/37) (approved), [#42](https://github.com/bookademic-team/Bookademic/pull/42) (approved), [#50](https://github.com/bookademic-team/Bookademic/pull/50) (approved), [#51](https://github.com/bookademic-team/Bookademic/pull/51) (approved)

**Commits**

- [`2d9341f`](https://github.com/bookademic-team/Bookademic/commit/2d9341f26dc52c10ae62c810c018262f87f68153) docs(sprint1): fix product docs (#52) (2026-11-05)
- [`225ecf2`](https://github.com/bookademic-team/Bookademic/commit/225ecf21f41c037b97bd0c3366c67dc93dee5c46) docs(sprint1): add product docs (#34) (2026-11-03)

### Sıla Yıldırım

- **Student number:** 220201015
- **GitHub:** [@silayildrm06-cell](https://github.com/silayildrm06-cell)
- **Role:** Scrum Master & Agile Process Coach
- **Responsibilities:** Agile process, team communication, impediment tracking, project board, Git-Flow rules and submission coordination.

**Sprint 1 work**

- Team roles and RACI matrix ([`raci.md`](docs/sprint1/raci.md))
- Git-Flow and repository workflow rules ([`git-flow.md`](docs/sprint1/git-flow.md))
- Meeting notes and decision record ([`docs/meetings/`](docs/meetings/))
- Project README; review plan; cross-check of all Sprint 1 documents and correction tracking
- Individual contributions record (this file); GitHub Projects board; Sprint 1 release review and submission

**Issues**

- Sprint 1: [#28](https://github.com/bookademic-team/Bookademic/issues/28) Add team roles and RACI matrix, [#43](https://github.com/bookademic-team/Bookademic/issues/43) docs: Scrum Master Sprint 1 docs (meeting notes, RACI, README), [#46](https://github.com/bookademic-team/Bookademic/issues/46) docs: move meeting notes and RACI into docs/, [#56](https://github.com/bookademic-team/Bookademic/issues/56) docs: add Git-Flow rules and CONTRIBUTIONS.md for Sprint 1 submission
- Assigned stories (implemented from Sprint 3): [US-05](https://github.com/bookademic-team/Bookademic/issues/9), [US-09](https://github.com/bookademic-team/Bookademic/issues/13), [US-12](https://github.com/bookademic-team/Bookademic/issues/16), [US-16](https://github.com/bookademic-team/Bookademic/issues/20), [US-23](https://github.com/bookademic-team/Bookademic/issues/48)

**Pull requests (authored, merged)**

- [#29](https://github.com/bookademic-team/Bookademic/pull/29) docs: add team roles and RACI matrix
- [#44](https://github.com/bookademic-team/Bookademic/pull/44) Sprint 1 Scrum Master docs: meeting notes, RACI update, README (#43)
- [#47](https://github.com/bookademic-team/Bookademic/pull/47) docs: move meeting notes and RACI into docs/ (#46)

**Reviews given**

- [#33](https://github.com/bookademic-team/Bookademic/pull/33) (approved), [#40](https://github.com/bookademic-team/Bookademic/pull/40) (approved)

**Commits**

- [`0980030`](https://github.com/bookademic-team/Bookademic/commit/09800304d84eace2218deeb891e5e6e39f602e63) docs: remove duplicate raci.md from root (#46) (2026-11-04)
- [`874ad79`](https://github.com/bookademic-team/Bookademic/commit/874ad790fdf72fc27a43d364f870bcc05cbf8e1f) docs(raci): add US-23 and US-07, align ADR row (#46) (2026-11-04)
- [`492ec4f`](https://github.com/bookademic-team/Bookademic/commit/492ec4fd11c30f027e349461569bc4c1c11efb08) docs(meetings): move 31 Oct planning notes to docs/meetings (#46) (2026-11-04)
- [`e9e7de2`](https://github.com/bookademic-team/Bookademic/commit/e9e7de2586910956ba7334076687ee9ea1076161) docs(meetings): move Sprint 1 alignment record to docs/meetings (#46) (2026-11-04)
- [`324fa02`](https://github.com/bookademic-team/Bookademic/commit/324fa028572cd26fe6ee73c430f953e06429cbe0) docs:Sprint 1 meeting notes, RACI and README (#43) (2026-11-04)
- [`4d8f359`](https://github.com/bookademic-team/Bookademic/commit/4d8f359201fc3d44faff5dd42a2b2bd8f9f0b7d4) docs: Sprint 1 meeting notes,RACI and README (#43) (2026-11-04)
- [`c18ce5d`](https://github.com/bookademic-team/Bookademic/commit/c18ce5df7df8ac4c34a508b1a8bf259f0201ade3) docs: move RACI matrix to docs/sprint1 (#28) (2026-11-03)
- [`4555d15`](https://github.com/bookademic-team/Bookademic/commit/4555d155b6ed0fac6ac44839dfc254e8750dd876) docs: add team roles and RACI matrix (#28) (2026-11-03)

### Mürsel Debbağ

- **Student number:** 230204037
- **GitHub:** [@murseldbbg](https://github.com/murseldbbg)
- **Role:** Lead Software Architect & Technical Lead
- **Responsibilities:** System and software architecture, technology choices and code-review leadership.

**Sprint 1 work**

- OpenAPI 3.1 contract v0.2.2: 35 operations, RFC 9457 error model ([`openapi.yaml`](docs/api/openapi.yaml))
- Story ↔ API traceability ([`story-mapping.md`](docs/api/story-mapping.md))
- ADR-0001 technology stack decision, accepted ([`0001-tech-stack.md`](docs/adr/0001-tech-stack.md))

**Issues**

- Sprint 1: [#32](https://github.com/bookademic-team/Bookademic/issues/32) ADR 0001: Technology stack decision, [#39](https://github.com/bookademic-team/Bookademic/issues/39) Align OpenAPI and ADR with Sprint 1 decisions (US-23, ADR accepted)
- Technical enablers: [EN-02](https://github.com/bookademic-team/Bookademic/issues/2) (done)
- Assigned stories (implemented from Sprint 3): [US-06](https://github.com/bookademic-team/Bookademic/issues/10), [US-08](https://github.com/bookademic-team/Bookademic/issues/12)

**Pull requests (authored, merged)**

- [#31](https://github.com/bookademic-team/Bookademic/pull/31) EN-02: Add OpenAPI contract v0.2.2 and story mapping
- [#33](https://github.com/bookademic-team/Bookademic/pull/33) ADR 0001: Add technology stack decision
- [#40](https://github.com/bookademic-team/Bookademic/pull/40) Align OpenAPI and ADR with Sprint 1 decisions (US-23, ADR accepted)

**Reviews given**

- [#29](https://github.com/bookademic-team/Bookademic/pull/29) (approved), [#42](https://github.com/bookademic-team/Bookademic/pull/42) (approved), [#45](https://github.com/bookademic-team/Bookademic/pull/45) (approved), [#50](https://github.com/bookademic-team/Bookademic/pull/50) (approved)

**Commits**

- [`c049969`](https://github.com/bookademic-team/Bookademic/commit/c049969691728b9fbd7dcc7723ac51176b5a00e2) Update openapi.yaml (2026-11-04)
- [`9cecdab`](https://github.com/bookademic-team/Bookademic/commit/9cecdab5c51a10a4c35508088ae84744e8e0e212) Update 0001-tech-stack.md (2026-11-04)
- [`df92544`](https://github.com/bookademic-team/Bookademic/commit/df92544a55cda36ca26ac8f7ab2d9eb469a04b09) docs(api): remove stray blank line in story mapping (#39) (2026-11-04)
- [`dcd3866`](https://github.com/bookademic-team/Bookademic/commit/dcd38662b9908c39783437662ec2412ae6e48d89) docs(api): name e-mail story US-23 in story mapping (#39) (2026-11-04)
- [`a2d8ccb`](https://github.com/bookademic-team/Bookademic/commit/a2d8ccb88562f14e6866b73c525b21bd3cb80317) docs(api): name e-mail story US-23 in story mapping (#39) (2026-11-04)
- [`6c4404f`](https://github.com/bookademic-team/Bookademic/commit/6c4404f22485e38b2be5201a9594004c6e63a83e) docs(api): reference US-23 in notifications description (#39) (2026-11-04)
- [`81f0a94`](https://github.com/bookademic-team/Bookademic/commit/81f0a940731006b408e2471cbe18e99e0ff94c0e) docs(adr): mark ADR-0001 as accepted (#39) (2026-11-04)
- [`c3f540b`](https://github.com/bookademic-team/Bookademic/commit/c3f540bd3e1f466055aca0481936326efdfca1cd) docs(adr): add technology stack decision (#32) (2026-11-03)
- [`1e70247`](https://github.com/bookademic-team/Bookademic/commit/1e702474f7c65dc92d5bf98aec2fc81997bd7978) docs(api): add OpenAPI contract v0.2.2 (2026-11-03)

### Muhammed Mirac Kocadağ

- **Student number:** 220201033
- **GitHub:** [@muhammedkocadag](https://github.com/muhammedkocadag)
- **Role:** Quality Assurance & Test Automation Engineer
- **Responsibilities:** Quality assurance, testability of acceptance criteria, test planning and automation.

**Sprint 1 work**

- Sprint 1 test plan and QA strategy ([`test-plan.md`](docs/test-plan.md))
- Testability review of acceptance criteria; alignment of the test scope with the backlog

**Issues**

- Assigned stories (implemented from Sprint 3): [US-07](https://github.com/bookademic-team/Bookademic/issues/11), [US-15](https://github.com/bookademic-team/Bookademic/issues/19), [US-20](https://github.com/bookademic-team/Bookademic/issues/24)

**Pull requests (authored, merged)**

- [#30](https://github.com/bookademic-team/Bookademic/pull/30) docs(qa): Sprint 1 Test Plan and Quality Strategy
- [#36](https://github.com/bookademic-team/Bookademic/pull/36) docs(qa): translate test-plan.md to English
- [#37](https://github.com/bookademic-team/Bookademic/pull/37) docs(qa): align test plan with US-07 and temporal boundaries
- [#51](https://github.com/bookademic-team/Bookademic/pull/51) docs(qa): restore docs/test-plan.md with US-07 alignment

**Reviews given**

- [#31](https://github.com/bookademic-team/Bookademic/pull/31) (approved), [#33](https://github.com/bookademic-team/Bookademic/pull/33) (approved), [#49](https://github.com/bookademic-team/Bookademic/pull/49) (approved), [#54](https://github.com/bookademic-team/Bookademic/pull/54) (approved)

**Commits**

- [`ee4120b`](https://github.com/bookademic-team/Bookademic/commit/ee4120b55e760aa36a96182ba2c452c158997bba) docs(qa): restore docs/test-plan.md with US-07 alignment (2026-11-04)
- [`28cd699`](https://github.com/bookademic-team/Bookademic/commit/28cd699e5101c59901a35bf2333a6321bb8084f0) docs(qa): align test plan with US-07 and temporal boundaries (2026-11-03)
- [`795b9d3`](https://github.com/bookademic-team/Bookademic/commit/795b9d3dd99e530a0dcd31f0df5084ea69b551b3) docs(qa): translate test plan and qa strategy to English (2026-11-03)
- [`a2a31db`](https://github.com/bookademic-team/Bookademic/commit/a2a31db28300e8cd3d39b63cf6f8cda2433613c8) docs(qa): add comprehensive sprint 1 test strategy and plan (2026-11-03)

### Mehmet Çalışkan

- **Student number:** 230204011
- **GitHub:** [@Mhmt-Clskn](https://github.com/Mhmt-Clskn)
- **Role:** DevOps & CI/CD Infrastructure Engineer
- **Responsibilities:** GitHub organization and repository, branch protection, Docker environment, CI/CD and releases.

**Sprint 1 work**

- GitHub organization `bookademic-team`, repository, `main`/`develop` branch protection, labels
- docker-compose skeleton aligned with ADR-0001 ([`docker-compose.yml`](docker-compose.yml), [`.env.example`](.env.example)); [`LICENSE`](LICENSE)
- Issue synchronisation with the backlog; Sprint 1 release pull request and tag

**Issues**

- Technical enablers: [EN-01](https://github.com/bookademic-team/Bookademic/issues/1), [EN-03](https://github.com/bookademic-team/Bookademic/issues/3), [EN-04](https://github.com/bookademic-team/Bookademic/issues/4)
- Assigned stories (implemented from Sprint 3): [US-01](https://github.com/bookademic-team/Bookademic/issues/5), [US-02](https://github.com/bookademic-team/Bookademic/issues/6), [US-03](https://github.com/bookademic-team/Bookademic/issues/7), [US-04](https://github.com/bookademic-team/Bookademic/issues/8)

**Pull requests (authored, merged)**

- [#27](https://github.com/bookademic-team/Bookademic/pull/27) feat(devops): add docker-compose skeleton for local development
- [#49](https://github.com/bookademic-team/Bookademic/pull/49) fix(devops): update compose services and license
- [#54](https://github.com/bookademic-team/Bookademic/pull/54) fix(devops): rename env.example to .env.example

**Reviews given**

- [#44](https://github.com/bookademic-team/Bookademic/pull/44) (approved), [#47](https://github.com/bookademic-team/Bookademic/pull/47) (approved)

**Commits**

- [`715c041`](https://github.com/bookademic-team/Bookademic/commit/715c04173b5acf4fe4eef7a8cf2729839a3bcf5f) Added the missing dot to the env.example filename as requested. (2026-11-05)
- [`16d4947`](https://github.com/bookademic-team/Bookademic/commit/16d49475ee206e0e9c5304b469f10a0ecdb6a4eb) fix(devops): update compose services and license. (2026-11-04)
- [`f09f441`](https://github.com/bookademic-team/Bookademic/commit/f09f4411407fbefca44add6e27768ae7ccaae097) feat(devops): add docker-compose skeleton for local development (2026-11-02)
- [`bc02531`](https://github.com/bookademic-team/Bookademic/commit/bc02531d0629e2c639546dbcd053baf6de8f75d7) Initial commit (2026-11-02)

### Emir Altunbulak

- **Student number:** 220205048
- **GitHub:** [@emiraltunbulak-dotcom](https://github.com/emiraltunbulak-dotcom)
- **Role:** Implementation & Integration Engineer
- **Responsibilities:** Implementation and integration across modules, frontend skeleton, M4 contribution to the API.

**Sprint 1 work**

- Screen list, role–screen matrix and main user-journey flow ([`ui-screens.md`](docs/ui-screens.md))
- M4 endpoint review and error-to-message mapping for the frontend

**Issues**

- Sprint 1: [#41](https://github.com/bookademic-team/Bookademic/issues/41) Add screen list and screen flow
- Assigned stories (implemented from Sprint 3): [US-10](https://github.com/bookademic-team/Bookademic/issues/14), [US-17](https://github.com/bookademic-team/Bookademic/issues/21), [US-18](https://github.com/bookademic-team/Bookademic/issues/22), [US-19](https://github.com/bookademic-team/Bookademic/issues/23), [US-21](https://github.com/bookademic-team/Bookademic/issues/25), [US-22](https://github.com/bookademic-team/Bookademic/issues/26)

**Pull requests (authored, merged)**

- [#42](https://github.com/bookademic-team/Bookademic/pull/42) docs(ui): add screen list and flow
- [#45](https://github.com/bookademic-team/Bookademic/pull/45) Update ui-screens.md
- [#50](https://github.com/bookademic-team/Bookademic/pull/50) docs(ui): fix module names, sprint1 refs, SCR-01 errors, daily limit wording

**Reviews given**

- [#27](https://github.com/bookademic-team/Bookademic/pull/27) (approved), [#38](https://github.com/bookademic-team/Bookademic/pull/38) (approved), [#53](https://github.com/bookademic-team/Bookademic/pull/53) (approved)

**Commits**

- [`71d24fb`](https://github.com/bookademic-team/Bookademic/commit/71d24fb85fd5987fe702033f9befe94151781848) docs(ui): align ui-screens with module names, sprint1 refs and error rules (2026-11-04)
- [`3e9a39e`](https://github.com/bookademic-team/Bookademic/commit/3e9a39ea48c91474e449e5c84f92e77f7a45248c) docs(ui): align ui-screens with module names, sprint1 refs and error rules (2026-11-04)
- [`859b9f4`](https://github.com/bookademic-team/Bookademic/commit/859b9f48eec542d40783f9fdb2cd16e94eaf3960) docs(ui): align ui-screens with module names, sprint1 refs and error rules (2026-11-04)
- [`0ce18db`](https://github.com/bookademic-team/Bookademic/commit/0ce18db9053e9590d49a5a8b0aae580a54852bc9) Update ui-screens.md (2026-11-04)
- [`71cb27a`](https://github.com/bookademic-team/Bookademic/commit/71cb27a6f02c6016565ca0b81e6c70f011b7754c) docs(ui): add screen list and flow (2026-11-04)
