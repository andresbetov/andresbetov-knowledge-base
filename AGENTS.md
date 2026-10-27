# AGENTS.md

Agent contract for this docs-only overview knowledge base. The human entrypoint is [README.md](README.md).

## 1. What this repo is

- Public portfolio plus AI-agent context.
- Overview only without duplicated implementation detail.
- Details live in source repos linked from each page.
- Audience is reviewers and agents seeking orientation.

## 2. Source-of-truth hierarchy

- On conflict, frontmatter `source_of_truth` wins; KB is overview-only.
- `source_of_truth` names the authoritative repository; the Source Map path and citation name the exact artifact.
- Source repos outrank this KB for numbers and behavior.
- Never invent numbers or dates or statuses.
- Section 5 of project pages allows zero TBD entries.
- Elsewhere use `TBD (unverified, see Source Map)` and keep it visible.

## 3. Navigation

- Start at [README.md](README.md) and open the project page by slug.
- Follow Source Map rows and inline citations outward to the source repositories.
- `source_of_truth` is the repository authority; the Source Map path and inline citation identify the exact artifact.
- Do not bypass the source repositories for facts.

## 4. File contract

- Repository root holds README.md, AGENTS.md, LICENSE and .gitignore.
- Approved folders are projects/ and _templates/ only.
- Project pages live in projects/ with one file per slug.
- Root content pages are education.md, certificates.md, writing.md, engineering.md and skills.md.
- Project frontmatter keys in order: title / slug / type / status / version / repos / source_of_truth / stack / last_verified.
- Root content frontmatter keys: title, slug, last_verified.
- Filename must match frontmatter slug exactly.
- Project pages use the full template and keep 8-section order immutable.
- Sections 1 to 4 stay within 45 body lines total.
- Project pages and the template keep one blank line between blocks and before the final back link; root pages keep one blank line between blocks.
- Keep each project page at or under 148 lines by wc -l.
- Template changes bump the integer version in the template and all project pages in the same commit.
- `version` is the page-format version, not the project release.
- Templates are illustrative; freshness and TBD rules apply to content pages.

## 5. Content rules

- English only in all pages; quoted source titles keep their original language.
- Text only with no images and no diagrams and no fences.
- Use relative links inside KB.
- Use absolute https links for outward sources; localhost development URLs are exempt.
- Keep no absolute filesystem paths and no secrets.
- Keep no superlatives and no marketing claims.
- Cite repository-relative paths; for multi-repository projects prefix with the repository name.
- Keep bullets to one sentence where possible.
- Keep Reproduce to five steps max with inline backticks.

## 6. Freshness

- Set last_verified to the UTC date the page was checked against its sources.
- Mark pages older than 90 days with the exact banner `> Stale: last verified YYYY-MM-DD; refresh before reuse.`
- Refresh stale pages from source repositories before reuse.

## 7. Scope freeze

- Project pages cover three slugs only with no expansion.
- Education, certificates, writing, engineering and skills are the five root content pages.
- Reserved for later: experience/; adding it requires a contract update.
- Do not add folders beyond projects/ and _templates/.
