# AGENTS.md

Agent entrypoint for this docs-only overview knowledge base.

## 1. What this repo is

- Public portfolio plus AI-agent context.
- Overview only without duplicated implementation detail.
- Details live in source repos linked from each page.
- Audience is reviewers and agents seeking orientation.

## 2. Source-of-truth hierarchy

- On conflict, frontmatter `source_of_truth` wins; KB is overview-only.
- Source repos outrank this KB for numbers and behavior.
- Never invent numbers or dates or statuses.
- Section 5 of project pages allows zero TBD entries.
- Elsewhere use `TBD (unverified, see Source Map)` and keep it visible.

## 3. Navigation

- Start at root README project table.
- Open projects overview page by slug for detail.
- Follow Source Map outward to source repos.
- Do not bypass Source Map for facts.

## 4. File contract

- Flat layout under projects with one file per slug.
- Education lives in education.md at the repository root.
- Certificates live in certificates.md at the repository root.
- Writing lives in writing.md at the repository root.
- Engineering principles live in engineering.md at the repository root.
- Certificate rows carry result with scale and issue month and a verification link.
- Filename must match frontmatter slug exactly.
- Project pages use the full template; root content pages use title, slug and last_verified.
- Keep 8-section order immutable across project pages.
- Summary budget applies to Sections 1 to 4.
- Keep 9 blank lines per file for readability.
- Keep each project page at or under 148 lines by wc -l.
- Validate frontmatter on every edit.

## 5. Content rules

- English only in all pages.
- Text only with no images and no diagrams and no fences.
- Use relative links inside KB.
- Use absolute https links for outward sources.
- Keep no absolute filesystem paths and no secrets.
- Keep no superlatives and no marketing claims.
- Keep body lines under five commas each.
- Keep Reproduce to five steps max with inline backticks.

## 6. Freshness

- Set last_verified to YYYY-MM-DD of UTC merge date.
- Use merge date and not author date.
- Mark pages older than 90 days as stale with banner.
- Refresh stale pages from source repos before reuse.

## 7. Scope freeze

- Project pages cover three slugs only with no expansion.
- Education, certificates, writing and engineering are the four root-level content pages.
- Reserved for later: experience/.
- Template changes require minor version bump.
- Do not add folders beyond agreed scope.
