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
