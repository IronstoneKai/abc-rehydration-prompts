# 🧠 ABC Rehydration Prompt — LGA Project
**Version:** v1.1.0  
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

### 🧰 6.1 — Simplified Local Sandbox Reset
If the project workspace includes the npm script `db:reset`, it should be used as the canonical entrypoint for all CLI validation.  
This command automatically:
- Spins up Dockerized Supabase services (via Supabase CLI)
- Applies all migrations
- Executes the latest sandbox reseed file (e.g., `docs/sandbox_seed_phaseX.sql`)

**Equivalent manual steps:**
```bash
docker compose up -d
supabase db reset
```

**Preferred command:**
```bash
npm run db:reset
```

### 🧱 6.2 — Canonical Database Reset Procedure
Every new sandbox or environment reset must begin with:

```bash
npm run db:reset
```

This command:
- Spins up the Dockerized Supabase services (via Supabase CLI)
- Applies all migrations in chronological order, including the latest phase migration (e.g., `20260125_phase5_lead_assimilation.sql`)
- Executes the latest reseed script (`docs/sandbox_seed_phaseX.sql`)
- Restarts the environment containers
- Verifies orchestration baseline creation (via `orchestration_tasks` and `orchestration_telemetry`)

#### Verification Checks
After reset, confirm the following:

```bash
psql "$SUPABASE_DB_URL" -c "select * from public.orchestration_tasks order by created_at desc limit 1;"
psql "$SUPABASE_DB_URL" -c "select * from public.orchestration_telemetry order by created_at desc limit 1;"
psql "$SUPABASE_DB_URL" -c "select * from public.leads limit 1;"
```

If these commands return valid rows (or `(0 rows)` for a new baseline without leads), the sandbox is healthy and ready for Phase validation.

> [!IMPORTANT]
> This reset procedure is mandatory before any:
> - CLI validation runs
> - Pull request merge
> - Phase tagging
> - Sandbox reseed file creation

### 📋 6.3 — Sequence
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

### 🧩 8.1 — When and How to Use This Prompt
Use this document whenever you open a new ChatGPT / ABC session and need to restore the full state of the LGA program. You can think of this as “loading your operating context.”

#### 🪄 Startup Steps
- Copy the entire contents of the current rehydration file (`LGA-ABC-Rehydration-vX.Y.Z.md`).
- Paste it as the first message in a new ABC conversation with ChatGPT (before saying anything else).
- The prompt tells the model:
    - Which repositories and canonical files to treat as context (`lga-frontend`, `agentic-business-os`, etc.).
    - How to interpret roles (Codex-Frontend, Codex-OS, ABC Agent, human CLI).
    - How to enforce `.codexrules.json`.
    - Which phase of the program is currently complete, and which one is active.
- Once the assistant confirms initialization, you can continue work exactly from where the previous session ended.

> [!TIP]
> You may shorten the paste to only the `[BEGIN ABC REHYDRATION PROMPT]` block if you’ve already uploaded the canonical document file as a reference in the chat; however, for full continuity it’s safest to paste the entire document.

### 🪪 8.2 — Maintaining Version Continuity
Every major milestone (for example, after finishing Phase 5 or adopting a new ADR) should produce a new rehydration version in your `abc-rehydration-prompts` repository.

#### 📈 Update Procedure
1. **Duplicate the latest file**:
   ```bash
   cp LGA-ABC-Rehydration-v1.0.0.md LGA-ABC-Rehydration-v1.1.0.md
   ```
2. **Edit the new file**:
   - Update the Version, Date, and Completed Phases tables.
   - Append or modify any new rules, canonical file paths, or workflow changes.
3. **Commit and tag**:
   ```bash
   git add LGA-ABC-Rehydration-v1.1.0.md
   git commit -m "v1.1.0 – Phase-5 bootstrap and ADR-031 initialization"
   git push origin main
   git tag -a v1.1.0 -m "Phase-5 rehydration update"
   git push origin v1.1.0
   ```
4. **Record the change in `CHANGELOG.md`**.
5. **Use the latest tag when starting any new session.**

### 🔄 8.3 — Recommended Workflow for New Sessions
| Step | Action | CLI / Chat |
|------|--------|------------|
| 1 | Pull latest rehydration repo | `git pull origin main` |
| 2 | Open the newest file (e.g. v1.1.0) | — |
| 3 | Copy full markdown or `[BEGIN … END PROMPT]` block | — |
| 4 | Paste into new ABC session | Chat |
| 5 | Verify that assistant lists all canonical repos and current phase | Chat |
| 6 | Begin next phase or task | CLI + Chat |

> [!NOTE]
> Before starting any validation loop, run the **Canonical Database Reset Procedure** (see §6.2).

### 🧪 8.4 — Interactive CLI Validation Protocol (Human-in-the-Loop)
- The **ABC Agent** provides CLI commands one at a time.
- The **human operator** executes each command locally (Docker + Supabase).
- The human returns the terminal output to the ABC Agent.
- The ABC Agent interprets the results, determines success/failure, and issues the next command.
- Once the E2E validation passes, **Codex-OS** updates the Task Plan to “Complete” and tags the phase.

### 🧭 8.5 — Archiving Older Versions
- Keep all historical versions in `abc-rehydration-prompts`.
- Each version acts as a precise snapshot of the project’s state at that time (similar to a “governance checkpoint”).
- Older versions must never be edited, only superseded.

### 🧾 8.6 — Summary of Lifecycle Rules
| Action | Trigger | Result |
|--------|---------|--------|
| Finish a phase & approve PR merge | Close of Phase X | Create next vX+1.0 file |
| New ADR accepted | Immediately | Increment minor version |
| Major architectural pivot | Rare | Increment major version |
| Beginning a new ChatGPT conversation | Always | Paste latest file’s contents |
| Assistant becomes slow or loses context | Optional | Generate new minor revision with updated canonical state |

### ✅ Quick Bootstrap Snippet (to paste at session start)
```text
[BEGIN ABC REHYDRATION PROMPT]
Load canonical repositories:
- lga-frontend (engineering)
- agentic-business-os (governance)
Apply .codexrules.json to enforce role boundaries.
Assume all phases ≤ 4 complete; Phase 5 (“Lead Assimilation & External Sync”) planned.
You are ABC Agent orchestrating Codex-Frontend (engineering) and Codex-OS (governance) with human CLI validation.
Use version LGA-ABC-Rehydration-v1.1.0 as canonical baseline.
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
