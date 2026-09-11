---
name: aidlc-infrastructure-design
description: Use when a solution requires an explicit account of topology, environments, data, capacity, cost, migration, secrets, and rollback impact.
---

# AIDLC v2 Infrastructure Design

## Goal

Make infrastructure and operational consequences visible before delivery begins.

## Required inputs

- Approved requirements, architecture, contracts, and NFR design.
- Pinned repository and environment documentation from `input-manifest.yaml`.

## Procedure

1. Map components and data stores to environments, regions/zones, networks, identities, and trust boundaries.
2. Describe data lifecycle, backups, retention, residency, migrations, and recovery.
3. Reference secret names and management systems without recording values.
4. Estimate workload, capacity limits, scaling triggers, and material cost drivers.
5. Define deployment ordering, compatibility windows, rollback conditions, and irreversible steps.
6. Identify ownership, runbook, and environment prerequisites.

## Output contract

Write run-scoped `solution/infrastructure-impact.md` covering topology, environment matrix, data, secret references, migration, capacity, cost, dependencies, ownership, and rollback impact.

## Quality checks

- No credential, token, private key, kubeconfig, or secret value appears.
- Every irreversible or destructive operation has an approval and recovery boundary.
- Capacity and cost statements identify assumptions.

## Constraints

Do not ask the user directly or mutate infrastructure. Missing target facts remain explicit blockers.

## Completion

Complete when delivery can estimate, provision, deploy, operate, and reverse the solution without hidden infrastructure assumptions.
