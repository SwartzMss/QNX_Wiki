---
name: qnx-llm-wiki
description: "Use when the user asks to initialize, ingest, query, or lint this project's QNX LLM wiki using its existing raw/ HTML archive."
---

# QNX LLM Wiki

Adapted from [Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki) (MIT license). Build and maintain a QNX knowledge base using the existing `raw/` HTML archive and a separately compiled `wiki/`. The user chooses when and which sources to compile. Do not create wiki pages merely because this skill was installed or read.

Core ideas from Karpathy:
- "The LLM writes and maintains the wiki; the human reads and asks questions."
- "The wiki is a persistent, compounding artifact."

## Architecture

Three layers, all under the user's project root:

**raw/** — Immutable source material. In this project, `raw/qnx-sdp-8.0/` contains official HTML pages and images. `raw/manifest.tsv` maps every file to its official URL and SHA-256. Read these files; never rewrite, rename, or convert them in place. `raw/README.md` is the collection overview.

**wiki/** — Compiled knowledge articles. You have full ownership. Organized by topic subdirectories, one level only: `wiki/<topic>/<article>.md`. Contains two special files:
- `wiki/index.md` — Global index. One row per article, grouped by topic, with link + summary + Updated date.
- `wiki/log.md` — Append-only operation log.

**SKILL.md** (this file) — Schema layer. Defines structure and workflow rules. The source archive already exists; this skill does not imply that all archived pages have been ingested.

Templates live in `references/` relative to this file. Read them when you need the exact format for raw files, articles, archive pages, or the index.

## Init

When the user asks to initialize the wiki, check that `raw/` and `raw/manifest.tsv` exist, then create only missing wiki structure:

- `wiki/index.md` — heading `# Knowledge Base Index`, empty body
- `wiki/log.md` — heading `# Wiki Log`, empty body

Do not overwrite existing files, ingest sources, create topic articles, or alter `raw/`. Repeated Init is a no-op when both files already exist. If one file exists, create only the missing file; report the resulting state. When a new `wiki/log.md` is created, append `## [YYYY-MM-DD] init | wiki initialized` once. An explicit Ingest may perform this same initialization first if Init has not been run.

If Query or Lint cannot find the wiki structure, tell the user to run Init or Ingest. Query on an initialized but empty wiki reports that no knowledge has been ingested yet; it does not create articles.

## The Grounding Invariant

Every load-bearing fact in wiki/ — numbers, dates, direct quotes — must be supported by the visible body of the HTML or Markdown files linked by that article's Raw field. Compile *establishes* this invariant (locate before you write); lint *checks candidates* with `scripts/check_evidence.py`. The script extracts visible HTML `<article>` text and excludes navigation; its output is a report to review, not proof that every statement is correct. Use the official URL from `raw/manifest.tsv` near technical claims so readers can check the exact QNX page.

---

## Ingest

Ensure Init has run, then read an existing QNX source in raw/ and compile it into wiki/ only if it adds something useful. Fetch only newly supplied sources. Ingest a user-selected page or bounded chapter set; do not treat the 730 archived HTML pages as one automatic batch. Whether to compile depends on the triage below.

### Fetch (raw/)

1. If the user names an existing path under `raw/qnx-sdp-8.0/`, read that file in place. Confirm it appears in `raw/manifest.tsv` and use the listed official URL. Do not fetch, copy, or convert an existing HTML page. Inspect referenced images when the claim depends on a diagram. If the user supplies a new URL or file outside raw/, acquire it using available tools.

2. For a new source only, pick a topic directory. Check existing `raw/` subdirectories first; reuse one if appropriate. Create a new subdirectory only for a genuinely distinct topic.

3. For a new source only, save as `raw/<topic>/YYYY-MM-DD-descriptive-slug.md`.
   - Slug from source title, kebab-case, max 60 characters.
   - Published date unknown → omit the date prefix from the file name (e.g., `descriptive-slug.md`). The metadata Published field still appears; set it to `Unknown`.
   - If a file with the same name already exists, append a numeric suffix (e.g., `descriptive-slug-2.md`).
   - Include metadata header: source URL, collected date, published date.
   - Preserve original text. Clean formatting noise. Do not rewrite opinions.

   See `references/raw-template.md` for the exact format. The existing QNX HTML archive keeps its original filenames and format; this template does not apply to it.

### Triage

After saving the raw file and before editing wiki/, search wiki/ with the source's key entities and synonyms, then state the disposition:

- **New** — creates one or more new articles.
- **Update** — merges into existing article(s).
- **Disputed** — contradicts existing content; may combine with New or Update (see Compile for conflict annotation).
- **No material** — adds no knowledge beyond what the wiki already holds. Keep the raw file, log it (see Post-Ingest), and stop. Do not force an article out of a thin source.

New, Update, and Disputed may be combined. No material is exclusive.

### Compile (wiki/)

Determine where the new content belongs:

- **Same core thesis as existing article** → Merge into that article. Add the new source to Sources/Raw. Update affected sections.
- **New concept** → Create a new article in the most relevant topic directory. Name the file after the concept, not the raw file.
- **Spans multiple topics** → Place in the most relevant directory. Add See Also cross-references to related articles elsewhere.

These are not mutually exclusive. A single source may warrant merging into one article while also creating a separate article for a distinct concept it introduces. In all cases, check for factual conflicts: if the new source contradicts existing content, mark the contested claims with a **Status: Disputed** block (see `references/article-template.md`). When the conflicting content lives in separate articles, mark both and cross-link them.

**Source fidelity.** Every number, date, and direct quote must be located in the raw file (grep or read) *before* it is written; write the value exactly as found — if the source says 42K, write 42K, not 42,000. Derived values (sums, deltas, counts you computed) must show their components so each component is findable in raw. If you cannot locate a value, do not write its exact form; drop it or state it without precision.

See `references/article-template.md` for article format. Key points:
- Sources field: author, organization, or publication name + date, semicolon-separated.
- Raw field: markdown links to the exact raw files, semicolon-separated. Existing QNX links end in `.html`; new markdown sources end in `.md`.
- Relative paths from `wiki/<topic>/` use `../../raw/<collection>/<guide>/<file>.html` for this archive (two levels up to project root).

### Cascade Updates

After the primary article, check for ripple effects. Do not rely on the index alone: search the full wiki for the source's key entities, aliases, and the claims it touches, then update every non-archive article whose content is materially affected. Each updated file gets its Updated date refreshed.

When the new source supersedes or contradicts an existing claim, keep the old claim for the record but mark it with a Status block (see `references/article-template.md`): **Outdated** when something newer replaces it, **Disputed** when sources disagree. Never silently rewrite history.

Archive pages are never cascade-updated (they are point-in-time snapshots).

### Post-Ingest

Update `wiki/index.md`: add or update entries for every touched article. When adding a new topic section, include a one-line description. The Updated date reflects when the article's knowledge content last changed, not the file system timestamp. See `references/index-template.md` for format.

Append to `wiki/log.md`:

```
## [YYYY-MM-DD] ingest | <primary article title>
- Disposition: <New; Update; Disputed>
- Raw: <raw file path>
- Updated: <cascade-updated article title>
```

Omit `- Updated:` lines when no cascade updates occur. For No material, log and stop. Use a project-root-relative raw path (for example, `raw/topic/file.md`):

```
## [YYYY-MM-DD] ingest | no material: <project-root-relative raw file path>
- Disposition: No material
```

The exact no-material heading is the machine-readable inventory key; the Disposition line remains required for a complete human-readable log entry.

### Check This Ingest

Before finishing each Ingest that creates or updates articles, automatically check every touched article, including cascade updates. Verify its `Raw` paths, wiki links, index entry, and `Updated` date. Run `scripts/check_evidence.py` with those article paths and inspect the reported fidelity suspects and evidence errors against the cited raw text. Fix safe structural mistakes and factual mistakes with clear source evidence; report unresolved questions. The script also prints a global unreferenced-raw inventory; treat that as backlog, not as a failure of this ingest. Summarize the check in the response without adding a separate Lint log entry. A No material ingest only needs its required log entry and no article check.

This scoped check is part of Ingest. The full-wiki Lint below remains available on request for cross-article consistency and maintenance.

### Research (multi-source ingest)

Use only when the user explicitly asks to research a topic or gather sources into the wiki. Ordinary knowledge questions go to Query, which never writes files.

1. Split the topic into a few angles. For each, search with a wide net — official names, abbreviations, and synonyms, not just the literal keywords.
2. For any core claim you expect to conclude, deliberately search the opposing side: failures, criticism, failed replications.
3. Save selected sources to raw/ as usual. Searching may run in parallel; compilation must not — compile one source at a time, because index.md, log.md, and cascade updates are shared state.

---

## Query

Search the wiki and answer questions. Examples of triggers:
- "What do I know about X?"
- "Summarize everything related to Y"
- "Compare A and B based on my wiki"

### Steps

1. Read `wiki/index.md` to locate candidate articles, then full-text search wiki/ with the topic's key terms *and their synonyms*. Never claim the wiki has no relevant content until both the index and the full-text search come back empty — and say that you searched.
2. Read the articles you found and synthesize an answer.
3. Prefer wiki content over your own training knowledge. Cite sources with markdown links: `[Article Title](wiki/topic/article.md)` (project-root-relative paths for in-conversation citations; within wiki/ files, use paths relative to the current file).
4. Output the answer in the conversation. Do not write files unless asked.

### Archiving

When the user explicitly asks to archive or save the answer to the wiki:

1. Write the answer as a new wiki page. See `references/archive-template.md`. When converting conversation citations to the archive page, rewrite project-root-relative paths (e.g., `wiki/topic/article.md`) to file-relative paths (e.g., `../topic/article.md` or `article.md` for same-directory).
   - Sources: markdown links to the wiki articles cited in the answer.
   - No Raw field (content does not come from raw/).
   - File name reflects the query topic, e.g., `transformer-architectures-overview.md`.
   - Place in the most relevant topic directory.
2. Always create a new page. Never merge into existing articles (archive content is a synthesized answer, not raw material).
3. Update `wiki/index.md`. Prefix the Summary with `[Archived]`.
4. Append to `wiki/log.md`:
   ```
   ## [YYYY-MM-DD] query | Archived: <page title>
   ```

---

## Lint

Quality checks on the wiki. Three categories with different authority levels.

### Safe Fixes (auto-fix)

Fix these automatically:

**Index consistency** — compare `wiki/index.md` against actual wiki/ files (excluding index.md and log.md):
- File exists but missing from index → add entry with `(no summary)` placeholder. For Updated, use the article's metadata Updated date if present (for archive pages, the Archived date); otherwise fall back to file's last modified date.
- Index entry points to nonexistent file → mark as `[MISSING]` in the index. Do not delete the entry; let the user decide.
- Index entry's Updated differs from the article's metadata Updated (or Archived, for archive pages) → update the index entry to match the article.

**Internal links** — for every markdown link in wiki/ article files (body text and Sources metadata), excluding Raw field links (validated by Raw references below), excluding See Also section links (handled by the See Also rule below), and excluding index.md/log.md (handled above):
- Target does not exist → search wiki/ for a file with the same name elsewhere.
  - Exactly one match → fix the path.
  - Zero or multiple matches → report to the user.

**Raw references** — every link in a Raw field must point to an existing raw/ file:
- Target does not exist → search raw/ for a file with the same name elsewhere.
  - Exactly one match → fix the path.
  - Zero or multiple matches → report to the user.

**See Also** — within each topic directory:
- Target of a See Also link does not exist → search wiki/ for a file with the same name elsewhere.
  - Exactly one match → fix the path.
  - Zero matches → remove the link (a dead cross-reference is not load-bearing).
  - Multiple matches → report to the user.

### Mechanical Reports (no fixes)

Run these mechanically with `python3 <skill-dir>/scripts/check_evidence.py <project-root>` (optionally followed by project-root-relative article paths to limit scope). Default scope is the whole wiki. It accepts `.md`, `.html`, and `.htm` Raw links; the raw inventory reports HTML pages but not images or `raw/README.md`, and shows only the first 30 unreferenced files. Report findings; never auto-fix facts.

**Source fidelity** — reported suspects are candidates, not verdicts: derived values and product names may appear. Judge each against the raw context and report only real mismatches.

**Evidence errors** — articles the script cannot verify (missing Raw field, unresolvable Raw links, or Raw links escaping `raw/`). These always need a decision, not a fix from the script.

**Unreferenced raw files** — files logged with a No material disposition are excluded; everything else is a genuine backlog reminder.

### Judgment Reports (no fixes)

These rely on your judgment. Report findings without auto-fixing:

- Factual contradictions across articles
- Outdated claims superseded by newer sources but still presented without a Status block
- Missing conflict annotations where sources disagree
- Obviously missing cross-references between related articles (suggest them; do not add silently)
- Malformed Status blocks (Outdated missing its date, or either block missing its explanation; format reference: `references/article-template.md`)
- Orphan pages with no inbound links from other wiki articles
- Missing cross-topic references
- Concepts frequently mentioned but lacking a dedicated page
- Archive pages whose cited source articles have been substantially updated since archival

### Post-Lint

Append to `wiki/log.md`:

```
## [YYYY-MM-DD] lint | <N> issues found, <M> auto-fixed
```

---

## Conventions

- Standard markdown with relative links throughout.
- wiki/ supports one level of topic subdirectories only. No deeper nesting.
- Today's date for log entries, Collected dates, and Archived dates. Updated dates reflect when the article's knowledge content last changed. Published dates come from the source (use `Unknown` when unavailable).
- Inside wiki/ files, all markdown links use paths relative to the current file. In conversation output, use project-root-relative paths (e.g., `wiki/topic/article.md`).
- Init creates only missing wiki scaffolding. Ingest updates both `wiki/index.md` and `wiki/log.md` (a No material ingest updates only the log) and checks touched articles before finishing. Archive (from Query) updates both. Full Lint updates `wiki/log.md` (and `wiki/index.md` only when auto-fixing index entries). Plain queries do not write any files.
