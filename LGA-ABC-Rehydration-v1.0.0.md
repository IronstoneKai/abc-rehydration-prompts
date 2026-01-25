# 😁 ABC Rehydration Prompt — LGA Project

Version: v1.0.0
Date: 2026-01-25
Author: IronstoneKai
Associated Project: lga-frontend
Governance System: Agentic Business Chain (ABC)
Derived From: Active ABC Session 2026-01-25

## 🙐 1. Purpose
This file restores the full reasoning and operational context for the LGA (Lead Generation Agent) system under the Agentic Build Chain (ABC).  It rehydrates the environment, agents, canonical repositories, and governance logic so new sessions can resume complex coordination between Codex agents and human CLI validation seemessly.

## 🚀 2. Canonical Repositories and Documents

## *lga-frontend*
Core implementation repo where Codex-Frontend writes code and SQL.

| Path | Purpose |
| ------|-----------------------|
| supabase/migrations/ | Schema evolution, triggers, orchestration logic |
 | supabase/functions/ | Runtime functions (event_init, event_finalize) |
 | app/api/orchestration/ | API endpoints for orchestration lifecycle |
 | docs/sandbox_seed_phaseX.sql | Sandbox reseed files for validation |
 | .codexrules.json | Agentic editing boundaries |
 | package.json | Defines build/runtime dependencies and Supabase SDK versions |

## *agentic-business-os*
Covers governance and architecture repo managed by Codex-OS.

| Path | Purpose |
| -------------- |------------------------------------------------------------------|
| docs/adr/ | Architectural Decision Records (ADR-027 – ADR-030+) |
| docs/task_plans/ | Phase-based Task Plans |
| docs/task_plans/README.md | Master phase index |
