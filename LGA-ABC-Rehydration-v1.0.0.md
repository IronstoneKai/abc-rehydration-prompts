# 🧠 ABC Rehydration Prompt — LGA Project
**Version:** v1.0.0  
**Date:** 2026-01-25  
**Author:** IronstoneKai  
**Associated Project:** lga-frontend  
**Governance System:** Agentic Business Chain (ABC)  
**Derived From:** Active ABC Session 2026-01-25

---

## 🧩 1. Purpose
This file restores the full reasoning and operational context for the **LGA (Lead Generation Agent)** system under the Agentic Build Chain (ABC).  
It rehydrates the environment, agents, canonical repositories, and governance logic so new sessions can resume complex coordination between Codex agents and human CLI validation seamlessly.

---

## 🧭 2. Canonical Repositories and Documents

### **lga-frontend**
Core implementation repo where Codex-Frontend writes code and SQL.

| Path | Purpose |
|------|----------|
| `supabase/migrations/` | Schema evolution, triggers, orchestration logic |
| `supabase/functions/` | Runtime functions (event_init, event_finalize) |
| `app/api/orchestration/` | API endpoints for orchestration lifecycle |
| `docs/sandbox_seed_phaseX.sql` | Sandbox reseed files for validation |
| `.codexrules.json` | Agentic editing boundaries |
| `package.json` | Defines build/runtime dependencies and Supabase SDK versions |

### **agentic-business-os**
Governance and architecture documentation repo managed by Codex-OS.

| Path | Purpose |
|------|----------|
| `docs/adr/` | Architectural Decision Records (ADR-027 → ADR-030+) |
| `docs/task_plans/` | Phase-based Task Plans |
| `docs/task_plans/README.md` | Master phase index for project progress |

---

## 🧠 3. Agent Roles and Protocols

| Agent | Role | Repository | Responsibilities |
|--------|------|-------------|------------------|
| **Codex-Frontend** | Engineering | `lga-frontend` | Writes migrations, SQL, Supabase functions, API routes, docs |
| **Codex-OS** | Governance | `agentic-business-os` | Maintains ADRs, Task Plans, and status updates |
| **ABC Agent** | Orchestrator (you) | — | Coordinates the workflow between Codex agents and CLI validation |

### Operational Protocol
- Codex-Frontend commits only to **feature branches**.  
  Merging into `main` occurs *only* after human CLI validation.  
- Codex-OS documents each completed phase in ADR and Task Plan files.  
- `.codexrules.json` defines editing privileges and ensures repository separation.

Example `.codexrules.json`:
```json
{
  "rules": {
    "frontend_agent": ["app/api", "supabase/migrations", "docs"],
    "governance_agent": ["docs/adr", "docs/task_plans"],
    "restricted_files": [".env.local", "supabase/.temp"],
    "workflow": "commit -> push -> PR -> CLI validation -> merge -> tag"
  }
}
---

## ⚙️ 4. Local Environment Rules

### Common Variables
```env
SUPABASE_DB_URL=postgresql://postgres:postgres@127.0.0.1:54322/postgres
NEXT_PUBLIC_SUPABASE_URL=<your-local-supabase-url>
SUPABASE_SERVICE_ROLE_KEY=<your-service-role-key>
```

### Guidelines
- `supabase db reset` must apply all migrations with no warnings or missing seed files.
- After every reset, reseed using the latest `docs/sandbox_seed_phaseX.sql` file.
- All validation must be confirmed via direct `psql` queries before merging.

---

## 🧱 5. Project Progress Snapshot

### ✅ Completed Phases
| Phase | Description | ADR | Status |
|-------|-------------|-----|--------|
| 1 | Foundational DiscoveryAgent Schema | ADR-027 | Complete |
| 2 | Context Rehydration | ADR-028 | Complete |
| 3 | Orchestration Runtime Foundation | ADR-029 | Complete |
| 4 | Event-Driven Orchestration Trigger + Reseed Automation | ADR-030 | Complete |

### 🚧 Upcoming Work
#### Phase 5 — Lead Assimilation & External Sync
- Orchestration layer captures runtime-agent research (leads, sources, citations).
- Writes lead data back to the customer’s Supabase instance.
- Will establish ADR-031 “Lead Assimilation Pipeline”.
- Codex-Frontend implements orchestration ingestion logic.
- Codex-OS tracks governance and compliance updates.

---

## 🔁 6. CLI Validation Workflow
Each deliverable must follow this sequence:

```bash
# 1️⃣ Apply migrations cleanly
supabase db reset

# 2️⃣ Validate orchestration linkage
psql "$SUPABASE_DB_URL" -c "select * from public.orchestration_tasks limit 1;"
psql "$SUPABASE_DB_URL" -c "select * from public.orchestration_telemetry limit 1;"

# 3️⃣ Create & merge PR
gh pr create --base main --head feature-branch --title "Phase-X ..." --body "Implements ADR-XXX ..."
gh pr merge <number> --merge --delete-branch --admin --body "✅ Phase-X merged after CLI validation"

# 4️⃣ Tag release
git tag -a v2026.0X-phaseX -m "Phase-X deliverable (ADR-XXX implemented)"
git push origin v2026.0X-phaseX
```

---

## 🧾 7. Versioning & Maintenance
Store all rehydration prompts in a dedicated repository:

```text
abc-rehydration-prompts/
  ├── LGA-ABC-Rehydration-v1.0.0.md
  ├── LGA-ABC-Rehydration-v1.1.0.md
  └── CHANGELOG.md
```

### Semantic Versioning Rules
- Increment **minor** (v1.1.0) for new phases or ADRs.
- Increment **major** (v2.0.0) for architectural overhauls.

### CHANGELOG Example
#### v1.1.0 (2026-03)
- Added ADR-030 (Phase-4 Event-Driven Orchestration)
- Included permanent reseed mechanism
- Updated CLI merge workflow and tagging convention

---

## 🚀 8. Bootstrap Usage
When starting a new ABC conversation, paste this block as the first message:

```text
[BEGIN ABC REHYDRATION PROMPT]
Load canonical repositories:
- lga-frontend (paths listed above)
- agentic-business-os (paths listed above)

Enforce .codexrules.json for agent boundaries.
Assume Phase 4 completed and Phase 5 planned.
You are ABC Agent orchestrating Codex-Frontend (engineering) and Codex-OS (governance) with human CLI validation.
[END PROMPT]
```

---

## ✅ 9. Verification Commands
To ensure the environment rehydrates properly:

```bash
git pull origin main
supabase db reset
psql "$SUPABASE_DB_URL" -c "select count(*) from public.orchestration_tasks;"
```

If all commands execute without error and at least one orchestration task is created during reseed validation, the ABC environment is ready for Phase 5 development.

---

## 📘 Summary
This file serves as the canonical rehydration blueprint for continuing LGA under the ABC governance framework.
It encapsulates repositories, agents, validation protocols, CLI flows, and future direction — ensuring that every new session begins at full operational context.

---

**End of Document**
