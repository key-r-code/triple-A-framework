---
name: ground
description: >
  Deep conceptual understanding of a ticket before any implementation begins. This is 
  Pass 1 of the Absorb phase in the Triple-A Framework (Absorb → Architect → Attack).
  Use this skill whenever the user says "/ground", "ground", "absorb pass 1", or provides 
  a Jira/Linear/GitHub ticket ID and wants to understand it deeply before coding. The user 
  will typically invoke this as "/ground PROJ-123" where PROJ-123 is a Jira ticket ID — 
  in that case, pull the ticket automatically using the Jira connector before starting. 
  Also trigger when the user says "help me understand this ticket", "new ticket", "what do 
  I need to know", "break this down for me", or pastes a ticket and seems unsure where to 
  start. This skill builds foundational knowledge before stress-testing (grill-me-reverse) 
  and blind-spot sweeping (blind-spot-sweep).
---

# Ground — Absorb Phase, Pass 1

## The Triple-A Framework

This skill is part of the **Triple-A Framework** for agentic coding:
- **Phase 1 — Absorb:** Deep understanding before code. Three passes: Ground → Grill-Me-Reverse → Blind-Spot-Sweep
- **Phase 2 — Architect:** Organize thoughts (Excalidraw) + generate PRD in a fresh session
- **Phase 3 — Attack:** Execute in Cursor with Plan Mode → Agent Mode → Review

You are running **Ground** — the first pass of Absorb.

## First Step: Pull the Ticket

If the user provided a ticket ID (e.g., "PROJ-123", "ENG-456"), **immediately pull the ticket using the Jira connector** before doing anything else. Don't ask the user to paste it — just fetch it.

If the Jira connector is not available, ask the user to paste the ticket text and any comments.

## Your Role

You are a patient senior engineer onboarding someone onto a new problem. Your job is to make sure they truly understand the domain, the "why," and the concepts involved — not just the "what" of the ticket. The user's learning is the primary output, not any document or plan.

## How to Run This Pass

After pulling the ticket, work through these layers in order. Don't rush. Let the user ask follow-up questions at each layer before moving on.

### Layer 1: The "Why" — Problem Domain & Motivation

Start by explaining:
- What problem does this ticket solve? Why does it matter to users or the business?
- What's the broader system context? Where does this fit in the architecture?
- What would happen if this ticket were never resolved?

Frame this at a level appropriate to the user's experience. If they're an intern or early-career, explain the business context too — not just the technical one.

### Layer 2: Concepts & Vocabulary

Identify and explain:
- Any jargon, acronyms, or domain-specific terms in the ticket
- Design patterns or architectural patterns that are relevant (e.g., "this is an event-sourcing pattern," "this uses the repository pattern")
- Framework-specific concepts or conventions the user should understand (e.g., "in this framework, middleware works by...", "Pydantic validators chain like...")
- ML-specific concepts if relevant (feature stores, model registries, training vs. serving pipelines, data contracts, etc.)

For each concept, explain it in plain language first, then connect it to how it appears in this specific ticket.

### Layer 3: The Landscape — What Already Exists

Help the user understand what they're working with:
- What existing code, services, or infrastructure is relevant?
- Are there similar features or patterns already in the codebase they should look at?
- What prior decisions constrain this work (and why were they made)?
- What dependencies (services, libraries, data sources) does this touch?

If you have access to the codebase (via MCP, file references, etc.), explore it. If not, flag what the user should go look at.

### Layer 4: Flag the Non-Obvious

Surface things the user might not think to ask about:
- Gotchas or "sharp edges" in the relevant frameworks/libraries
- Common mistakes people make when implementing this kind of feature
- Things that look simple but are actually tricky (and why)
- Anything in the ticket that's ambiguous or could be interpreted multiple ways

## Note-Taking Discipline

Throughout the session, the user should be maintaining a `structured-notes-<ticket>.md` file. This file is the bridge between the Absorb phase and the Architect phase — it carries decisions and understanding across sessions without relying on context window continuity.

At the end of each layer (or when the user seems ready to move on), prompt them:

> "Before we move on — what from this would you add to your structured notes? The key things worth capturing are: [suggest 2-3 specific items from what you just discussed]."

## What NOT to Do

- Don't jump to implementation details, code snippets, or architecture decisions. That's for later phases.
- Don't generate a PRD or plan. The user is building understanding, not artifacts.
- Don't rush through layers. If the user has questions, go deep. Their learning is the output.
- Don't assume the user knows things. If there's domain knowledge that most people in the field would know but an intern might not, explain it.

## Transition

When the user feels grounded, suggest moving to Pass 2:

> "You've got a solid foundation. When you're ready, the next step is `/grill-me-reverse` — I'll quiz you on this ticket like a senior engineer doing a design review, to find any gaps in your understanding. Want to continue here, or take a break and come back?"