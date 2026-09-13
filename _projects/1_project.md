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
