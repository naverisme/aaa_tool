# Architecture · <project and scope>

| Baseline field | Value |
| --- | --- |
| AAA version | <version> |
| Scope | <Git-root-relative directory or `.`> |
| Source baseline | <full reviewed Git commit> |
| Coverage | <entry points and boundaries inspected, known exclusions> |

## Objective and constraints

<Goal, exclusions, confirmed requirements and measurable acceptance criteria.>

## Current architecture

<As-built components and evidence with repository-relative paths and line ranges or symbols. Include limits of observation.>

## Target architecture

| Component | Responsibility and code owner/path | Inputs and outputs | Dependencies | State and failure behavior |
| --- | --- | --- | --- | --- |
| <name> | <path, mark proposed paths> | <contract> | <allowed calls> | <owned state and errors> |

### Flows

<Trigger → entry point → component boundary → state change or response. Cover the normal path and material failures.>

## Decisions and rules

| ID | Status | Decision or rule | Evidence and rationale | Trade-off, check or revisit trigger |
| --- | --- | --- | --- | --- |
| <ID> | <proposed, accepted or superseded> | <testable decision> | <requirement, source, rationale> | <trade-off and observable check> |

## Delivery and verification

| Order | Work and prerequisites | Completion check |
| --- | --- | --- |
| <step> | <implementation unit and dependencies> | <test or observable result> |

<Include migration and rollback for changes that require them.>

## Assumptions and open questions

<For each item, give its impact, whether it blocks publication, and how to resolve it.>
