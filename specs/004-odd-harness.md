# Spec 004: Optional ODD Harness

**Tier:** 2 (Medium / Standard SDD)
**Date:** 2026-10-30
**Status:** APPROVED for implementation by repository owner

## Goal

Add a lightweight Observability-Driven Development (ODD) guide that complements SDD and RDD across discovery, specification, implementation, verification, and closing.

## Acceptance criteria

- AC-01: `harnesses/odd.md` defines applicability, useful signals, failure/degradation, privacy, ownership, and explicit non-goals.
- AC-02: Tier 1, Tier 2, and Tier 3 spec templates request operational signals proportionally and allow `Not applicable`.
- AC-03: Phase, Discovery, RDD, review, and closing guidance link ODD without creating a universal stage or approval gate.
- AC-04: Agent guidance, README catalog, migration guide, memory, and changelog point to the harness.
- AC-05: No telemetry provider, SDK, endpoint, SLO, request header, or backend contract is invented or required.
- AC-06: The guidance prohibits logging credentials, personal/financial data, and full payloads by default.

## Scope

Documentation only: `harnesses/odd.md`, existing SDD/RDD harnesses, three spec templates, active spec and the corresponding indexes/state/changelog. No application code or dependencies.

## Design boundaries

ODD describes what operators need to observe for a concrete workflow. RDD assesses evidence on the frozen candidate; its Reliability lens may review the implementation of ODD criteria. The frontend reports only facts visible at its boundary. Backend health and distributed traces belong to the service owner. Correlation is propagated only under an agreed contract.

## Verification

Check changed Markdown formatting and local links. Do not report a self-approval or fabricate operational evidence.
