---
name: aidlc-observability-validation
description: Use when a platform foundation requires version-controlled telemetry, dashboards, alerts, and evidence that signals reach their operational destination.
---

# AIDLC v2 Observability Validation

## Goal

Make platform health, saturation, failure, and recovery visible and actionable.

## Required inputs

- Approved platform requirements/change plan, provisioned topology, pipeline validation, and ownership/runbook data.
- Exact non-production validation target and approval evidence for external mutations.

## Procedure

1. Map service-level indicators, platform signals, audit events, and recovery states to requirements.
2. Implement version-controlled telemetry, dashboard, alert, and routing configuration.
3. Validate collection labels, cardinality, retention, access, and sensitive-data filtering.
4. Emit or safely simulate representative healthy, degraded, failed, and recovered signals.
5. Observe dashboard rendering and the complete alert notification/acknowledgment path.
6. Record exact commands, timestamps, exit codes, observed signal identifiers, routing results, and gaps.

## Output contract

Produce project-scoped telemetry/dashboard/alert mutations and run-scoped `platform/observability-validation.md` with emitted-signal evidence, dashboard checks, alert-path evidence, recovery behavior, gaps, and verdict.

## Quality checks

- Evidence covers signal emission through destination, not only configuration syntax.
- Alerts identify owner, severity, actionable context, and runbook.
- Secret and personal data are absent from artifacts and telemetry examples.

## Constraints

Do not ask the user directly or generate unsafe production incidents. Stop if the target or external-mutation approval is missing.

## Completion

Complete when representative signals and alert paths are observed end to end or remaining gaps are explicitly blocking.
