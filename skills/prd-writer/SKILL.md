---
name: prd-writer
description: >
  Generates structured Product Requirements Documents (PRDs) for software and app features.
  Use this skill any time a user wants to write, draft, create, or build a PRD, product spec,
  feature spec, requirements document, or product requirements for an app, feature, or product.
  Also trigger when the user says things like "define requirements for", "spec out this feature",
  "write up what this feature should do", "I need a requirements doc", "help me document this
  feature", "create user stories for", "write acceptance criteria for", or "turn this idea into
  a spec". Trigger even when the user describes what they want to build conversationally without
  using the word "PRD" — if they're describing a feature or product and seem to want a formal
  document out of it, use this skill. Especially useful for Agile/Scrum teams and founders
  building software products.
---

# PRD Writer

You are helping the user produce a complete, structured Product Requirements Document (PRD). Your job is to take whatever input they give — a brief description, a set of notes, a bullet list of ideas, or a fully described feature — and turn it into a professional, Agile-ready PRD that a dev team can immediately work from.

## What a great PRD does

A PRD bridges the gap between business intent and technical execution. It answers:
- **Why** are we building this? (Problem + opportunity)
- **Who** are we building it for? (Users + personas)
- **What** exactly are we building? (Scope + features)
- **How** will we know it's working? (Success metrics)
- **What's the bar for done?** (Acceptance criteria + DoD)

The goal is to be precise enough that a developer can build it and a QA engineer can test it — without over-specifying implementation details.

## Before you write

If the user hasn't given you enough to work from, ask targeted questions:
1. **Product/Feature name** — What is this called?
2. **Problem being solved** — What user pain or business opportunity does this address?
3. **Target users** — Who uses this, and what's their context?
4. **Core functionality** — What must it do? What's optional?
5. **Known constraints** — Tech stack, timeline, budget, regulatory?
6. **Success definition** — How do you measure that this worked?

Don't ask all 6 at once. Read what the user gave you and ask only for what's genuinely missing.

## PRD Structure

Generate the PRD using this structure. Adapt section depth to the complexity of what's being built — a small feature doesn't need the same depth as a full product.

---

### 1. Document Header
- **Product/Feature Name**
- **Author**
- **Date**
- **Version**
- **Status** (Draft / In Review / Approved)

### 2. Executive Summary
2–4 sentences. What is this, why does it matter, and what outcome does it drive? Written for a non-technical stakeholder.

### 3. Problem Statement
- What problem does this solve?
- Who experiences this problem?
- What's the current workaround (if any)?
- Why now? (urgency or opportunity)

### 4. Goals & Success Metrics
Use OKR or KPI framing:
- **Goal**: [Outcome-oriented statement]
- **Metric**: [Measurable indicator]
- **Target**: [Specific threshold / timeframe]

Example:
| Goal | Metric | Target |
|------|--------|--------|
| Increase checkout completion | Conversion rate | ≥ 75% within 60 days |

### 5. Target Users / Personas
For each persona:
- **Name / Role**
- **Context** (what they're trying to accomplish)
- **Pain points** this feature addresses

### 6. Scope

#### In Scope
What this PRD covers.

#### Out of Scope
What is explicitly excluded to prevent scope creep. Be specific.

### 7. User Stories & Acceptance Criteria

Organize by Epic → Feature → User Story. For each story:

**Format:**
```
US-[N]: [Short title]
As a [persona], I want to [action] so that [benefit].

Acceptance Criteria:
- Given [context], when [action], then [outcome]
- Given [context], when [action], then [outcome]
[Add as many as needed to fully define the behavior]

Priority: Must / Should / Could / Won't (MoSCoW)
Effort Estimate: [XS / S / M / L / XL]
```

Use Gherkin-style (Given/When/Then) for acceptance criteria — it makes them directly testable by QA.

### 8. Functional Requirements

Numbered list of specific behaviors the system must exhibit. Reference user stories where relevant.

| ID | Requirement | Priority | Notes |
|----|-------------|----------|-------|
| FR-01 | ... | Must | ... |
| FR-02 | ... | Should | ... |

### 9. Non-Functional Requirements

| Category | Requirement | Target |
|----------|-------------|--------|
| Performance | Page load time | < 2s |
| Security | Auth standard | OAuth 2.0 / JWT |
| Accessibility | Compliance | WCAG 2.1 AA |
| Scalability | Concurrent users | 10,000 |

Only include categories relevant to the feature.

### 10. Technical Considerations
High-level technical notes — not implementation decisions, but constraints or known complexity the team should be aware of:
- API dependencies
- Data model impacts
- Third-party integrations
- Known technical risks

### 11. Dependencies
- Other teams or systems this work depends on
- Features or infrastructure that must exist before this can ship

### 12. Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| ... | H/M/L | H/M/L | ... |

### 13. Definition of Done
A shared checklist that defines when this feature is truly complete:

- [ ] All acceptance criteria pass
- [ ] Unit + integration tests written and passing
- [ ] QA sign-off on all user stories
- [ ] No P1/P2 defects open
- [ ] Performance benchmarks met
- [ ] Security review passed (if applicable)
- [ ] Documentation updated
- [ ] Product Owner approval

Customize the DoD based on what the user tells you about their team's standards.

### 14. Open Questions
Capture unresolved decisions that could affect scope or design. Flag the owner and target resolution date if known.

| Question | Owner | Due |
|----------|-------|-----|
| ... | ... | ... |

---

## Output format

- Produce the PRD as a **Word document (.docx)** using the `docx` skill by default, unless the user asks for markdown or another format.
- Use heading styles (H1, H2, H3) for document structure.
- Tables should be formatted and readable.
- If producing markdown, use standard CommonMark.

## Tone and precision

- Write in clear, unambiguous language. Avoid vague terms like "fast", "easy", or "user-friendly" — replace them with measurable criteria.
- Acceptance criteria should be specific enough that a QA engineer can write a test case directly from them.
- Functional requirements should be atomic — one behavior per requirement.
- Keep the executive summary jargon-free; keep the technical sections precise.

## After generating the PRD

Offer to:
1. Break it into sprint-ready user stories for backlog import
2. Generate a test plan from the acceptance criteria
3. Create a one-page executive summary version
