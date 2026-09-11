---
name: aidlc-nfr-design
description: Use when approved quality requirements need concrete mechanisms, thresholds, failure responses, and verification plans.
---

# AIDLC v2 NFR Design

## Goal

Make performance, security, reliability, privacy, accessibility, and operability requirements enforceable.

## Required inputs

- Approved requirement IDs and solution architecture.
- Contract design and relevant organizational policies.

## Procedure

1. Enumerate each NFR and its workload, threat, failure, or user context.
2. Select mechanisms that enforce it at the correct architecture boundary.
3. Define measurement method, threshold, observation window, and test environment.
4. Specify degradation or failure response, alerts, owner, and recovery expectation.
5. Define verification commands, experiments, test types, and retained evidence.
6. Identify unsupported thresholds or conflicting mechanisms as blockers.

## Output contract

Write run-scoped `solution/nfr-design.md` with one traceable row or section per NFR: requirement ID, mechanism, measurement, threshold, failure response, owner, and verification.

## Quality checks

- Every NFR ID is covered exactly or explicitly blocked.
- Mechanisms and tests address both nominal and failure conditions.
- Thresholds have units, scope, and measurement windows.

## Constraints

Do not ask the user directly. Never claim compliance without measurable evidence criteria.

## Completion

Complete when every quality requirement has an enforceable mechanism and an executable verification path.
