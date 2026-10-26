---
title: "Example"
slug: project-template
type: backend-system
status: active
version: 1
repos: ["https://example.com/repo"]
source_of_truth: "https://example.com/docs"
stack: ["python", "sqlite"]
last_verified: 2026-01-15
---

# Example

## 1. Intent
This template describes one project in a fixed shape.
Use a slug matching the filename without extension.
Goal is TBD (unverified, see Source Map)
Scope covers purpose and users.

## 2. Non-Goals
- Not a tutorial for new tools.
- Not a record of meetings.
- Not a home for unverified claims.

## 3. Source Map
| Area | Repo/Path | Entrypoint |
| --- | --- | --- |
| API | https://example.com/repo | src/app.py:main |
| Docs | https://example.com/docs | docs/index.md:overview |
| Tests | https://example.com/repo | tests/test_app.py:suite |

## 4. System at a Glance
- Purpose is described in Intent above.
- Primary users are operators and reviewers.
- Main workflow starts at src/app.py:main.
- Data stays within the listed repo.

## 5. Key Numbers
No verified numbers yet — see Source Map.

## 6. Engineering Discipline
- Tests run with tests/test_app.py:suite.
- Review happens before each publish.
- Changes keep docs and code aligned.

## 7. Reproduce
1. Open `docs/index.md` and read overview.
2. Run `src/app.py` with sample input.
3. Check output against expected result.
4. Record method and date for any number.

## 8. Limitations
- Coverage is partial until first verification.
- Numbers are missing until measured.
- Scope excludes items listed in Non-Goals.

## 9. History
- Created from template.
- Intent drafted.
- Source Map added.
- Glance reviewed.
- Key Numbers checked.
- Discipline confirmed.
- Reproduce tried.
- Limitations noted.
No publish events yet.
