# Personal Site Design — al-folio on GitHub Pages

## Goal

Build a personal site (blog, projects, publications, research interests)
for Neeraj Gupta, hosted on GitHub Pages at `https://gneeraj97.github.io/`.
Explicitly excludes a resume/CV page — the site is about writing, projects,
and research interests, not a resume.

## Repository

- Repo name: `gneeraj97.github.io` (root user site, not a project subpath)
- Visibility: **private** for now, on GitHub. Publish (make public) later,
  once content is ready — GitHub Pages for a user-site repo requires the
  repo to be public on free plans, so going public is a precondition for
  the site actually being reachable, but is a separate decision from
  building it.
- Local repo already initialized at
  `/Users/neerajgupta/Desktop/Project/myBlog` with default branch `main`.

## Framework

**al-folio** (Jekyll theme for academics/researchers):
<https://github.com/alshedivat/al-folio>

Chosen over Astro/Hugo alternatives because it ships CV/publications/
projects/blog structure out of the box, matching this site's purpose
most directly, despite a heavier (Ruby/Jekyll) toolchain than Hugo.
Trade-off accepted knowingly (see brainstorming conversation).

## Pages/sections enabled

- **About** — short bio, research interests (LLM safety, agentic
  systems, NLP/security), contact links (GitHub, LinkedIn, Google
  Scholar). No downloadable resume/CV file.
- **Blog** (`_posts/`) — Markdown posts. Includes general writing and
  "notes on things I find interesting" (book excerpts, papers, etc.) as
  a blog category/tag rather than a separate collection — kept simple,
  add a dedicated collection later only if it's actually needed.
- **Projects** (`_projects/`) — card gallery of past and current work.
- **Publications** (`_bibliography/papers.bib`) — BibTeX entries for
  published academic papers (MedChat, the multimedia-detection survey).

## Pages/features explicitly dropped from al-folio defaults

- **CV page** — no resume/CV content on the site at all, per explicit
  request.
- **People page** — academic-group feature, not relevant for a solo
  site.
- **Repositories widget** (live "pinned GitHub repos" card) — overlaps
  with the Projects section; can be added later if wanted.

## Content policy — confidentiality toward employers

This is the most important constraint on Projects content:

- **No employer attribution.** Never name a past or current employer
  (BrowserStack, ShareChat, Indus Insights, SpecterOps, Purdue) in
  connection with a specific project's description.
- **No quantified business metrics.** Never include specific numbers
  tied to business impact — dollar figures, percentage improvements,
  user counts, latency numbers, cost savings, etc. — regardless of
  which employer they originated from.
- **Describe the technical work itself.** e.g. "Built a multi-agent LLM
  orchestration system for security analysis using LangGraph, FastAPI,
  and PostgreSQL" — what was built, what techniques/stack were used —
  with no company name and no numbers.
- **Exception: already-public open-source work.** Projects with a
  public GitHub repo and/or a public blog post already describing them
  (Jailbreaker, DeepPass2, CypherK9, Sniffr, DermRAG, the DeepSeek/
  Unsloth fine-tuning experiment) can link directly to those existing
  public artifacts as-is — this isn't new disclosure, just linking to
  what's already public. Metrics that appear in the *linked* public
  post/repo are not something this rule can or needs to scrub; the
  rule applies to prose written fresh for this site.
- **Proprietary/internal projects with no public repo** (e.g. BloodHound
  Talk, the Red Team Report Intelligence Platform) — described
  generically: what kind of system it was and the general techniques
  used, no name, no metrics, no identifying specifics beyond that.

This rule applies uniformly to all draft project write-ups I produce for
this site, sourced from `/Users/neerajgupta/Desktop/Job/resume/master_resume.json`
(used only as a private source of project facts — never linked, quoted
verbatim, or exposed on the site itself).

## Data flow

1. Content authored as Markdown (`_posts/`, `_projects/`) or BibTeX
   (`_bibliography/papers.bib`) files in the repo.
2. Commit and push to `main` on `gneeraj97.github.io` (GitHub, private
   for now).
3. GitHub Actions (al-folio's built-in deploy workflow) builds the
   Jekyll site.
4. Deployed to GitHub Pages — reachable at `https://gneeraj97.github.io/`
   once the repo is made public and Pages is enabled.

## Testing / verification

- Local preview via `bundle exec jekyll serve` (or al-folio's Docker
  setup) before pushing, to catch rendering issues.
- GitHub Actions build must pass (fails loudly on broken Liquid/config)
  before anything is considered "done."
- Manual check of each new page/post in the local preview: renders,
  links resolve, no stray template placeholders.

## Out of scope for this spec

- Actually making the repo public / going live (a separate decision,
  made later once content is ready).
- Custom visual theming beyond al-folio's defaults (may become a
  follow-up spec if wanted).
- Comments, analytics, search — not requested, not added speculatively.
