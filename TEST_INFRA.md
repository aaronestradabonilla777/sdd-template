# TEST_INFRA.md — E2E Test Infrastructure & Specification

## 1. Executive Overview & Testing Philosophy

The **Multi-Agent SDD Template** test infrastructure provides an automated, requirement-driven, opaque-box testing framework. It verifies the architectural integrity, process governance, model tiering constraints, token efficiency budgets, and specification contracts of the template without coupling to internal implementation quirks.

### Core Testing Invariants
1. **Opaque-Box Requirement Derivation**: Every test case originates directly from user requirements (`ORIGINAL_REQUEST.md`) and architectural contracts (`PROJECT.md`).
2. **Zero Facade Testing**: Tests validate real structural files, line bounds, link integrity, role permissions, state machine transitions, and cross-reference validity.
3. **Stand-Alone Portability**: The test runner (`tests/e2e_test_runner.py`) relies exclusively on the Python 3 standard library (`os`, `sys`, `json`, `re`, `pathlib`, `argparse`, `dataclasses`, `unittest`), requiring zero pip installations.
4. **4-Tier Test Architecture**:
   - **Tier 1: Feature Coverage**: ≥5 test cases per feature for all 16 features in PROJECT.md (80 test cases).
   - **Tier 2: Boundary & Corner Cases**: ≥5 boundary test cases per feature covering line bounds, file counts, malformed markdown, invalid tiers, unassigned roles (80 test cases).
   - **Tier 3: Cross-Feature Combinations**: Pairwise and multi-variable interactions between sizing, discovery, closing, multi-agent roles, model tiers, and adapters (8 suites).
   - **Tier 4: Real-World Application Scenarios**: ≥5 comprehensive end-to-end scenarios executing complete SDD workflows from Phase 0 to Phase N.

---

## 2. Test Architecture: 4-Tier Test Suite Structure

```
tests/
├── e2e_test_runner.py               # Standalone test runner and opaque-box validator
└── test_cases/
    ├── tier1_feature_coverage.json  # 80 feature coverage test specifications
    ├── tier2_boundary_cases.json    # 80 boundary and corner case specifications
    ├── tier3_combinations.json      # 8 cross-feature combinatorial test suites
    └── tier4_real_world_scenarios.json # 5 full end-to-end real-world scenarios
```

---

## 3. Tier 1: Feature Coverage Catalog (80 Test Cases)

| Test ID | Feature # | Feature Name | Description & Assertion | Expected Output / Authoritative Source |
|---|---|---|---|---|
| **TC-T1-F01-01** | 1 | 7-Role Taxonomy | Orchestrator role definition: Tier 1 model, meta-controller, no direct code writing | `harnesses/multi-agent.md`, `PROJECT.md:23` |
| **TC-T1-F01-02** | 1 | 7-Role Taxonomy | Explorer role definition: Tier 2 model, read-only tools, no mutation | `harnesses/multi-agent.md`, `PROJECT.md:23` |
| **TC-T1-F01-03** | 1 | 7-Role Taxonomy | Discovery Specialist role: Brownfield audit, 6 pillars, read-only | `harnesses/multi-agent.md`, `discovery.md` |
| **TC-T1-F01-04** | 1 | 7-Role Taxonomy | Worker role definition: Implementation, <300 line PR, 2-attempt breaker | `harnesses/multi-agent.md`, `isolation.md` |
| **TC-T1-F01-05** | 1 | 7-Role Taxonomy | Reviewer, Challenger, and Forensic Auditor separation of duties and veto | `harnesses/multi-agent.md`, `review.md` |
| **TC-T1-F02-01** | 2 | 3-Tier Model Matrix | Tier 1 (Pro/Frontier) pinned to Orchestrator, Challenger, Auditor | `harnesses/multi-agent.md`, `PROJECT.md:24` |
| **TC-T1-F02-02** | 2 | 3-Tier Model Matrix | Tier 2 (Fast/Economical) pinned to Worker, Explorer, Reviewer | `harnesses/multi-agent.md`, `PROJECT.md:24` |
| **TC-T1-F02-03** | 2 | 3-Tier Model Matrix | Tier 3 (Local SLM/Ultra-Light) pinned to mechanical CRUD & boilerplate | `harnesses/multi-agent.md`, `PROJECT.md:24` |
| **TC-T1-F02-04** | 2 | 3-Tier Model Matrix | 80/20 token budget rule: ≥80% total token volume routed to Tier 2/3 | `harnesses/multi-agent.md`, `ORIGINAL_REQUEST.md:15` |
| **TC-T1-F02-05** | 2 | 3-Tier Model Matrix | Cost & latency profile definitions per tier documented in table | `harnesses/multi-agent.md` |
| **TC-T1-F03-01** | 3 | 2-Attempt Circuit Breaker | Initial build/test failure activates Attempt 1 state | `harnesses/isolation.md`, `PROJECT.md:25` |
| **TC-T1-F03-02** | 3 | 2-Attempt Circuit Breaker | Pass on Attempt 1 resets breaker and proceeds to handoff | `harnesses/isolation.md` |
| **TC-T1-F03-03** | 3 | 2-Attempt Circuit Breaker | Failure on Attempt 1 activates Attempt 2 state with error feedback | `harnesses/isolation.md` |
| **TC-T1-F03-04** | 3 | 2-Attempt Circuit Breaker | Pass on Attempt 2 completes task without escalation | `harnesses/isolation.md` |
| **TC-T1-F03-05** | 3 | 2-Attempt Circuit Breaker | Consecutive failure on Attempt 2 trips circuit breaker and halts | `harnesses/isolation.md`, `PROJECT.md:25` |
| **TC-T1-F04-01** | 4 | Multi-Environment Adapters | Antigravity adapter configuration exists in `docs/adapters/antigravity.md` | `docs/adapters/antigravity.md`, `PROJECT.md:26` |
| **TC-T1-F04-02** | 4 | Multi-Environment Adapters | Claude Code CLI adapter exists in `CLAUDE.md` and `docs/adapters/claude_code.md` | `CLAUDE.md`, `docs/adapters/claude_code.md` |
| **TC-T1-F04-03** | 4 | Multi-Environment Adapters | OpenCode / Local AI adapter exists in `OPENCODE.md` and `docs/adapters/opencode.md`| `OPENCODE.md`, `docs/adapters/opencode.md` |
| **TC-T1-F04-04** | 4 | Multi-Environment Adapters | Master instructions `AGENTS.md` loadable across all 3 runtime environments | `AGENTS.md`, `ORIGINAL_REQUEST.md:15` |
| **TC-T1-F04-05** | 4 | Multi-Environment Adapters | Tool permissions and sandboxing defined for each environment adapter | `docs/adapters/` |
| **TC-T1-F05-01** | 5 | Token & Context Protocols | File-based coordination standard: "Files for content, Messages for coordination"| `AGENTS.md`, `PROJECT.md:27` |
| **TC-T1-F05-02** | 5 | Token & Context Protocols | Message coordination envelope word limit: <300 words per message | `AGENTS.md`, `PROJECT.md:27` |
| **TC-T1-F05-03** | 5 | Token & Context Protocols | BRIEFING active situational memory bound: <100 markdown lines | `AGENTS.md`, `PROJECT.md:27` |
| **TC-T1-F05-04** | 5 | Token & Context Protocols | Append-only locked sections (`🔒 Identity`, `🔒 Constraints`) preservation | `AGENTS.md` |
| **TC-T1-F05-05** | 5 | Token & Context Protocols | Prompt prefix caching optimization (static top, dynamic payload bottom) | `AGENTS.md`, `CLAUDE.md` |
| **TC-T1-F06-01** | 6 | Pre-Change Discovery | Step 1 Repo Topography Audit protocol defined in `harnesses/discovery.md` | `harnesses/discovery.md`, `PROJECT.md:28` |
| **TC-T1-F06-02** | 6 | Pre-Change Discovery | Step 2 Dependency & Runtime engine audit (package manager, node/rust) | `harnesses/discovery.md` |
| **TC-T1-F06-03** | 6 | Pre-Change Discovery | Step 3 Verification Harness inventory (build, test, lint, typecheck) | `harnesses/discovery.md` |
| **TC-T1-F06-04** | 6 | Pre-Change Discovery | Step 4 Coding Conventions & Architectural Patterns audit (FSD, clean arch) | `harnesses/discovery.md` |
| **TC-T1-F06-05** | 6 | Pre-Change Discovery | Step 5-6 Invariants, security boundaries, and technical debt markers audit | `harnesses/discovery.md` |
| **TC-T1-F07-01** | 7 | Post-Change Closing | Step 1 Post-Implementation Verification Sign-Off (PVS) matrix definition | `harnesses/closing.md`, `PROJECT.md:29` |
| **TC-T1-F07-02** | 7 | Post-Change Closing | Step 2 Project memory synchronization (`Engram.md` ADR & status sync) | `harnesses/closing.md` |
| **TC-T1-F07-03** | 7 | Post-Change Closing | Step 3 Spec status update (marking `COMPLETADA` with date/verifier) | `harnesses/closing.md` |
| **TC-T1-F07-04** | 7 | Post-Change Closing | Step 4 Release notes & changelog generation (Keep a Changelog format) | `harnesses/closing.md` |
| **TC-T1-F07-05** | 7 | Post-Change Closing | Exit Gate Approval Checklist completeness in `harnesses/closing.md` | `harnesses/closing.md` |
| **TC-T1-F08-01** | 8 | Token-Bounded Memory | Engram 4-section layout compliance (Stack, Invariants, Decisions, Status) | `Engram.md`, `harnesses/engram.md` |
| **TC-T1-F08-02** | 8 | Token-Bounded Memory | Engram strict line count budget: 80 to 150 lines | `harnesses/engram.md`, `PROJECT.md:30` |
| **TC-T1-F08-03** | 8 | Token-Bounded Memory | Pruning protocol: archiving superseded decisions to `docs/engram_archive.md` | `harnesses/engram.md` |
| **TC-T1-F08-04** | 8 | Token-Bounded Memory | Information density rules: zero code dumps, zero conversational logs | `harnesses/engram.md` |
| **TC-T1-F08-05** | 8 | Token-Bounded Memory | Multi-agent memory lifecycle: read at start, sync at closing | `harnesses/engram.md` |
| **TC-T1-F09-01** | 9 | Adaptive Sizing Matrix | Tier 1 (Small/Hotfix): 1-3 files, ≤80 LOC, zero schema changes | `harnesses/sizing.md`, `PROJECT.md:31` |
| **TC-T1-F09-02** | 9 | Adaptive Sizing Matrix | Tier 2 (Medium/Standard): 4-10 files, 80-300 LOC, additive schema changes | `harnesses/sizing.md`, `PROJECT.md:31` |
| **TC-T1-F09-03** | 9 | Adaptive Sizing Matrix | Tier 3 (Large/Epic): >10 files, >300 LOC, core architecture / breaking | `harnesses/sizing.md`, `PROJECT.md:31` |
| **TC-T1-F09-04** | 9 | Adaptive Sizing Matrix | Quick decision tree questionnaire (4 deterministic YES/NO questions) | `harnesses/sizing.md` |
| **TC-T1-F09-05** | 9 | Adaptive Sizing Matrix | Dynamic escalation rules: automatic tier elevation upon threshold breach | `harnesses/sizing.md` |
| **TC-T1-F10-01** | 10 | Fast-Track Micro-Spec | Template file existence: `specs/_template_hotfix.md` | `specs/_template_hotfix.md`, `PROJECT.md:32` |
| **TC-T1-F10-02** | 10 | Fast-Track Micro-Spec | Template line length bound: strictly ≤25 lines | `specs/_template_hotfix.md`, `PROJECT.md:32` |
| **TC-T1-F10-03** | 10 | Fast-Track Micro-Spec | Required sections: Problem, Affected Files (≤3), Solution, Verification | `specs/_template_hotfix.md` |
| **TC-T1-F10-04** | 10 | Fast-Track Micro-Spec | Heavyweight sections omitted: no DB schema table, no REST endpoint table | `specs/_template_hotfix.md` |
| **TC-T1-F10-05** | 10 | Fast-Track Micro-Spec | Token efficiency verification: ~75% token reduction vs standard spec | `specs/_template_hotfix.md`, `PROJECT.md:32` |
| **TC-T1-F11-01** | 11 | Epic Spec Template | Template file existence: `specs/_template_epic.md` | `specs/_template_epic.md`, `PROJECT.md:33` |
| **TC-T1-F11-02** | 11 | Epic Spec Template | Milestone slice decomposition table (sub-specs <300 LOC each) | `specs/_template_epic.md` |
| **TC-T1-F11-03** | 11 | Epic Spec Template | Adversarial review gate section with Auditor/Challenger sign-off | `specs/_template_epic.md` |
| **TC-T1-F11-04** | 11 | Epic Spec Template | Rollback strategy section requirement for catastrophic failure recovery | `specs/_template_epic.md` |
| **TC-T1-F11-05** | 11 | Epic Spec Template | Global Epic closing checklist with E2E verification sign-off | `specs/_template_epic.md` |
| **TC-T1-F12-01** | 12 | Agnostic SDD Routing | Agnostic workflow routing in `harnesses/phase.md` without brand names | `harnesses/phase.md`, `PROJECT.md:34` |
| **TC-T1-F12-02** | 12 | Agnostic SDD Routing | Tri-branch phase routing diagram: Tier 1 vs Tier 2 vs Tier 3 | `harnesses/phase.md` |
| **TC-T1-F12-03** | 12 | Agnostic SDD Routing | Integration of Phase 0 Discovery and Phase N Closing into lifecycle | `harnesses/phase.md` |
| **TC-T1-F12-04** | 12 | Agnostic SDD Routing | Compatibility with ponytail minimalism rules in `harnesses/ai-rules.md` | `harnesses/ai-rules.md` |
| **TC-T1-F12-05** | 12 | Agnostic SDD Routing | Micro-spec exemption recognized under `harnesses/ai-rules.md` | `harnesses/ai-rules.md` |
| **TC-T1-F13-01** | 13 | Comprehensive Docs | `README.md` root overview with architecture summary and navigation | `README.md`, `PROJECT.md:35` |
| **TC-T1-F13-02** | 13 | Comprehensive Docs | Greenfield project quickstart walkthrough in `README.md` | `README.md` |
| **TC-T1-F13-03** | 13 | Comprehensive Docs | Existing production repo (brownfield) adoption guide in `README.md` | `README.md` |
| **TC-T1-F13-04** | 13 | Comprehensive Docs | Sizing decision tree documentation in `README.md` | `README.md` |
| **TC-T1-F13-05** | 13 | Comprehensive Docs | Model tiering and role taxonomy quick reference in `README.md` | `README.md` |
| **TC-T1-F14-01** | 14 | End-to-End Examples | Brownfield hotfix example exists in `docs/examples/example_hotfix.md` | `docs/examples/example_hotfix.md`, `PROJECT.md:36` |
| **TC-T1-F14-02** | 14 | End-to-End Examples | Greenfield feature example exists in `docs/examples/example_greenfield.md` | `docs/examples/example_greenfield.md`, `PROJECT.md:36` |
| **TC-T1-F14-03** | 14 | End-to-End Examples | Hotfix example illustrates Tier 1 micro-spec, single agent, fast closing | `docs/examples/example_hotfix.md` |
| **TC-T1-F14-04** | 14 | End-to-End Examples | Greenfield example illustrates Tier 2 standard SDD with multi-agent roles| `docs/examples/example_greenfield.md` |
| **TC-T1-F14-05** | 14 | End-to-End Examples | Examples demonstrate valid tripartite coordination message envelopes | `docs/examples/` |
| **TC-T1-F15-01** | 15 | Repo Packaging Integrity | Zero broken relative markdown links across all template files | `PROJECT.md:37`, `harnesses/` |
| **TC-T1-F15-02** | 15 | Repo Packaging Integrity | Consistent file naming and directory structure matching Code Layout | `PROJECT.md:68-112` |
| **TC-T1-F15-03** | 15 | Repo Packaging Integrity | Valid markdown syntax across all harnesses, specs, and docs | Project Repository |
| **TC-T1-F15-04** | 15 | Repo Packaging Integrity | Clean repository hygiene (.gitignore, no leftover temporary files) | Project Repository |
| **TC-T1-F15-05** | 15 | Repo Packaging Integrity | Complete file tree presence per PROJECT.md inventory (24 key files) | `PROJECT.md § Code Layout` |
| **TC-T1-F16-01** | 16 | E2E Test Suite | Automated test runner in `tests/e2e_test_runner.py` with CLI invocation | `tests/e2e_test_runner.py`, `PROJECT.md:38` |
| **TC-T1-F16-02** | 16 | E2E Test Suite | Standalone runner requires only Python 3 standard library | `tests/e2e_test_runner.py` |
| **TC-T1-F16-03** | 16 | E2E Test Suite | Validation passes all 80 Tier 1 Feature Coverage assertions | `tests/test_cases/tier1_feature_coverage.json` |
| **TC-T1-F16-04** | 16 | E2E Test Suite | Validation passes all 80 Tier 2 Boundary & Corner Case assertions | `tests/test_cases/tier2_boundary_cases.json` |
| **TC-T1-F16-05** | 16 | E2E Test Suite | Validation passes Tier 3 combinations and Tier 4 real-world scenarios | `tests/test_cases/` |

---

## 4. Tier 2: Boundary & Corner Cases Catalog (80 Test Cases)

| Test ID | Feature # | Target Boundary | Input / Boundary Condition | Expected Behavior / Failure Mode Handled |
|---|---|---|---|---|
| **TC-T2-F01-01** | 1 | Role Invariant | Orchestrator attempts direct write to `src/` | Gate Rejection: Orchestrator writes metadata only |
| **TC-T2-F01-02** | 1 | Role Invariant | Explorer invokes mutating tool (`write_to_file`) | Gate Rejection: Explorer is 100% read-only |
| **TC-T2-F01-03** | 1 | Role Invariant | Worker dispatched with unbounded prompt ("fix all bugs") | Dispatch Rejection: Requires scoped Result Contract |
| **TC-T2-F01-04** | 1 | Role Invariant | Reviewer approves PR with 450 lines changed (>300 LOC) | Review Rejection: Must reject PRs >300 lines |
| **TC-T2-F01-05** | 1 | Role Invariant | Forensic Auditor detects modified test assertion | Binary Veto: Halts merge unconditionally |
| **TC-T2-F02-01** | 2 | Model Tier Misallocation | Tier 1 Pro model dispatched to run routine linter | Policy Rejection: Tier 1 forbidden for routine lints |
| **TC-T2-F02-02** | 2 | Model Tier Misallocation | Tier 3 Local SLM dispatched for multi-module refactor | Policy Rejection: Tier 3 forbidden for epic refactors |
| **TC-T2-F02-03** | 2 | Invalid Model Tier | Dispatch specifies non-existent Tier (`Tier 0` or `Tier 4`)| Schema Rejection: Only Tiers 1, 2, 3 permitted |
| **TC-T2-F02-04** | 2 | Budget Violation | Tier 1 model volume exceeds 20% in standard execution | Budget Warning: Fails 80/20 budget ratio threshold |
| **TC-T2-F02-05** | 2 | Missing Tier Tag | Spec lacks `Model Tier` field in Result Contract | Fallback: Defaults to Tier 2 with mandatory review |
| **TC-T2-F03-01** | 3 | Circuit Breaker Bound | Worker attempts Attempt 3 after Attempt 2 failure | Hard Block: Attempt 3 strictly forbidden; must escalate |
| **TC-T2-F03-02** | 3 | Circuit Breaker Bound | Worker crashes immediately before Attempt 1 (Zero-attempt)| Error Dossier packaged: Escalates to Orchestrator |
| **TC-T2-F03-03** | 3 | Circuit Breaker Escalation | Escalation message contains 800 lines of raw log | Envelope Violation: Must save log to file, send ref |
| **TC-T2-F03-04** | 3 | Breaker Tampering | Worker modifies test assertion to pass on Attempt 2 | Forensic Auditor VETO: Reverts tamper, trips breaker |
| **TC-T2-F03-05** | 3 | Breaker State Reset | Subsequent feature dispatch inherits dirty breaker state | State Isolation: Breaker resets to 0 attempts on new task |
| **TC-T2-F04-01** | 4 | Adapter Permission Denied | Sandboxed tool execution denied in Antigravity | Graceful Fallback: Switches to direct file inspection |
| **TC-T2-F04-02** | 4 | Missing Adapter File | `docs/adapters/` missing one of the 3 required guides | Validation Failure: All 3 adapters must exist |
| **TC-T2-F04-03** | 4 | Adapter Context Bound | OpenCode runs on 8k token context window | Token Conservation: Loads only relevant sub-sections |
| **TC-T2-F04-04** | 4 | Vendor Brand Lock | Hardcoded "Claude" in agnostic `harnesses/phase.md` | Validation Failure: Zero commercial tool hardcoding |
| **TC-T2-F04-05** | 4 | Conflicting Instructions | `CLAUDE.md` and `AGENTS.md` define conflicting rules | Precedence Rule: AGENTS.md SDD rules take precedence |
| **TC-T2-F05-01** | 5 | Token Word Count Limit | Coordination message contains 650 words (>300 limit) | Protocol Violation: Receiver rejects inline bloat |
| **TC-T2-F05-02** | 5 | Active Memory Bound | `BRIEFING.md` reaches 125 lines (>100 limit) | Pruning Required: Old sections moved to archive |
| **TC-T2-F05-03** | 5 | Locked Section Integrity | Agent deletes `## 🔒 My Identity` from BRIEFING | Protocol Violation: Locked sections are append-only |
| **TC-T2-F05-04** | 5 | Raw Code Dump | Subagent pastes entire 400-line file into chat prompt | Protocol Violation: Files for content, messages for coord |
| **TC-T2-F05-05** | 5 | Cache Invalidation | Dynamic timestamp placed in line 1 of system prompt | Cache Busting Warning: Dynamic vars must be at bottom |
| **TC-T2-F06-01** | 6 | Package Manager Clashes | Agent attempts `npm install` in `pnpm` monorepo | Discovery Gate Rejection: Detected package manager mandatory |
| **TC-T2-F06-02** | 6 | Broken Baseline State | Baseline repository has 3 failing tests before change | Baseline Alert: Must document broken baseline before fix |
| **TC-T2-F06-03** | 6 | Missing Verification Tool | Legacy repository has zero test scripts in package.json | Fallback Protocol: Requires manual verification in spec |
| **TC-T2-F06-04** | 6 | Discovery Mutating Code | Discovery Specialist edits `src/index.ts` | Role Invariant Rejection: Discovery is 100% read-only |
| **TC-T2-F06-05** | 6 | Truncated Discovery | `harnesses/discovery.md` missing Step 5 (Invariants) | Schema Validation Failure: All 6 steps mandatory |
| **TC-T2-F07-01** | 7 | Premature Exit | Worker declares done while unit tests fail | PVS Gate Rejection: 100% tests must pass before closing |
| **TC-T2-F07-02** | 7 | Stale Spec Status | Spec remains `IN PROGRESS` after PR merged | Closing Gate Rejection: Must mark `COMPLETADA` with date |
| **TC-T2-F07-03** | 7 | Incomplete Changelog | Changelog entry lacks `Fixed:` or `Changed:` section | Format Rejection: Must follow Keep a Changelog |
| **TC-T2-F07-04** | 7 | Omitted Engram Sync | Feature merged without updating `Engram.md` | Closing Gate Rejection: Engram sync is non-negotiable |
| **TC-T2-F07-05** | 7 | Missing Closing Harness | `harnesses/closing.md` deleted or missing | Structural Failure: Core closing lifecycle missing |
| **TC-T2-F08-01** | 8 | Engram Line Overflow | `Engram.md` grows to 210 lines (>150 line limit) | Budget Failure: Exceeds 150 lines; pruning mandatory |
| **TC-T2-F08-02** | 8 | Engram Underflow / Missing | `Engram.md` has 30 lines and omits `## 4. Estado` | Structural Failure: Must contain all 4 sections |
| **TC-T2-F08-03** | 8 | Engram Code Bloat | Full 50-line code snippet pasted into `Engram.md` | Density Violation: Facts & decisions only, no code dumps |
| **TC-T2-F08-04** | 8 | Missing Engram Harness | `harnesses/engram.md` deleted or corrupted | Structural Failure: Memory rules missing |
| **TC-T2-F08-05** | 8 | Incomplete ADR Record | ADR entry in Engram missing "alternative discarded" | Quality Failure: Decision must document rationale |
| **TC-T2-F09-01** | 9 | Sizing File Boundary | Change modifies 4 files under Tier 1 Fast-Track | Escalation Trigger: Tier 1 max 3 files -> Escalate to Tier 2 |
| **TC-T2-F09-02** | 9 | Sizing LOC Boundary | Change introduces 95 LOC under Tier 1 Fast-Track | Escalation Trigger: Tier 1 max 80 LOC -> Escalate to Tier 2 |
| **TC-T2-F09-03** | 9 | Sizing Schema Boundary | Change alters database table under Tier 1 Fast-Track | Escalation Trigger: Schema change -> Escalate to Tier 2/3 |
| **TC-T2-F09-04** | 9 | Sizing Ambiguity | Task spans 11 files with 250 LOC | Classification Decision: >10 files triggers Tier 3 Epic |
| **TC-T2-F09-05** | 9 | Missing Sizing Harness | `harnesses/sizing.md` missing | Structural Failure: Adaptive sizing matrix required |
| **TC-T2-F10-01** | 10 | Micro-Spec Length Limit | `specs/_template_hotfix.md` contains 38 lines (>25 limit)| Template Bloat Failure: Micro-spec must be ≤25 lines |
| **TC-T2-F10-02** | 10 | Micro-Spec File Overrun | Micro-spec lists 5 affected files | Schema Rejection: Max 3 files in micro-spec |
| **TC-T2-F10-03** | 10 | Micro-Spec Missing Test | Micro-spec omits Verification command | Quality Rejection: Verification command mandatory |
| **TC-T2-F10-04** | 10 | Micro-Spec Schema Bloat | Micro-spec includes database schema table | YAGNI Violation: Heavyweight tables forbidden in hotfix |
| **TC-T2-F10-05** | 10 | Micro-Spec Missing Done | Micro-spec lacks Section 5 Checklist | Quality Rejection: Quick closing checklist mandatory |
| **TC-T2-F11-01** | 11 | Epic Slice Overrun | Sub-spec slice in Epic specifies 600 LOC (>300 limit) | Decomposition Failure: Slices must be <300 LOC |
| **TC-T2-F11-02** | 11 | Bypassed Adversarial Gate| Implementation begins without Challenger approval | Gate Violation: Code prohibited until gate signed |
| **TC-T2-F11-03** | 11 | Missing Rollback Plan | `specs/_template_epic.md` omits rollback section | Safety Failure: Rollback strategy mandatory for Epics |
| **TC-T2-F11-04** | 11 | Missing Discovery Ref | Epic spec drafted without referencing Phase 0 Discovery | Audit Failure: Epic must link to discovery findings |
| **TC-T2-F11-05** | 11 | Circular Epic Slices | Slice M2 depends on M3 which depends on M2 | Dependency Graph Error: Slices must form a DAG |
| **TC-T2-F12-01** | 12 | Brand Coupling in Phase | `harnesses/phase.md` specifies `SPEC (Claude)` | Vendor Coupling Rejection: Must use generic roles |
| **TC-T2-F12-02** | 12 | Illegal Phase Skipping | Tier 2 standard change attempts to bypass DESIGN phase | SDD Protocol Violation: Tier 2 requires DESIGN phase |
| **TC-T2-F12-03** | 12 | Missing Discovery Phase | Brownfield project skips Phase 0 Discovery | Audit Violation: Brownfield changes require Phase 0 |
| **TC-T2-F12-04** | 12 | Missing Closing Phase | Task marked done without executing Phase N Closing | Lifecycle Violation: All tiers must execute Phase N |
| **TC-T2-F12-05** | 12 | Terminology Mismatch | `phase.md` and `ai-rules.md` use conflicting phase names| Alignment Failure: Phases must align across harnesses |
| **TC-T2-F13-01** | 13 | Missing Quickstart Cmd | `README.md` lacks copy-pasteable initiation command | Usability Defect: Quickstart commands mandatory |
| **TC-T2-F13-02** | 13 | Undocumented Env Var | Template uses required env var missing from docs | Docs Defect: All prerequisites must be documented |
| **TC-T2-F13-03** | 13 | Broken README Link | `README.md` links to non-existent `harnesses/foo.md` | Link Integrity Failure: Dead internal link |
| **TC-T2-F13-04** | 13 | Sizing Matrix Desync | Sizing thresholds in README disagree with sizing.md | Consistency Defect: README must match sizing.md |
| **TC-T2-F13-05** | 13 | Truncated Docs Section | `README.md` contains placeholder `[TODO: write this]` | Completeness Failure: Zero placeholder content |
| **TC-T2-F14-01** | 14 | Missing Hotfix Example | `docs/examples/example_hotfix.md` missing | Completeness Failure: Hotfix walkthrough mandatory |
| **TC-T2-F14-02** | 14 | Missing Greenfield Ex | `docs/examples/example_greenfield.md` missing | Completeness Failure: Greenfield walkthrough mandatory |
| **TC-T2-F14-03** | 14 | Hotfix Example Size Bloat| Hotfix example modifies 6 files (Tier 1 limit is 3) | Example Defect: Example must obey Tier 1 rules |
| **TC-T2-F14-04** | 14 | Message Bloat in Example | Example coordination message contains 900 words | Example Defect: Example must obey <300 word rule |
| **TC-T2-F14-05** | 14 | Dead Reference in Ex | Example references non-existent spec or code file | Integrity Defect: All example paths must be valid |
| **TC-T2-F15-01** | 15 | Dead Anchor Link | Markdown contains `[text](#heading-does-not-exist)` | Link Integrity Failure: Broken anchor link |
| **TC-T2-F15-02** | 15 | Dead Relative Path | Markdown contains `[text](../missing_file.md)` | Link Integrity Failure: Broken relative path |
| **TC-T2-F15-03** | 15 | Unclosed Code Fence | Markdown file has unclosed ```` fence | Syntax Failure: Malformed markdown block |
| **TC-T2-F15-04** | 15 | Path Separator Bug | Markdown link uses backslashes `harnesses\phase.md` | Portability Failure: Must use forward slashes |
| **TC-T2-F15-05** | 15 | Non-UTF8 Character | Markdown file contains corrupted multibyte byte sequence| Encoding Failure: All files must be UTF-8 clean |
| **TC-T2-F16-01** | 16 | Runner Missing Target | Runner invoked on non-existent directory path | Error Handling: Returns exit code 2 with clear msg |
| **TC-T2-F16-02** | 16 | Corrupted Test Case JSON| `tier1_feature_coverage.json` contains invalid JSON | Error Handling: Runner reports JSON syntax error |
| **TC-T2-F16-03** | 16 | Assertion Failure Exit | One test case fails in test suite | Runner Contract: Exits with non-zero exit code (1) |
| **TC-T2-F16-04** | 16 | Tampered Assertion | Test assertion modified from `assert x == 5` to `assert True`| Forensic Integrity: Runner verifies assertion hash |
| **TC-T2-F16-05** | 16 | Broken JSON Report | Runner `--json-report` crashes or writes invalid JSON | Runner Contract: Guaranteed valid JSON output |

---

## 5. Tier 3: Cross-Feature Combinations (8 Test Suites)

Tier 3 validates pairwise and multi-variable interactions across system boundaries:

### Suite T3-COMB-01: Sizing (Tier 1) × Discovery (Light) × Micro-Spec × Worker (Tier 2/3) × Fast Closing
- **Interaction**: A localized bugfix in a production monorepo.
- **Workflow Path**:
  1. Discovery performs Steps 1-3 only (Topography, Dependencies, Verification commands).
  2. Sizing evaluates 4 questions -> All NO -> Tier 1 (Small / Hotfix).
  3. Micro-spec drafted in `specs/_template_hotfix.md` (≤25 lines, ≤3 files).
  4. Worker (Fast/Flash model) performs single-turn implementation.
  5. 2-Attempt Circuit Breaker initialized to 0. Test command executes successfully.
  6. Closing executes abbreviated PVS sign-off and records one-line status in `Engram.md`.
- **Invariants Verified**:
  - Micro-spec never exceeds 25 lines.
  - Zero heavy architectural artifacts generated.
  - Token consumption < 4,500 tokens.

### Suite T3-COMB-02: Sizing Escalation × Circuit Breaker × Full SDD Switch
- **Interaction**: Task initially scoped as Tier 1 hotfix discovers schema dependency during implementation.
- **Workflow Path**:
  1. Worker begins implementation under Tier 1 micro-spec.
  2. Worker identifies need to alter a shared database schema in `src/shared/`.
  3. Sizing dynamic escalation rule triggers immediately.
  4. Circuit breaker halts execution before modifying schema.
  5. Task escalates to Orchestrator (Tier 1 model).
  6. Specification converted to standard `specs/_template.md` (Tier 2).
  7. Full SDD flow activated: SPEC -> DISEÑO -> IMPLEMENTACIÓN -> VERIFICACIÓN.
- **Invariants Verified**:
  - No unauthorized schema modification without Tier 2 spec.
  - Breaker prevents out-of-scope file modifications (>3 files).

### Suite T3-COMB-03: Multi-Agent Role Chain (Orchestrator → Worker → Reviewer) × 80/20 Token Budget
- **Interaction**: Standard feature implementation delegating across model tiers.
- **Workflow Path**:
  1. Orchestrator (Tier 1 Pro model) drafts spec and Result Contract in `specs/`.
  2. Orchestrator dispatches Worker (Tier 2 Flash model) using tripartite message (<300 words).
  3. Worker writes code and executes local tests.
  4. Worker submits handoff to Reviewer (Tier 2 Fast model).
  5. Reviewer validates line count (<300 LOC), style, and acceptance criteria.
  6. Closing signs off PVS and synchronizes `Engram.md`.
- **Invariants Verified**:
  - Orchestrator touches 0 source code files.
  - ≥80% total tokens executed on Tier 2 model.
  - PR changes strictly ≤300 lines.

### Suite T3-COMB-04: Tier 3 Epic Sizing × Full Discovery × Milestone Slices × Adversarial Challenger × Forensic Auditor
- **Interaction**: High-assurance architectural migration.
- **Workflow Path**:
  1. Discovery Specialist executes all 6 discovery steps, compiling `ARCH_AUDIT.md`.
  2. Sizing classifies as Tier 3 Epic (>10 files, architectural impact).
  3. Orchestrator drafts `specs/_template_epic.md`, decomposing into 3 slices (<300 LOC each).
  4. Challenger (Tier 1 Pro) reviews design and stress-tests assumptions.
  5. Challenger signs Adversarial Review Gate before code implementation.
  6. Workers execute slices sequentially.
  7. Forensic Auditor performs integrity audit (checks for test tampering, lockfile stability).
  8. Final sign-off updates `Engram.md` (<150 lines) and generates Keep a Changelog entry.
- **Invariants Verified**:
  - Implementation blocked until Adversarial Gate signed.
  - Slices form an acyclic dependency graph.
  - Forensic Auditor retains absolute binary veto.

### Suite T3-COMB-05: Multi-Environment Adapters × 7 Roles × Sandboxing Fallbacks
- **Interaction**: Identical SDD task executed in Antigravity, Claude Code, and OpenCode.
- **Workflow Path**:
  1. Evaluates `AGENTS.md` loadability in Antigravity system prompt.
  2. Evaluates `CLAUDE.md` loadability in Claude Code CLI session.
  3. Evaluates `OPENCODE.md` in local SLM environment (Qwen 2.5 Coder).
  4. In Antigravity: Sandboxed command denial gracefully falls back to native file tools.
  5. In OpenCode: Constrained context limits (8k-32k) load only needed harnesses.
- **Invariants Verified**:
  - Zero tool execution lockup.
  - Universal role contracts respected across all 3 environments.

### Suite T3-COMB-06: Token Budget Rules × File-Based Coordination × 100-Line BRIEFING × 150-Line Engram
- **Interaction**: Long-running multi-session project memory preservation.
- **Workflow Path**:
  1. Multi-agent communication uses file references exclusively (`specs/`, `handoff.md`).
  2. All agent coordination messages verified <300 words.
  3. Agent `BRIEFING.md` monitored: when approaching 100 lines, past entries archived.
  4. Repository `Engram.md` monitored: when approaching 150 lines, completed features pruned to `docs/engram_archive.md`.
- **Invariants Verified**:
  - BRIEFING.md strictly <100 lines.
  - Engram.md strictly between 80 and 150 lines.
  - Zero quadratic conversational token bloat.

### Suite T3-COMB-07: Worker Retry Failure × 2-Attempt Breaker Escalation × Orchestrator Intervention
- **Interaction**: Worker encounters recurring compilation and type errors.
- **Workflow Path**:
  1. Worker executes implementation; local build fails (Attempt 1).
  2. Worker analyzes compiler error and applies fix.
  3. Re-test fails with secondary type error (Attempt 2).
  4. 2-Attempt Circuit Breaker trips: Worker immediately halts execution.
  5. Worker compiles error dossier with verbatim compiler output and file diff.
  6. Escalation sent to Orchestrator; Orchestrator intervenes with Tier 1 reasoning.
- **Invariants Verified**:
  - Worker NEVER attempts Attempt 3.
  - Zero token burn in runaway retry loops.

### Suite T3-COMB-08: Adversarial Threat Detection × Forensic Integrity Violation × Rollback Execution
- **Interaction**: Worker introduces an insecure library and weakens test assertions.
- **Workflow Path**:
  1. Worker modifies unit test assertion to pass failing edge case.
  2. Reviewer fails to catch assertion change.
  3. Challenger identifies edge-case vulnerability.
  4. Forensic Auditor audits diff against base: detects loosened assertion.
  5. Forensic Auditor executes BINARY VETO (Release Blocked).
  6. Rollback protocol executed per Epic spec rollback strategy.
- **Invariants Verified**:
  - Binary veto unconditionally blocks merge.
  - Repository rolls back to clean baseline state.

---

## 6. Tier 4: Real-World Application Scenarios (5 End-to-End Scenarios)

### Scenario 1: Production Hotfix in Brownfield Monorepo (UI Token Mismatch)
- **Context**: A production bug where CTA buttons in `/contacto` have broken hover states due to CSS token drift.
- **Step 1 (Discovery Light)**: Agent verifies repository type (Astro + Tailwind monorepo), lockfile (`pnpm-lock.yaml`), and test command (`pnpm build`).
- **Step 2 (Sizing)**: 1 file affected (`src/styles/tokens.css`), 6 lines changed, 0 schema changes -> Tier 1 Fast-Track.
- **Step 3 (Micro-Spec)**: Author `specs/hotfixes/20261029-button-hover.md` (21 lines) using `specs/_template_hotfix.md`.
- **Step 4 (Execution)**: Worker applies minimal CSS fix adhering to ponytail rules.
- **Step 5 (Verification & Closing)**: `pnpm build` succeeds; Acceptance Criterion verified; commit created `fix: repair cta button hover token`; one-line entry added to `Engram.md`.
- **Expected Outcome**: Task completed in single turn, 0 broken links, <3,000 tokens consumed.

### Scenario 2: Greenfield Full-Stack Feature (User Profile API & UI View)
- **Context**: Greenfield web application requires a new User Profile screen and supporting REST endpoint.
- **Step 1 (Discovery)**: Greenfield repository inspection confirms Feature-Sliced Design in `src/features/` and Axum backend in `src/api/`.
- **Step 2 (Sizing)**: 6 files affected, ~180 lines, additive types -> Tier 2 Standard SDD.
- **Step 3 (Spec & Design)**: Orchestrator (Tier 1 model) writes `specs/004-user-profile.md` using `specs/_template.md` with Gherkin ACs and Zod types.
- **Step 4 (Implementation)**: Worker (Tier 2 model) implements endpoint and UI view; unit tests pass on Attempt 1.
- **Step 5 (Review & Closing)**: Reviewer certifies PR (<300 LOC); PVS sign-off executed; `Engram.md` updated with new module status; spec marked `COMPLETADA`.
- **Expected Outcome**: Clean modular architecture, complete test coverage, 80% tokens on Tier 2 model.

### Scenario 3: Architectural Migration Epic (Database Adapter Replacement)
- **Context**: Replacing local in-memory store with PostgreSQL persistence layer across 14 modules.
- **Step 1 (Full Discovery)**: Discovery Specialist conducts 6-pillar audit, documenting existing invariants and DB queries in `ARCH_AUDIT.md`.
- **Step 2 (Sizing)**: 14 files, >400 LOC, database schema changes -> Tier 3 Epic.
- **Step 3 (Epic Spec & Decomposition)**: Orchestrator drafts `specs/005-postgres-migration.md` decomposing into 3 independent slices: M1 (Connection & Schemas), M2 (Repository Implementations), M3 (Integration Tests & Rollback).
- **Step 4 (Adversarial Gate)**: Challenger audits connection pooling and transaction rollbacks; signs Adversarial Review Gate.
- **Step 5 (Phased Execution)**: Workers implement M1, M2, M3 sequentially. Forensic Auditor verifies test integrity and lockfile stability.
- **Step 6 (Comprehensive Closing)**: Full PVS sign-off, ADR written to `docs/adr/005-postgres.md`, `Engram.md` consolidated to 120 lines, changelog generated.
- **Expected Outcome**: Zero regressions, zero unhandled errors, verified rollback strategy.

### Scenario 4: Worker Circuit Breaker Escalation during Legacy Integration
- **Context**: Worker integrating legacy payment gateway encounters subtle SDK signature mismatch.
- **Step 1 (Execution)**: Worker writes adapter code and runs test. Test fails with `SignatureVerificationError` (Attempt 1).
- **Step 2 (Correction)**: Worker modifies hashing parameter and re-runs test. Test fails with `MalformedPayloadException` (Attempt 2).
- **Step 3 (Circuit Breaker Trip)**: Worker immediately halts execution. Does NOT attempt Attempt 3.
- **Step 4 (Escalation)**: Worker packages verbatim error output and git diff into `.agents/teamwork/worker/escalation.md`.
- **Step 5 (Orchestrator Resolution)**: Orchestrator (Tier 1 model) reviews legacy SDK documentation, identifies endianness requirement, and provides corrected contract.
- **Step 6 (Recovery & Pass)**: Worker applies corrected contract; test passes on next run.
- **Expected Outcome**: Circuit breaker prevents infinite retry loop; token exhaustion averted.

### Scenario 5: Adversarial Red-Team & Forensic Integrity Audit
- **Context**: Worker attempts to bypass a flaky edge-case test by commenting out an assertion before merging.
- **Step 1 (Worker Action)**: Worker modifies `test_token_expiry` assertion from `assert expiry > now` to `assert True`.
- **Step 2 (Reviewer Check)**: Reviewer checks formatting and style; misses subtle assertion modification.
- **Step 3 (Adversarial Challenger Probing)**: Challenger red-teams token expiration logic; flags missing expiration validation under clock drift.
- **Step 4 (Forensic Auditor Audit)**: Forensic Auditor compares git diff against `main` for all files in `tests/`. Detects altered assertion.
- **Step 5 (Binary Veto)**: Forensic Auditor issues **INTEGRITY VIOLATION VETO**. Merging is unconditionally blocked.
- **Step 6 (Remediation)**: Worker rolls back assertion tampering, fixes underlying clock drift bug, and re-submits.
- **Expected Outcome**: Test tampering prevented; zero integrity compromise.

---

## 7. Automated Test Runner Architecture (`tests/e2e_test_runner.py`)

### 7.1 Architecture & Capabilities
The test runner is a standalone, single-file Python script designed to execute in any environment with Python 3.8+:
```
python tests/e2e_test_runner.py [OPTIONS]
```

### 7.2 Core Validation Modules
1. **File Inventory & Topography Validator**:
   - Asserts existence of all 24 canonical files across `AGENTS.md`, `CLAUDE.md`, `OPENCODE.md`, `Engram.md`, `harnesses/`, `specs/`, `docs/adapters/`, `docs/prompts/`, and `docs/examples/`.
2. **Markdown Syntax & Section Validator**:
   - Parses markdown headers, ensures all required sections exist (e.g. 6 steps in `discovery.md`, 4 stages in `closing.md`, 4 sections in `Engram.md`).
3. **Link & Anchor Cross-Reference Integrity Checker**:
   - Parses all relative links (`[text](path.md)`) and internal anchors (`[text](#anchor)`).
   - Resolves target files and verifies that target headings exist. Zero broken links tolerated.
4. **Role Definition & Model Tiering Matrix Validator**:
   - Verifies all 7 roles are defined with input/output contracts and tool permissions.
   - Verifies 3 model tiers (Tier 1 Pro, Tier 2 Fast, Tier 3 Local) and 80/20 token budget rule.
5. **Circuit Breaker State Machine Engine**:
   - Validates the 2-attempt limit rule, immediate halt on Attempt 2 failure, and escalation contract.
6. **Sizing Decision Tree & Line Bound Validator**:
   - Validates Tier 1 (≤3 files, ≤80 LOC, ≤25 lines spec), Tier 2 (4-10 files, 80-300 LOC), Tier 3 (>10 files, >300 LOC).
   - Validates Engram line count budget (80-150 lines) and BRIEFING line count (<100 lines).

### 7.3 CLI Options
```
--repo PATH          Path to template repository root (default: current directory)
--tier {1,2,3,4,all} Run specific test tier (default: all)
--verify-suite       Validate consistency of test suite JSON files
--json-report PATH   Export test execution results to JSON file
--verbose            Enable detailed step-by-step diagnostic output
```

### 7.4 Exit Codes
- `0`: All tests passed successfully (100% compliance).
- `1`: Test assertion or validation failure detected.
- `2`: Invalid CLI invocation, missing arguments, or file read error.

---

## 8. Expected Output Derivation Methodology & Oracles

### Authoritative Reference Hierarchy
1. **Primary Oracle**: `ORIGINAL_REQUEST.md` (Approved Task Prompt & Acceptance Criteria).
2. **Architectural Oracle**: `PROJECT.md` (Features 1-16, Interface Contracts, Code Layout).
3. **Specification Mines**: `teamwork_spec_miner_survey_2/analysis.md` and `teamwork_explorer_survey_3/analysis.md`.
4. **Deterministic Properties**: Exact line bounds (≤25 lines for micro-spec, 80-150 lines for Engram, <100 lines for BRIEFING), file counts (≤3 for Tier 1, 4-10 for Tier 2, >10 for Tier 3), and maximum retry bounds (2 attempts).

Every test assertion in `tests/test_cases/*.json` explicitly records its `authoritative_source` and `assertion_logic`.
