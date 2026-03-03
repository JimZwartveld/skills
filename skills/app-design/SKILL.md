---
name: app-design
description: Design a new application from scratch. Covers architecture, separation of concerns, scalability, deployment, and produces a boundary conditions document.
argument-hint: "[app-name]"
disable-model-invocation: true
---

# Application Design: $ARGUMENTS

You are starting an application design session. Your goal is to produce a **boundary conditions document** — a markdown file that captures all architectural decisions and constraints before any code is written.

## Philosophy

Be critical. Challenge assumptions. Push for separation of concerns:
- **Back-end logic must be independent of any UI.** Engineering/business logic should be usable from a Python script, a spreadsheet, a CLI, a simple dashboard, or a full web app — without modification.
- **Ask "should this be its own package/service?"** whenever a feature could serve multiple applications.
- **Design for gradual exposure:** core logic → spreadsheet/CLI → simple dashboard → full web application.

## Process

1. Read existing files in `implementation_plan/$ARGUMENTS/` if they exist (resuming work)
2. Work through the question categories below **in order**, asking 2–4 questions at a time
3. Be critical: challenge vague answers, suggest alternatives, flag potential over-engineering or under-engineering
4. When all categories are covered, write the boundary conditions document

## Question Categories

Work through these categories. Not every question applies to every app — skip what's irrelevant, but cover every category.

### 1. Problem & Purpose
- What problem does this application solve? Who experiences this problem today?
- What is the single most important thing this application must do well?
- What existing tools/processes does this replace or complement?
- How do users solve this problem today (spreadsheets, manual processes, other software)?

### 2. Users & Access
- Who are the distinct user types (engineers, managers, external clients)?
- How many concurrent users do you expect at launch? In 1 year? In 3 years?
- What authentication/authorization is needed? (SSO, API keys, role-based access)
- Should different users see different data or have different capabilities?

### 3. Core Domain & Boundaries
- What are the core computations/algorithms/business rules?
- **Critical: Can these be extracted into a standalone library with no UI or framework dependencies?**
- What data does the application own vs. consume from external sources?
- Are there existing libraries or packages in your ecosystem that this should build on?
- **Where are the boundaries between "core logic" and "application glue"?**

### 4. Separation & Reusability
- Will other applications need the same core logic? (If yes → separate package)
- Should this share a database with existing applications, or own its data?
- Are there features that look app-specific now but could become shared later?
- **Challenge: For each major feature, ask — "Could this be a library that multiple frontends consume?"**
- What is the right granularity? Monorepo with packages? Separate repos? Single repo with clear module boundaries?

### 5. Exposure Strategy (Gradual Rollout)
- What is the first way users will interact with this? (Script? Spreadsheet? CLI? Dashboard?)
- What is the eventual target interface? (Web app? Mobile? Embedded in another tool?)
- Map the progression: Which features are exposed at each stage?
- What API surface is needed to support all planned interfaces?
- Should there be a REST API, GraphQL, gRPC, or just a Python package interface?

### 6. Data Architecture
- What are the main data entities and their relationships?
- What is the expected data volume (rows, size, growth rate)?
- What are the read/write patterns? (Read-heavy? Write-heavy? Batch vs. real-time?)
- Is there time-series data, event sourcing, or audit trail requirements?
- Where does the data live today? What migrations are needed?
- What is the backup/recovery strategy?

### 7. Integration & Dependencies
- What external services does this depend on? (APIs, databases, file systems, hardware)
- What depends on this application? (Downstream consumers, reports, exports)
- Are there rate limits, SLAs, or availability constraints on dependencies?
- How should failures in dependencies be handled? (Retry, degrade, alert)

### 8. Performance & Scalability
- What operations must be fast (<100ms)? What can be slow (batch/async)?
- Are there computationally expensive operations? How will they scale?
- Do you need task queues, background workers, or scheduled jobs?
- What are the caching requirements? What data can be stale and for how long?
- Is horizontal scaling needed, or will a single server suffice for the foreseeable future?

### 9. Deployment & Infrastructure
- Where will this run? (Cloud provider, on-prem, hybrid, developer laptops)
- What is the deployment model? (Docker, serverless, VM, managed service)
- How many environments? (dev, staging, production)
- What CI/CD requirements exist?
- What monitoring and alerting is needed?
- What is the uptime requirement? (Best-effort? 99.9%? 99.99%?)

### 10. Security & Compliance
- Is there sensitive data (PII, financial, health, proprietary)?
- Are there regulatory requirements (GDPR, SOC2, industry-specific)?
- What is the data residency requirement? (EU, US, specific country)
- How should secrets and credentials be managed?

### 11. Developer Experience
- Who will maintain this after initial development? What is their skill level?
- What languages/frameworks is the team comfortable with?
- What testing strategy is appropriate? (Unit, integration, E2E, property-based)
- How should documentation be handled? (API docs, architecture docs, runbooks)

### 12. Scope Control & Phasing
- What is the MVP — the absolute minimum that delivers value?
- What features are tempting to include but should be deferred?
- What is the timeline for MVP vs. full vision?
- **Challenge: Is this really one application, or is it two applications sharing a library?**

## Critical Challenges (apply throughout)

At the end of each category, challenge the answers:
- "You said X — but wouldn't it be simpler to Y?"
- "This feature sounds like it belongs in a shared library, not in this app."
- "If you build this into the app now, you'll have to extract it later when app B needs it."
- "Do you really need this at launch, or is it a phase 2 concern?"
- "This sounds over-engineered for your current scale. What if you started with Z?"

## Output

When all categories are covered and the user confirms, write to `implementation_plan/$ARGUMENTS/boundary-conditions.md`:

```markdown
# Application Design: $ARGUMENTS

## 1. Problem Statement
[Concise description of the problem and who it serves]

## 2. Users & Access Model
[User types, scale expectations, auth approach]

## 3. Architecture & Boundaries

### Core Domain (standalone, no UI dependencies)
- [Package/library 1]: [what it does]
- [Package/library 2]: [what it does]

### Application Layer (UI, API, glue)
- [Component 1]: [what it does, what core packages it uses]

### Boundary Diagram
[ASCII or description of how components relate]

## 4. Separation Strategy
- **Shared packages:** [what gets extracted for reuse]
- **App-specific code:** [what stays in this application]
- **Repository structure:** [monorepo layout or multi-repo strategy]

## 5. Exposure Roadmap
| Phase | Interface | Features Exposed | Core Packages Used |
|-------|-----------|-----------------|-------------------|
| 1 | [e.g., Python package / CLI] | [subset] | [packages] |
| 2 | [e.g., Spreadsheet / Simple dashboard] | [larger subset] | [packages] |
| 3 | [e.g., Full web application] | [all] | [packages] |

## 6. Data Architecture
[Entities, relationships, storage, volume estimates]

## 7. Integration Points
[External dependencies, downstream consumers, failure handling]

## 8. Performance Constraints
[Latency requirements, scaling approach, async operations]

## 9. Deployment Model
[Infrastructure, environments, CI/CD, monitoring]

## 10. Security & Compliance
[Data sensitivity, regulations, secrets management]

## 11. Developer Experience
[Team, languages, testing strategy, documentation]

## 12. MVP Definition
### In scope (Phase 1)
- [Feature 1]
- [Feature 2]

### Explicitly out of scope (later phases)
- [Deferred feature 1]
- [Deferred feature 2]

## 13. Key Decisions & Trade-offs
| Decision | Chosen approach | Alternative considered | Rationale |
|----------|----------------|----------------------|-----------|
| [e.g., Monorepo vs multi-repo] | [chosen] | [alternative] | [why] |

## 14. Open Questions
- [Anything unresolved that needs investigation]
```

## Important
- Do NOT skip categories — at minimum acknowledge why a category doesn't apply
- Be opinionated: if the user is making a mistake, say so
- Push hard on separation of concerns — the default should be "extract to a library" unless there's a good reason not to
- Keep asking until both you and the user agree that the design is solid
- When done, tell the user the boundary conditions document is ready and they can proceed to feature discovery with `/discovery <feature-name>` for individual features
