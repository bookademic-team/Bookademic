# Sprint 1 Retrospective

| Field | Value |
| --- | --- |
| Date | 16 November 2026 |
| Medium | Microsoft Teams (online) |
| Participants | Melih, Sıla, Mürsel, Mirac, Mehmet, Emir |
| Facilitator | Sıla (Scrum Master) |
| Sprint | Sprint 1 – Project Foundation, released as [`v0.1.0-sprint1`](https://github.com/bookademic-team/Bookademic/releases/tag/v0.1.0-sprint1) |

## Sprint 1 in numbers

| Measure | Value |
| --- | --- |
| Milestone | Sprint 1 closed, 20 of 20 issues done |
| Pull requests merged into `develop` | 19 (plus the release pull request into `main`) |
| Deliverables | 6 of 6 required deliverables submitted on 5 November |
| Every member | at least 2 merged pull requests and 2 reviews (see [CONTRIBUTIONS.md](../../CONTRIBUTIONS.md)) |

## What went well

- The team was **satisfied with the work overall**: all six deliverables were submitted on time, with a release tag, a project board and an individual contributions record.
- Every member contributed through their own Issues, commits, pull requests and reviews.
- Every change went through an Issue, a feature branch and a reviewed pull request, merged with a merge commit.

## What did not go well

**Main problem: everyone worked on everything at the same time.** Documents that depend on each other (backlog, test plan, OpenAPI, screen list) were written in parallel, so they used different story numbers and names and had to be corrected several times just before the deadline.

Other problems:

| Problem | Effect | Example |
| --- | --- | --- |
| Wrong issue number after `Closes` | An unrelated issue was closed | `Closes #7` (US-03) was written for US-07 (#11) in #37 and #51 |
| Review submitted as *Comment* instead of *Approve* | The pull request could not be merged | #40 stayed blocked |
| Files uploaded to the wrong folder | Duplicate RACI file, broken README links | Meeting notes and `raci.md` in the repository root (#43), fixed in #47 |
| A file deleted while editing in the browser | Test plan missing from `develop` | `docs/test-plan.md`, restored in #51 |
| Branch names outside the rule | Harder to trace branches to issues | `…-patch-1`, `feature/qa-test-plan` |
| Rendered text copied instead of raw Markdown | Headings and tables lost | `git-flow.md` first draft |
| Missing templates, wrong Git author name | No common PR format; commits under a Windows user name | EN-01 templates; `MC\Mehmet Çalışkan` |
| Most work finished in the last two days | Late-night corrections | 3–5 November |

## Action items for Sprint 2

| # | Action | Owner |
| --- | --- | --- |
| A1 | **Dependent work is done in order:** work others depend on is finished first; the people who depend on it review it while waiting and start after it is merged. In Sprint 2, Melih (SRS, backlog) and Mürsel (C4, UML, ERD) go first. | Melih, Mürsel; Sıla tracks |
| A2 | Check the issue number on the Issue page before writing `Closes #<no>` | Everyone |
| A3 | Submit reviews with **Approve** or **Request changes** and at least one concrete comment | Everyone |
| A4 | Create new files with **Add file → Create new file** and the full path (`docs/…`); copy Markdown from the raw file; check *Files changed* before merging | Everyone |
| A5 | Branch names `feature/<issue-no>-<short-name>` from the latest `develop` | Everyone |
| A6 | Issue and pull request templates; fix Git author name | Mehmet (#59) |
| A7 | Comment on #11 and change `Closes #7` to `Refs #11` in #37 and #51 | Mirac |
