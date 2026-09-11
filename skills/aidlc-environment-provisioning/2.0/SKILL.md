---
name: aidlc-environment-provisioning
description: Use when an approved platform plan authorizes bounded IaC or environment configuration mutations against an exact target.
---

# AIDLC v2 Environment Provisioning

## Goal

Create reproducible environment configuration while preserving target identity, approvals, and recoverable evidence.

## Required inputs

- Approved `platform/change-plan.md` and `platform/requirements.md`.
- Exact repository revision, target account/project/cluster/environment, credentials reference, and approval evidence.

## Procedure

1. Perform safe read-only inspection to verify identity, current state, drift, permissions, and tool versions.
2. Stop before mutation if target identity, approval evidence, rollback, or required privilege is absent or inconsistent.
3. Change version-controlled IaC/config first and validate syntax, policy, and plan output.
4. Apply only explicitly approved external mutations, respecting stop conditions and provider locking.
5. Run post-apply checks and compare observed resources with the approved plan.
6. Record commands, exit codes, redacted outcomes, changed paths, drift, and deviations.

## Output contract

Produce project-scoped IaC/config mutations and run-scoped `platform/provisioning-results.md` with target identity, approval reference, plan/apply or offline-validation evidence, exit codes, changed paths, deviations, and verdict.

## Quality checks

- Read-only inspection is distinguished from external mutation.
- No secret value, credential, token, private key, or kubeconfig content is retained.
- A success verdict requires real validated project mutations and all approved checks.

## Constraints

Do not ask the user directly. Never mutate an implicit/default target or proceed without recorded approval. Do not retry destructive operations automatically.

## Completion

Complete when approved IaC/config changes and observed validation evidence agree, or when a precise blocker is reported before unsafe mutation.
