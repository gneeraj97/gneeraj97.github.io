# Personal Site (al-folio) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Stand up a personal site (about, blog, projects, publications) at
`gneeraj97.github.io`, built on the al-folio Jekyll theme, with a private
GitHub repo ready to be made public later.

**Architecture:** Vendor al-folio v0.16.3 (the classic Bootstrap/SCSS
release, not the newer Tailwind rewrite) into this repo, strip the
features we don't want (CV/People/Repositories/Teaching pages), configure
site identity, and replace all demo content (bio, publications, projects,
blog posts) with real content. Content is authored as Markdown/YAML/BibTeX
files; GitHub Actions builds and deploys to GitHub Pages once the repo is
public.

**Tech Stack:** Jekyll (Ruby), al-folio v0.16.3 theme, GitHub Actions,
GitHub Pages.

**Spec:** `docs/superpowers/specs/2026-09-13-personal-site-design.md`

## Global Constraints

- Repo: `gneeraj97.github.io`, root user site (`baseurl` blank), **private**
  for now — do not make it public or enable GitHub Pages serving as part
  of this plan.
- Dropped al-folio features: CV page, People/profiles page, Repositories
  widget, Teaching page, Dropdown/submenu page, Bookshelf/books feature,
  News page. Keep: About, Blog, Projects, Publications.
- **Confidentiality rule (applies to every project write-up):** never name
  a past or current employer in connection with a specific project; never
  include quantified business metrics (dollar figures, percentages, user
  counts, latency, cost savings) for employer-attributed work. Describe
  only the technical work itself. Exception: projects with a public
  GitHub repo or public blog post (Jailbreaker, DeepPass2, CypherK9,
  DermRAG) may link to that public artifact as-is. Proprietary/internal
  projects with no public repo (multi-agent security platform, report
  intelligence pipeline) are described generically with no name, no
  metrics. This does not restrict personal/academic projects with no
  employer attached.
- Source of project facts: `/Users/neerajgupta/Desktop/Job/resume/master_resume.json`
  — used privately to inform write-ups; never linked or exposed on the
  site.
- Local Ruby is outdated for this toolchain (system Ruby 2.6.10, Bundler
  1.17.2). Use a Homebrew-installed Ruby for this project instead of
  system Ruby.

---

## File Structure

```
Gemfile, Gemfile.lock          — Ruby deps (from al-folio, unmodified)
_config.yml                    — site identity, socials/scholar config, nav
_data/socials.yml              — email, github/linkedin usernames, scholar id
_pages/about.md                — bio + research interests (home page)
_pages/blog.md                 — blog index (unmodified from theme)
_pages/projects.md             — projects index (display_categories updated)
_pages/publications.md         — publications index (unmodified from theme)
_bibliography/papers.bib        — real publication entries
_posts/2026-09-13-welcome.md   — first real blog post
_projects/1_project.md .. 9_project.md — real project entries
.github/workflows/deploy.yml   — kept as-is (builds + deploys on push)
.gitignore                     — Jekyll build artifacts
README.md                      — short repo description (replaces theme's)
```

Deleted from the vendored theme: `_pages/cv.md`, `_pages/profiles.md`,
`_pages/about_einstein.md`, `_pages/repositories.md`, `_pages/teaching.md`,
`_pages/dropdown.md`, `_pages/books.md`, `_pages/news.md`, `_data/cv.yml`,
`_data/repositories.yml`, `_books/` (entire dir), all demo `_posts/*` and
`_projects/*` files, and CI-noise workflow files
(`axe.yml`, `broken-links.yml`, `broken-links-site.yml`, `codeql.yml`,
`deploy-docker-tag.yml`, `deploy-image.yml`, `docker-slim.yml`,
`lighthouse-badger.yml`, `prettier.yml`, `prettier-html.yml`,
`prettier-comment-on-pr.yml`, `update-citations.yml`, `update-tocs.yml`,
`schedule-posts.txt`), plus `FAQ.md`, `INSTALL.md`, `CONTRIBUTING.md`,
`CUSTOMIZE.md`, `Dockerfile`, `docker-compose.yml`,
`docker-compose-slim.yml`, `package.json`, `package-lock.json`, `bin/`.

Kept but unmodified: `purgecss.config.js` (the deploy workflow's CSS-purge
step needs it), `LICENSE` (theme attribution).

---

### Task 1: Vendor al-folio v0.16.3 and get a baseline build working

**Files:**
- Create: everything under the repo root from the al-folio v0.16.3 tarball
  (minus the files listed for deletion above)
- Create: `.gitignore`

**Interfaces:**
- Produces: a working local Jekyll build at `_site/` via
  `bundle exec jekyll build`, reachable with the demo content still in
  place. Every later task rebuilds on top of this.

- [ ] **Step 1: Download and extract al-folio v0.16.3**

```bash
cd /Users/neerajgupta/Desktop/Project/myBlog
curl -sL "https://github.com/alshedivat/al-folio/archive/refs/tags/v0.16.3.tar.gz" -o /tmp/al-folio-v0.16.3.tar.gz
tar -xzf /tmp/al-folio-v0.16.3.tar.gz --strip-components=1 -C .
rm /tmp/al-folio-v0.16.3.tar.gz
```

- [ ] **Step 2: Remove CI-noise workflows, docs, and dev-container files**

```bash
cd /Users/neerajgupta/Desktop/Project/myBlog
rm -f .github/workflows/axe.yml \
      .github/workflows/broken-links.yml \
      .github/workflows/broken-links-site.yml \
      .github/workflows/codeql.yml \
      .github/workflows/deploy-docker-tag.yml \
      .github/workflows/deploy-image.yml \
      .github/workflows/docker-slim.yml \
      .github/workflows/lighthouse-badger.yml \
      .github/workflows/prettier.yml \
      .github/workflows/prettier-html.yml \
      .github/workflows/prettier-comment-on-pr.yml \
      .github/workflows/update-citations.yml \
      .github/workflows/update-tocs.yml \
      .github/workflows/schedule-posts.txt \
      FAQ.md INSTALL.md CONTRIBUTING.md CUSTOMIZE.md \
      Dockerfile docker-compose.yml docker-compose-slim.yml \
      package.json package-lock.json
rm -rf bin/
ls .github/workflows/
```

Expected: only `deploy.yml` remains in `.github/workflows/`.

- [ ] **Step 3: Write `.gitignore`**

```
_site/
.jekyll-cache/
.jekyll-metadata
.bundle/
vendor/bundle/
.sass-cache/
```

- [ ] **Step 4: Install a modern Ruby via Homebrew (system Ruby 2.6 is too old)**

```bash
brew install ruby imagemagick
echo 'export PATH="/opt/homebrew/opt/ruby/bin:$PATH"' >> ~/.zshrc
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
ruby -v
```

Expected: a Ruby version >= 3.1 (Homebrew's current `ruby` formula), not
`2.6.10`.

- [ ] **Step 5: Install gems and verify a baseline build**

```bash
cd /Users/neerajgupta/Desktop/Project/myBlog
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
gem install bundler
bundle install
JEKYLL_ENV=production bundle exec jekyll build
```

Expected: build completes with no errors, `_site/index.html` is created.
This is the baseline — demo content (Einstein bio, sample publications,
demo projects) is still present at this point; later tasks replace it.

- [ ] **Step 6: Commit**

```bash
git add -A
git commit -m "$(cat <<'EOF'
Vendor al-folio v0.16.3 theme, strip unused CI workflows

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RwXFBwmzbbEUXVxAr5jY2z
EOF
)"
```

---

### Task 2: Configure site identity and social/scholar links

**Files:**
- Modify: `_config.yml`
- Modify: `_data/socials.yml`

**Interfaces:**
- Consumes: baseline build from Task 1.
- Produces: `_config.yml` with `first_name: Neeraj`, `last_name: Gupta`,
  `url: https://gneeraj97.github.io`, `baseurl:` (blank); `_data/socials.yml`
  with real `email`, `github_username`, `linkedin_username`,
  `scholar_userid`. Later tasks (About page, Publications) rely on
  `scholar.last_name`/`scholar.first_name` being set here to bold the
  right author in the publications list.

- [ ] **Step 1: Edit `_config.yml` site settings**

Open `_config.yml` and change these existing keys (keep everything else
as shipped by the theme):

```yaml
title: blank # the website title (if blank, full name will be used instead)
first_name: Neeraj
middle_name:
last_name: Gupta
contact_note: >
  GitHub, LinkedIn, and Google Scholar are the best ways to reach me.
description: >
  Applied AI/ML engineer and researcher — LLM safety, multi-agent systems,
  and applied NLP for security.
footer_text: >
  Powered by <a href="https://jekyllrb.com/" target="_blank">Jekyll</a> with <a href="https://github.com/alshedivat/al-folio">al-folio</a> theme.
  Hosted by <a href="https://pages.github.com/" target="_blank">GitHub Pages</a>.
keywords: machine learning, LLM, research, blog, portfolio

url: https://gneeraj97.github.io
baseurl:
```

- [ ] **Step 2: Edit `_config.yml` jekyll-scholar author config**

Find the `scholar:` block and change `last_name`/`first_name`:

```yaml
scholar:
  last_name: [Gupta]
  first_name: [Neeraj, N.]
```

This makes jekyll-scholar bold "Gupta, N." in the author list on the
publications page — matches how the author appears in the bibliography
entries added in Task 5.

- [ ] **Step 3: Replace `_data/socials.yml` contents**

```yaml
# this file contains the social media links and usernames of the author
# the socials will be displayed in the order they are defined here
email: gneeraj97@hotmail.com
github_username: gneeraj97
linkedin_username: neeraj-gupta97
scholar_userid: DjxvnCoAAAAJ
rss_icon: true
```

(Dropped `cv_pdf`, `inspirehep_id`, and the `custom_social` example block
from the theme's default file — none apply here.)

- [ ] **Step 4: Rebuild and verify**

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
JEKYLL_ENV=production bundle exec jekyll build
grep -q "Neeraj Gupta" _site/index.html && echo "OK: name found"
grep -q "gneeraj97" _site/index.html && echo "OK: github username found"
```

Expected: both `OK:` lines print.

- [ ] **Step 5: Commit**

```bash
git add _config.yml _data/socials.yml
git commit -m "$(cat <<'EOF'
Configure site identity and social/scholar links

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RwXFBwmzbbEUXVxAr5jY2z
EOF
)"
```

---

### Task 3: Remove unused pages and features

**Files:**
- Delete: `_pages/cv.md`, `_pages/profiles.md`, `_pages/about_einstein.md`,
  `_pages/repositories.md`, `_pages/teaching.md`, `_pages/dropdown.md`,
  `_pages/books.md`, `_pages/news.md`
- Delete: `_data/cv.yml`, `_data/repositories.yml`
- Delete: `_books/` (entire directory)

**Interfaces:**
- Consumes: build from Task 2.
- Produces: a nav bar with exactly Blog, Publications, Projects (About is
  the home page, not a nav item). Later tasks don't touch these deleted
  files.

- [ ] **Step 1: Delete the files**

```bash
cd /Users/neerajgupta/Desktop/Project/myBlog
rm -f _pages/cv.md _pages/profiles.md _pages/about_einstein.md \
      _pages/repositories.md _pages/teaching.md _pages/dropdown.md \
      _pages/books.md _pages/news.md
rm -f _data/cv.yml _data/repositories.yml
rm -rf _books/
```

- [ ] **Step 2: Rebuild and verify the removed pages are gone and the nav is right**

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
JEKYLL_ENV=production bundle exec jekyll build
test ! -d _site/cv && echo "OK: no cv page"
test ! -d _site/people && echo "OK: no people page"
test ! -d _site/repositories && echo "OK: no repositories page"
test ! -d _site/teaching && echo "OK: no teaching page"
test ! -d _site/books && echo "OK: no books page"
grep -o 'nav-item[^<]*<a[^>]*>[^<]*' _site/index.html | grep -oE '>[a-z]+$' | sort -u
```

Expected: all four `OK:` lines print, and the nav-item scan shows only
`blog`, `projects`, `publications` (order may vary).

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "$(cat <<'EOF'
Remove CV, people, repositories, teaching, books, news pages

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RwXFBwmzbbEUXVxAr5jY2z
EOF
)"
```

---

### Task 4: Write the About page

**Files:**
- Modify: `_pages/about.md`

**Interfaces:**
- Consumes: build from Task 3.
- Produces: the home page bio. Later tasks (blog, projects, publications)
  are linked from this page's prose but don't depend on its exact wording.

- [ ] **Step 1: Replace `_pages/about.md` contents**

```markdown
---
layout: about
title: about
permalink: /
subtitle: AI/ML engineer & researcher — LLMs, multi-agent systems, applied NLP for security

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false
  more_info:

selected_papers: false
social: true

announcements:
  enabled: false

latest_posts:
  enabled: true
  scrollable: true
  limit: 3
---

I'm an applied AI/ML engineer and researcher working on large language models, multi-agent systems, and their application to security and evaluation. I hold an MS in Computational Data Science from Purdue University and a B.Tech from IIT Kanpur.

My research interests center on **LLM safety and evaluation**, **multi-agent orchestration**, **retrieval-augmented generation**, and applying NLP techniques to security problems. I also spend time fine-tuning models for domain-specific tasks and building tools that make these systems easier to test and understand.

I write about machine learning system design, interesting papers, and the occasional book — you'll find that on the [blog](/blog/). See [projects](/projects/) for things I've built, and [publications](/publications/) for peer-reviewed work.
```

Note: `profile.image` still points at the theme's placeholder
`prof_pic.jpg` (under `assets/img/`) — replace that file with a real
photo whenever you have one; not something I can fabricate.

- [ ] **Step 2: Rebuild and verify**

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
JEKYLL_ENV=production bundle exec jekyll build
grep -q "LLM safety and evaluation" _site/index.html && echo "OK: bio found"
```

Expected: `OK: bio found` prints.

- [ ] **Step 3: Commit**

```bash
git add _pages/about.md
git commit -m "$(cat <<'EOF'
Write about page bio and research interests

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RwXFBwmzbbEUXVxAr5jY2z
EOF
)"
```

---

### Task 5: Replace the publications bibliography

**Files:**
- Modify: `_bibliography/papers.bib`

**Interfaces:**
- Consumes: build from Task 4; `scholar.last_name`/`first_name` set in
  Task 2.
- Produces: the publications page listing. No later task depends on
  bibliography entry keys.

- [ ] **Step 1: Replace `_bibliography/papers.bib` contents**

```bibtex
---
---

@inproceedings{liu2025medchat,
  title={MedChat: A Multi-Agent Framework for Multimodal Diagnosis with Large Language Models},
  author={Liu, P. R. and Bansal, S. and Dinh, J. and Pawar, A. and Satishkumar, R. and Desai, S. and Gupta, N. and Wang, X. and Hu, S.},
  booktitle={2025 IEEE 8th International Conference on Multimedia Information Processing and Retrieval (MIPR)},
  pages={456--462},
  year={2025},
  organization={IEEE},
  html={https://ieeexplore.ieee.org/document/11225970},
  arxiv={2506.07400}
}

@article{lin2024detecting,
  title={Detecting Multimedia Generated by Large AI Models: A Survey},
  author={Lin, L. and Gupta, N. and Zhang, Y. and Ren, H. and Liu, C.-H. and Ding, F. and Wang, X. and Li, X. and Verdoliva, L. and Hu, S.},
  journal={arXiv preprint arXiv:2402.00045},
  year={2024},
  arxiv={2402.00045}
}
```

- [ ] **Step 2: Rebuild and verify**

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
JEKYLL_ENV=production bundle exec jekyll build
grep -q "MedChat" _site/publications/index.html && echo "OK: MedChat found"
grep -q "Detecting Multimedia Generated" _site/publications/index.html && echo "OK: survey found"
grep -qv "Einstein" _site/publications/index.html && echo "OK: no demo Einstein entries"
```

Expected: all three `OK:` lines print.

- [ ] **Step 3: Commit**

```bash
git add _bibliography/papers.bib
git commit -m "$(cat <<'EOF'
Replace demo bibliography with real publications

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RwXFBwmzbbEUXVxAr5jY2z
EOF
)"
```

---

### Task 6: Replace demo blog posts with a first real post

**Files:**
- Delete: all files under `_posts/`
- Create: `_posts/2026-09-13-welcome.md`
- Modify: `_config.yml` (`blog_name`, `blog_description`)

**Interfaces:**
- Consumes: build from Task 5.
- Produces: the blog index with exactly one post. No later task depends
  on this post's content.

- [ ] **Step 1: Delete all demo posts**

```bash
cd /Users/neerajgupta/Desktop/Project/myBlog
rm -f _posts/*.md
```

- [ ] **Step 2: Create the first post**

```markdown
---
layout: post
title: Starting this blog
date: 2026-09-13
description: A short note on why this site exists and what to expect here.
tags: meta
categories: notes
---

I've been meaning to put a proper home online for a while — somewhere to write about the systems I build, papers I find interesting, and the occasional book. This is that.

Expect posts on applied LLM work (multi-agent systems, evaluation, fine-tuning), notes on papers and books I'm reading, and write-ups of side projects as I finish them. No fixed schedule — just when there's something worth writing down.
```

Save as `_posts/2026-09-13-welcome.md`.

- [ ] **Step 3: Update blog identity in `_config.yml`**

Find the `# Blog` section and change:

```yaml
blog_name: Neeraj Gupta
blog_description: Notes on ML systems, security, and things I'm reading.
```

- [ ] **Step 4: Rebuild and verify**

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
JEKYLL_ENV=production bundle exec jekyll build
grep -q "Starting this blog" _site/blog/index.html && echo "OK: post found"
test $(ls _posts/ | wc -l) -eq 1 && echo "OK: exactly one post file"
```

Expected: both `OK:` lines print.

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "$(cat <<'EOF'
Replace demo blog posts with first real post

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RwXFBwmzbbEUXVxAr5jY2z
EOF
)"
```

---

### Task 7: Replace demo projects with real, anonymized entries

**Files:**
- Delete: `_projects/1_project.md` through `_projects/9_project.md`
  (theme demo files)
- Create: `_projects/1_project.md` through `_projects/9_project.md`
  (real content, same filenames reused for ordering)
- Modify: `_pages/projects.md` (`display_categories`, description)

**Interfaces:**
- Consumes: build from Task 6.
- Produces: the projects gallery with 5 "work" entries (confidentiality
  rule applied) and 4 "open-source" entries (linking to real public
  repos). No later task depends on this.

- [ ] **Step 1: Delete the demo project files**

```bash
cd /Users/neerajgupta/Desktop/Project/myBlog
rm -f _projects/*.md
```

- [ ] **Step 2: Create `_projects/1_project.md`**

```markdown
---
layout: page
title: Multi-Agent Security Analysis System
description: Coordinating specialized LLM agents for multi-step security investigations
importance: 1
category: work
---

Designed and built a multi-agent LLM orchestration system where several specialized agents — each focused on a different analysis domain (privilege chains, credential paths, query generation, and so on) — collaborate through a supervisor pattern built with LangGraph. Agents communicate through structured handoff tools and a ReAct-style reasoning loop, letting the supervisor route a query to the right specialist and combine partial results into a coherent multi-step analysis.

The backend is a FastAPI service with an async PostgreSQL layer, exposing a REST API for managing conversations and running analyses, with support for real-time streaming responses.

**Stack:** Python, LangGraph, FastAPI, PostgreSQL (asyncpg), MLflow for experiment tracking
```

- [ ] **Step 3: Create `_projects/2_project.md`**

```markdown
---
layout: page
title: Automated Report Intelligence Pipeline
description: Turning long, unstructured technical reports into structured, searchable data
importance: 2
category: work
---

Built a multi-stage pipeline that ingests long-form technical reports (PDFs) and extracts structured information from them: identifying tools and techniques mentioned in the text, using an LLM with custom schemas to convert narrative descriptions into a structured graph representation, and generating visualizations and summary analytics from the result.

The system tracks entities and relationships consistently across a long document, handles multi-stage narratives, and exposes the extracted data through a REST API backed by PostgreSQL, with search and cross-document analytics on top.

**Stack:** Python, FastAPI, PostgreSQL, Pydantic (structured LLM outputs), NetworkX/Matplotlib for visualization
```

- [ ] **Step 4: Create `_projects/3_project.md`**

```markdown
---
layout: page
title: Recommendation & Notification Systems
description: Large-scale personalization and delivery systems for a consumer app
importance: 3
category: work
---

Designed personalized notification and content-recommendation systems for a large consumer platform, using matrix factorization for recommendations and large-scale batch processing to generate personalized notifications at scale. Also worked on the delivery side — building retry logic in collaboration with the backend team to improve how reliably notifications actually reached users.

**Stack:** Python, SQL, Apache Spark, Matrix Factorization
```

- [ ] **Step 5: Create `_projects/4_project.md`**

```markdown
---
layout: page
title: Infrastructure Uptime Forecasting
description: Predicting device/infrastructure availability to reduce downtime
importance: 4
category: work
---

Built a predictive framework for infrastructure availability, using time-series models (LSTM) and data mining on historical usage/failure patterns to anticipate downtime before it happened. Complemented this with a peak-usage model used for capacity planning ahead of major demand spikes.

**Stack:** Python, SQL, LSTM, time-series forecasting
```

- [ ] **Step 6: Create `_projects/5_project.md`**

```markdown
---
layout: page
title: Credit Risk & Churn Modeling
description: Classification and segmentation models for financial risk and retention
importance: 5
category: work
---

Built classification models (logistic regression, random forest, XGBoost) to assess credit risk, and descriptive segmentation models (CHAID) to identify the drivers behind customer churn, translating the segments into actionable retention strategies.

**Stack:** Python, XGBoost, Random Forest, SPSS Modeler, Tableau
```

- [ ] **Step 7: Create `_projects/6_project.md`**

```markdown
---
layout: page
title: Jailbreaker
description: Open-source, repeatable jailbreak & prompt-injection evaluation platform
importance: 6
category: open-source
---

An open-source, local-first platform for repeatable jailbreak, prompt-injection, and agent-behavior testing across chatbots and agent systems. It separates Target, Attacker, and Judge model roles, ships a registry of 30+ iterative and non-iterative attack techniques, and produces reproducible evidence — prompts, responses, judge scores, and traces — for every run.

I created and currently maintain this project.

[View on GitHub →](https://github.com/SpecterOps/Jailbreaker-CE)

**Stack:** Python, PostgreSQL, Docker Compose
```

- [ ] **Step 8: Create `_projects/7_project.md`**

```markdown
---
layout: page
title: DeepPass2
description: Multi-layer secret detection combining regex, a fine-tuned transformer, and LLM validation
importance: 7
category: open-source
---

A context-aware secret scanner that combines regex-based rules, a fine-tuned token-classification transformer, and LLM-based validation to detect structured credentials and free-form passwords hidden in documents. Password extraction is framed as sequence labeling, trained on a large synthetic corpus generated for the task, with a staged inference pipeline that chunks documents, extracts candidate spans, and validates them with an LLM to cut down false positives.

[View on GitHub →](https://github.com/SpecterOps/DeepPass2)

**Stack:** Python, Hugging Face Transformers, Flask, AWS Bedrock
```

- [ ] **Step 9: Create `_projects/8_project.md`**

```markdown
---
layout: page
title: CypherK9
description: LLM-augmented text-to-Cypher query generation, fine-tuned on graph data
importance: 8
category: open-source
---

A pipeline for generating Cypher graph-database queries from natural language, combining graph-structure-aware prompting with a fine-tuned code model. Explored static and dynamic retrieval augmentation strategies to improve query correctness, and deployed the resulting system as a set of containerized services.

[View on GitHub →](https://github.com/gneeraj97/CypherK9)

**Stack:** Python, LLM fine-tuning, Docker, vLLM
```

- [ ] **Step 10: Create `_projects/9_project.md`**

```markdown
---
layout: page
title: DermRAG
description: Retrieval-augmented generation for dermatology question answering
importance: 9
category: open-source
---

A domain-specific retrieval-augmented generation system for dermatology, built on Llama-2 with a recursive chunking strategy and a FAISS vector index. Fine-tuned the embedding model on domain-specific medical terminology to improve retrieval quality.

[View on GitHub →](https://github.com/gneeraj97/DermLlama-DermRAG)

**Stack:** Python, LangChain, FAISS, Llama-2
```

- [ ] **Step 11: Update `_pages/projects.md` categories**

Change the front matter `display_categories` line from
`display_categories: [work, fun]` to:

```yaml
display_categories: [work, open-source]
```

Also update the `description:` line to:

```yaml
description: Selected projects — some described generically to respect confidentiality with employers, others linking to public repositories.
```

- [ ] **Step 12: Rebuild and verify**

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
JEKYLL_ENV=production bundle exec jekyll build
grep -q "Multi-Agent Security Analysis System" _site/projects/index.html && echo "OK: work project found"
grep -q "Jailbreaker" _site/projects/index.html && echo "OK: open-source project found"
grep -qE "SpecterOps|BrowserStack|ShareChat|Indus Insights|BloodHound Talk" _projects/*.md && echo "FAIL: employer name leaked" || echo "OK: no employer names in project files"
grep -qE "[0-9]+%|\\\$[0-9]" _projects/1_project.md _projects/2_project.md _projects/3_project.md _projects/4_project.md _projects/5_project.md && echo "FAIL: metric leaked" || echo "OK: no metrics in work projects"
```

Expected: `OK:` for the first two checks, and `OK:` (not `FAIL:`) for the
last two confidentiality checks.

- [ ] **Step 13: Commit**

```bash
git add -A
git commit -m "$(cat <<'EOF'
Replace demo projects with real, anonymized project entries

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RwXFBwmzbbEUXVxAr5jY2z
EOF
)"
```

---

### Task 8: Final cleanup and full-site smoke check

**Files:**
- Create: `README.md` (overwrite theme's original)
- Verify: full site build

**Interfaces:**
- Consumes: build from Task 7.
- Produces: the finished local site, ready to push.

- [ ] **Step 1: Replace `README.md`**

```markdown
# gneeraj97.github.io

Personal site — blog, projects, and publications. Built with
[al-folio](https://github.com/alshedivat/al-folio) (Jekyll).

Design/content decisions are recorded in
`docs/superpowers/specs/2026-09-13-personal-site-design.md`.

## Local development

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
bundle install
bundle exec jekyll serve
```

Then open http://localhost:4000/.
```

- [ ] **Step 2: Full rebuild and smoke check every kept page**

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
JEKYLL_ENV=production bundle exec jekyll build
for f in _site/index.html _site/blog/index.html _site/projects/index.html _site/publications/index.html; do
  test -f "$f" && echo "OK: $f exists" || echo "FAIL: $f missing"
done
```

Expected: four `OK:` lines, no `FAIL:` lines.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "$(cat <<'EOF'
Add project README, finish content pass

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RwXFBwmzbbEUXVxAr5jY2z
EOF
)"
```

---

### Task 9: Create the private GitHub repo and push

**Files:** none (repo/remote operations only)

**Interfaces:**
- Consumes: final commit from Task 8.
- Produces: `origin` remote pointing at a private
  `gneeraj97/gneeraj97.github.io` GitHub repo, with `main` pushed. Going
  public and enabling Pages is explicitly **not** part of this task.

- [ ] **Step 1: Ensure `gh` CLI is installed and authenticated**

```bash
which gh || brew install gh
gh auth status
```

If `gh auth status` reports not logged in, stop and ask the user to run
`gh auth login` interactively (it opens a browser) before continuing —
this is a one-time authentication step that shouldn't be scripted
unattended.

- [ ] **Step 2: Create the private repo and add it as `origin`**

```bash
cd /Users/neerajgupta/Desktop/Project/myBlog
gh repo create gneeraj97/gneeraj97.github.io --private --source=. --remote=origin
```

- [ ] **Step 3: Push**

```bash
git push -u origin main
```

- [ ] **Step 4: Verify**

```bash
gh repo view gneeraj97/gneeraj97.github.io --json visibility,url
```

Expected: `"visibility": "PRIVATE"` and the URL matches
`https://github.com/gneeraj97/gneeraj97.github.io`.

---

## Self-review notes

- Spec coverage: About/Blog/Projects/Publications sections (Tasks 4–7),
  dropped CV/People/Repositories/Teaching/Dropdown/Books/News (Task 3),
  private repo (Task 9), confidentiality rule enforced with explicit grep
  checks in Task 7. Local build verification present in every task
  (spec's "testing/verification" section). Going public/enabling Pages is
  explicitly out of scope, matching the spec.
- No placeholders: every step has literal file content, not descriptions.
- Type/name consistency: `blog_name`/`blog_description` keys (Task 6),
  `display_categories`/`category` values `work`/`open-source` (Task 7),
  and `scholar.last_name`/`first_name` (Task 2) matching bibliography
  author strings (Task 5) all cross-checked against the actual al-folio
  v0.16.3 source fetched during planning.
