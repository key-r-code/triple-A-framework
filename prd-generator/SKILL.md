---
name: prd-generator
description: >
  Generate a structured PRD (Product Requirements Document) from a Jira ticket, 
  structured notes, and optional architecture diagrams. This is the core skill of 
  the Architect phase in the Triple-A Framework (Absorb → Architect → Attack). Use 
  this skill when the user says "/prd-generator", "generate prd", "write prd", 
  "create prd", "architect", "make a prd", "prd from my notes", or "I'm ready to 
  write the spec". Also trigger when the user provides a combination of a ticket + 
  notes + diagrams and wants them converted into a structured implementation document. 
  This skill expects the user to have already done context gathering in the Absorb phase 
  — if they haven't, suggest starting with /ground. The PRD is designed to be consumed 
  by AI coding agents (Cursor, Copilot, Claude Code) so it must be precise, 
  dependency-ordered, and verifiable — not a vague product brief.
---

# PRD Generator — Architect Phase

## The Triple-A Framework

This skill is part of the **Triple-A Framework** for agentic coding:
- **Phase 1 — Absorb:** Deep understanding before code. Three passes: Ground → Grill-Me-Reverse → Blind-Spot-Sweep
- **Phase 2 — Architect:** Organize thoughts (Excalidraw) + generate PRD in a fresh session ← YOU ARE HERE
- **Phase 3 — Attack:** Execute in Cursor with Plan Mode → Agent Mode → Review

You are generating a PRD that will be consumed by both the user and AI coding agents (primarily Cursor's agent/Composer mode). This means the PRD must be precise enough to act on, dependency-ordered, and contain verifiable success criteria. Vague PRDs produce vague code.

## Required Inputs

Before generating, confirm you have these four inputs. If any are missing, ask for them:

1. **The Jira/Linear/GitHub ticket** — the original ticket text, including any comments thread. This is the source of truth for requirements.
2. **Structured notes** — the user's notes from Phase 1 (conceptual grounding, grill-me-reverse, blind-spot sweep). These contain decisions, constraints, and understanding built during research.
3. **Architecture diagrams** (optional but recommended) — Excalidraw screenshots or similar. These capture the user's spatial mental model of the solution.
4. **PRD template** — use the template below unless the user provides their own.

## Generation Rules

When generating the PRD:

- **Prefer concrete file paths over generic placeholders.** Instead of "update the service layer," write "update `src/services/feature_service.py`". If you can't determine the exact path, say so and use a placeholder like `<path-to-feature-service>` so the user can fill it in.
- **Flag inferences.** Whenever you're inferring something rather than restating it from the inputs, mark it clearly: `[INFERRED: ...]`. The user should verify these before executing.
- **Preserve the user's decisions.** The structured notes contain decisions made during Phase 1 (e.g., "use Pydantic v2, not dataclasses"). These go in Section 7 (Out-of-Band Decisions) and must not be second-guessed.
- **Make success criteria verifiable.** Each criterion should be checkable with an assertion, a test, a metric, a curl command, or a screenshot. "Works correctly" is not a criterion.
- **Order the implementation plan by dependencies.** Foundations first: schema → data layer → service → handler → consumer/UI. Each task should be small enough to implement and test independently.
- **Keep it under 400 lines.** Token economy matters — agents that consume this PRD have limited context windows. Be precise, not verbose.
- **Include exact test commands.** Agents reference these constantly. `pytest tests/unit/test_feature.py -xvs` is useful; "run the tests" is not.

## PRD Template

Generate the PRD using this structure:

```markdown
# PRD: [Ticket ID] — [Title]

## 1. Context & Motivation
Why this ticket exists. The user/business problem. (3–5 lines max)

## 2. Goals & Non-Goals

### Goals
- [Specific, measurable goal 1]
- [Specific, measurable goal 2]

### Non-Goals (explicitly out of scope)
- [Thing that might seem in scope but isn't]
- [Adjacent improvement to save for a future ticket]

## 3. Success Criteria / Acceptance Tests
Each criterion must be verifiable — an assertion, metric, test, or observable behavior.

- [ ] [Given X, when Y, then Z]
- [ ] [Metric M stays below threshold T]
- [ ] [Endpoint /api/foo returns schema {...} with status 200]

## 4. Current State
What exists today that's relevant to this ticket.

- `path/to/relevant/file.py` — [what it does, what's relevant]
- `path/to/another/module/` — [current behavior]
- [Current data flow / architecture description]

## 5. Proposed Change
The intended end state, referenced concretely.

- [What changes in the architecture/data flow]
- [New files/modules to create]
- [Existing files to modify and how]
- For MLE: [schema changes, model interface deltas, pipeline impact]

## 6. Implementation Plan (dependency-ordered)

### Task 1: [Foundation — e.g., Schema/Data Layer]
- Files: `path/to/file.py`
- What: [Specific change]
- Test: [How to verify this task independently]

### Task 2: [Next layer — e.g., Service/Business Logic]
- Files: `path/to/service.py`
- What: [Specific change]
- Depends on: Task 1
- Test: [How to verify]

### Task 3: [Next layer — e.g., Handler/API/Consumer]
...

## 7. Out-of-Band Decisions
Decisions made during research that the agent should not second-guess.

- [Decision 1 — and brief rationale]
- [Decision 2 — and brief rationale]

## 8. Test Strategy
Exact commands to run at each level.

- **Unit:** `pytest tests/unit/test_xxx.py -xvs`
- **Integration:** `pytest tests/integration/test_xxx.py -xvs`
- **E2E:** `[exact command]`
- **Manual verification:** `curl -X POST http://localhost:8000/api/... -d '{...}'`

## 9. Risks & Failure Modes

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| [Risk 1] | [H/M/L] | [H/M/L] | [What to do] |

## 10. Rollout / Rollback
- Feature flag: [yes/no, flag name]
- Migration: [reversible? steps]
- Monitoring: [what to watch]
- Kill switch: [how to revert quickly]
```

## After Generation

Once the PRD is generated, present it to the user and:

1. **Highlight all `[INFERRED: ...]` markers** and ask the user to verify or correct them.
2. **Ask if there are any decisions from their manager/tech lead conversations** (from Pass 3's questions list) that should be incorporated.
3. **Suggest where to save it:** `specs/<ticket-id>/prd.md` in the repo, committed as the first commit on the feature branch.
4. **Suggest the transition to Phase 3:**

> "Your PRD is ready. Next steps — **Phase 3: Attack**
> 1. Commit this to `specs/<ticket-id>/prd.md` on your feature branch
> 2. Open Cursor and use Plan Mode to generate an execution plan from this PRD
> 3. Execute task by task in Agent/Composer mode
> 
> Want me to adjust anything in the PRD first?"

## What NOT to Do

- Don't add requirements that aren't in the inputs. The user did thorough research in Phase 1 — respect their decisions.
- Don't be vague. "Handle errors appropriately" is not useful for an agent. "Catch `ValueError` from `parse_features()`, log with severity ERROR, return 422 with `{error: 'invalid feature format'}`" is useful.
- Don't skip the Non-Goals section. This is arguably the most important section for agents — it prevents scope drift.
- Don't write a product-manager-style PRD. This is a technical specification for code agents. It should read like a senior engineer's design doc, not a business case.