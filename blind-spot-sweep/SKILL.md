---
name: blind-spot-sweep
description: >
  Systematic sweep across every relevant dimension of a ticket to catch blind spots 
  before coding begins. This is Pass 3 of the Absorb phase in the Triple-A Framework 
  (Absorb → Architect → Attack). Use this skill when the user says "/blind-spot-sweep", 
  "sweep", "pass 3", "what am I missing", "checklist", "systematic review", 
  "did I miss anything", or "questions for my manager". Also trigger when the user has 
  completed /grill-me-reverse (pass 2) and wants to do a final check before moving to 
  the Architect phase. This is the final pass of Absorb. It produces two key outputs: 
  confirmed understanding across all dimensions, and a list of questions the user should 
  ask their manager or tech lead — things Claude can't answer from the ticket or codebase 
  alone. If the user hasn't done any prior context gathering, suggest starting with 
  /ground first.
---

# Blind-Spot Sweep — Absorb Phase, Pass 3

## The Triple-A Framework

This skill is part of the **Triple-A Framework** for agentic coding:
- **Phase 1 — Absorb:** Deep understanding before code. Three passes: Ground → Grill-Me-Reverse → Blind-Spot-Sweep
- **Phase 2 — Architect:** Organize thoughts (Excalidraw) + generate PRD in a fresh session
- **Phase 3 — Attack:** Execute in Cursor with Plan Mode → Agent Mode → Review

You are running **Blind-Spot Sweep** — the third and final pass of Absorb.

You are doing a final, systematic pass to catch anything the user might have missed before they move to PRD generation and coding. Unlike Pass 2 (grill-me-reverse), this isn't a quiz — it's a collaborative sweep where you walk through every relevant dimension together and flag gaps.

The two most important outputs of this pass are:
1. Confirmed understanding across all dimensions (or newly filled gaps)
2. **Questions the user should ask their manager or tech lead** — organizational context, past decisions, political considerations, and constraints that can't be answered from the ticket or codebase alone

## How to Run This Pass

### Setup

You need context about the ticket. The user should either:
- Have completed Passes 1 and 2 in this or prior sessions
- Provide the ticket text and their structured notes so far
- At minimum, describe what they know about the ticket

### The Sweep

Walk through each dimension below, one at a time. For each:
1. State the dimension
2. Share what you know or can infer from the ticket/context about this dimension
3. Flag anything **non-obvious** — things that look simple but aren't, or assumptions that might be wrong
4. Suggest **specific questions for the manager/tech lead** — things that require organizational knowledge, past context, or political awareness that aren't in the ticket

Not every dimension will be relevant to every ticket. Skip or abbreviate dimensions that clearly don't apply, and spend more time on the ones that matter.

### The 10 Dimensions

**1. Data Flow**
- Where does data come from? How is it transformed? Where does it go?
- Are there intermediate stores, caches, or queues?
- What format is the data in at each stage?
- For ML tickets: what's the feature pipeline? Training data source? Serving path?

*Manager questions to suggest:* "Is there a data contract or schema registry I should be aware of?" / "Who owns the upstream data source — can I rely on its format staying stable?"

**2. Failure Modes**
- What happens when upstream services are down, slow, or returning errors?
- What if the data is malformed, missing fields, has duplicates, or is stale?
- What does partial failure look like? Can we end up in an inconsistent state?
- What's the blast radius of a bug here?

*Manager questions to suggest:* "Has this area broken before? What went wrong?" / "What's the alerting/on-call story for this component?"

**3. Security & Data Sensitivity**
- Does this touch PII, credentials, or sensitive data?
- Are there auth/authz requirements?
- Does data cross environment boundaries (dev/staging/prod)?
- Are there compliance requirements (GDPR, SOC2, HIPAA)?

*Manager questions to suggest:* "Are there security review requirements for changes to this area?" / "Who should I check with about data handling?"

**4. Performance & Cost**
- Is there a latency budget or SLA?
- What's the expected throughput? What's the ceiling?
- Are there infra cost implications (GPU time, API calls, storage)?
- For ML: training cost, inference latency, batch vs. real-time?

*Manager questions to suggest:* "What's the performance budget for this endpoint/pipeline?" / "Is there a cost ceiling I should design within?"

**5. Existing Patterns & Prior Art**
- Is there code in the repo already doing 80% of this?
- What patterns does the team follow for this kind of work?
- Are there utility functions, base classes, or shared libraries I should use?
- What patterns should I explicitly NOT follow (deprecated, legacy)?

*Manager questions to suggest:* "Is there a reference implementation I should look at?" / "Are there patterns the team has tried and rejected for this kind of thing?"

**6. Cross-Service Dependencies**
- What other services, APIs, or teams does this touch?
- Are there versioning or compatibility constraints?
- Do I need to coordinate timing with another team's deploy?
- Are there shared resources (databases, queues, caches) with contention risk?

*Manager questions to suggest:* "Do I need to coordinate with [other team] on this?" / "Is there a shared resource I should be careful about?"

**7. Testing Strategy**
- What's the smallest feedback loop I can run locally?
- What test infrastructure exists (fixtures, factories, mocks)?
- Are there integration test environments I should know about?
- What's the CI pipeline? What checks gate the PR?

*Manager questions to suggest:* "What's the expected test coverage for this area?" / "Are there specific test cases from QA I should make sure to cover?"

**8. Rollout & Rollback**
- Should this be behind a feature flag?
- Is this a gradual rollout or all-at-once?
- What's the rollback plan if something goes wrong?
- Are there database migrations? Are they reversible?

*Manager questions to suggest:* "What's the team's rollout policy for this kind of change?" / "Do I need to coordinate the deploy with anyone?"

**9. Scope Boundaries**
- What is explicitly NOT in this ticket?
- Are there adjacent features or improvements that are tempting but should wait?
- Where does this ticket end and the next one begin?
- Is there scope that's ambiguous — could be in or out?

*Manager questions to suggest:* "I think [X] is out of scope — is that right?" / "Should I also handle [adjacent concern] or is that a separate ticket?"

**10. Prior Decisions & Constraints**
- Are there ADRs (Architecture Decision Records) relevant to this?
- Were there Slack threads, design docs, or meetings that established constraints?
- Has someone attempted this before? What happened?
- Are there organizational politics or preferences that aren't documented?

*Manager questions to suggest:* "Were there any prior attempts at this I should know about?" / "Are there design decisions I should be aware of that aren't in the ticket?"

## Consolidating Notes

After the sweep, help the user consolidate their notes:

> "Here's a summary of what we covered. I'd suggest adding these to your structured notes:
> - [Key items from each relevant dimension]
> 
> And here are the questions I'd recommend asking your manager/tech lead before moving to the Architect phase:
> - [Consolidated list of manager questions, prioritized by importance]
> 
> Once you've had those conversations and updated your notes, you're ready for **Phase 2 — Architect**: take your notes to Excalidraw, draw out your mental model, then open a fresh Claude session and run `/prd-generator` with your ticket, notes, and diagrams."

## What NOT to Do

- Don't turn this into another quiz (that was Pass 2). This is collaborative — share what you know, flag what's uncertain.
- Don't skip the "questions for manager" part. This is often the most valuable output of the entire sweep.
- Don't generate code, PRDs, or plans. This is still the understanding phase.
- Don't spend equal time on every dimension. Prioritize the ones that are risky, ambiguous, or non-obvious for this specific ticket.