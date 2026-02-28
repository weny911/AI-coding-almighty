---
name: architecture
description: Architecture design and technical solution specialist. Use when users need (1) new project planning (2) technical solution design (3) architecture decision records (ADR) (4) refactor planning (5) technology selection. Trigger keywords include: architecture, design, plan, refactor, ADR, technical solution, system design.
---

# Architecture Skill

Provides methodological guidance for software architecture design.

## Rules

### Core Principle: Trade-offs First

The essence of architecture is **trade-offs**, not drawing boxes and arrows. Every decision must answer:
- Why choose A over B?
- Under what conditions will this decision fail?

### Constraints First

Before starting any design, clarify constraints first:
1. **Time and Resources**: How much time is available? How many people are available?
2. **Technical Debt**: What stack must be used? What legacy burden exists?
3. **Runtime Environment**: Read-heavy/write-light? High concurrency? Offline-first?

### Evolutionary Design

- **MVP**: Build the minimum viable product and make it run first
- **YAGNI**: You Aren't Gonna Need It; do not pre-build unnecessary features
- **Gall's Law**: Complex systems evolve from simple systems; do not start with an overly complex architecture

### Refactoring Principle: Strangler Fig Pattern

Avoid big-bang rewrites. Use incremental replacement:
1. Identify seams
2. Add tests
3. Small refactor
4. Verify and commit

## Phases

### Phase: Plan (New Project Planning)

1. **Requirements Decomposition**: Break vague requirements into core entities and interaction flows
2. **Constraint Identification**: Fill the Context section of the ADR template
3. **Technology Selection**: Recommend a stack based on constraints and record the ADR Decision
4. **Blueprint Design**: Design directory structure, module boundaries, and data flow
5. **Immediate Execution**: Use `write_to_file` to create the core skeleton

### Phase: Design (Solution Design)

1. **Define Problem Boundaries**: What is the scope of this problem?
2. **Analyze Current State**: What is the current state and pain points? (locate first with `find_code`)
3. **Define Target State**: What is the ideal state?
4. **Design the Transition Path**: How do we move from A to B?
5. **Record Trade-offs**: ADR is mandatory
6. **Execute the First Step Immediately**: Do not wait for confirmation

### Phase: Refactor (Refactor Planning)

1. Use `find_code(mode='impact')` to analyze change impact scope
2. Evaluate DICE complexity (provided by Manager)
3. Sort by complexity and prioritize high-risk files
4. Change one point at a time, verify, then continue

## ADR Template

When making important technical decisions, ADR must be recorded:

```markdown
# ADR: [Decision Title]

## Context
Why is this decision needed? What is the current pain point?

## Decision
What did we choose?

## Rationale
Why choose this over alternatives?
- **Pros**: ...
- **Cons**: ...

## Consequences
What impact does this decision introduce?

## Alternatives
What options were considered but rejected, and why?
```

## Anti-Patterns

- ❌ Draw architecture diagrams without explaining why
- ❌ Choose a tech stack without recording rationale
- ❌ Big-bang rewrite with no incremental migration
- ❌ Over-engineer upfront, violating YAGNI
