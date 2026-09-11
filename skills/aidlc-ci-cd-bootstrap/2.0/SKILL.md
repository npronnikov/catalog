---
name: aidlc-ci-cd-bootstrap
description: Use when an approved platform design needs a version-controlled delivery pipeline with validated gates, promotion, and rollback behavior.
---

# AIDLC v2 CI/CD Bootstrap

## Goal

Establish a reproducible supply path from source revision to immutable artifact and controlled environment promotion.

## Required inputs

- Approved platform requirements/change plan and provisioning results.
- Exact repositories, build/test commands, artifact registry, environments, and secret references.

## Procedure

1. Define triggers, concurrency, permissions, cache boundaries, and protected environments.
2. Implement build, test, security/policy, packaging, signing/provenance, and artifact publication stages as applicable.
3. Pin dependencies and pass immutable artifact identity between stages.
4. Add promotion approvals, environment checks, deployment strategy, and rollback path.
5. Validate configuration locally or through the approved non-production mechanism.
6. Record changed paths, commands, exit codes, observed gates, and untestable external assumptions.

## Output contract

Produce project-scoped pipeline mutations and run-scoped `platform/pipeline-validation.md` covering build, test, artifact identity, promotion, approval, permissions, and rollback behavior with concrete evidence.

## Quality checks

- Production promotion cannot bypass required approval and quality gates.
- Secrets are referenced through the platform facility and never embedded or logged.
- Validation demonstrates failure behavior as well as the happy path.

## Constraints

Do not ask the user directly or trigger an unapproved production deployment. Generated YAML without validation evidence is incomplete.

## Completion

Complete when pipeline mutations are syntactically valid, traceable, and demonstrably enforce the approved delivery controls.
