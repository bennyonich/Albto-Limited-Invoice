# Project Delivery Automation (PDA) — Specification Index

This folder is the **development blueprint** for the project delivery automation platform. Implement in this order:

1. [Project Workflow Bible](./PROJECT_WORKFLOW_BIBLE.md) — every process, gate, and control row
2. [project_lifecycle.yaml](./project_lifecycle.yaml) — master sequential lifecycle + gates
3. [stages/lifecycle_stages.yaml](./stages/lifecycle_stages.yaml) — entry/inputs/activities/outputs/exit per stage
4. [cross_cutting_domains.yaml](./cross_cutting_domains.yaml) — lifecycle-spanning domains (not sequential stages)
5. [data_relationships.yaml](./data_relationships.yaml) — canonical entity graph
6. [engines/](./engines/) — control, impact, forecast, priority, reporting, attention queue
7. [rules/advancement.yaml](./rules/advancement.yaml) — no-skipping + authorised override
8. [events/event_catalog.yaml](./events/event_catalog.yaml) — event-driven propagation patterns
9. [audit/event_schema.yaml](./audit/event_schema.yaml) — immutable audit trail fields

Derived artefacts (not stored here yet): database schema, OpenAPI, business-rule engine code, UI stage wizard, notification templates.
