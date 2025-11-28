# Lattice-Lock Project Context

## Overview

**Lattice-Lock** is a governance-first, deterministic code generation framework (v2.1) designed by Kevin Lappe to address fundamental failures in current LLM-based multi-agent software development systems. The framework is positioned for publication at ACM TOSEM (Transactions on Software Engineering and Methodology).

---

## The Problem It Solves

Current multi-agent coding systems like MetaGPT, SWE-agent, ChatDev, and OpenDevin suffer from three critical threat categories:

| Threat | Description | Root Cause |
|--------|-------------|------------|
| **T1: Structural Drift** | Hallucinated types, circular imports, inconsistent signatures | Absence of pre-existing type definitions |
| **T2: Stylistic Drift** | Forbidden libraries, unidiomatic patterns ("spaghetti code") | Delayed or absent feedback loops |
| **T3: Semantic Drift** | Code compiles but violates business invariants (e.g., negative prices) | Lack of executable business rule contracts |

Research shows 50-60% of agent outputs contain critical inconsistencies when task complexity increases.

---

## Core Innovation: The Paradigm Shift

Lattice-Lock transforms code generation from **probabilistic synthesis** into **constrained assembly**. Rather than treating implementation as a creative writing exercise, it constrains agents to fill business logic within pre-defined, rigorously typed scaffolds.

---

## Architecture: The "Governance Cage"

```
┌─────────────────────────────────────────────────────────┐
│              PRE-COMPUTATION PHASE                       │
│                                                          │
│   lattice.yaml ──► Polyglot Compiler ──► Governance Cage │
│   (Single Source                         ├── types_vX.py (Pydantic)
│    of Truth)                             ├── models_vX.py (SQLModel ORM)
│                                          ├── migrations/ (Alembic)
│                                          └── test_contracts_vX.py (Gauntlet)
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│         IMPLEMENTATION & ENFORCEMENT PHASE               │
│                                                          │
│   Mason Agent Swarm ──► Implementation Code              │
│         │                     │                          │
│         ▼                     ▼                          │
│   Sheriff (AST) ◄────────────┴────────► The Gauntlet     │
│         │                                    │           │
│         └────────► PASS / REJECT + Feedback ◄┘           │
└─────────────────────────────────────────────────────────┘
```

---

## Key Components

### 1. `lattice.yaml`—The Single Source of Truth

A versioned declarative schema defining entities, invariants, forbidden imports, and service interfaces.

**Key Features:**
- Semantic constraints via `ensures:` clauses
- Database persistence configuration (`persistence: table`)
- Field-level invariants (`gt: 0`, `unique: true`, `scale: 8`)
- Forbidden imports list (e.g., `requests`, `psycopg2`, `float`)
- Interface definitions with scaffold patterns

**Example Structure:**

```yaml
version: v2.1
generated_module: types_v2

config:
  forbidden_imports:
    - requests
    - psycopg2
    - sqlite3
    - float
  orm_engine: sqlmodel

entities:
  LimitOrder:
    description: "A priced limit order on a centralized exchange"
    persistence: table
    fields:
      order_id: { type: uuid, primary_key: true }
      price: { type: decimal, gt: 0, scale: 8 }
      quantity: { type: decimal, gt: 0, scale: 8 }
      side: { enum: [buy, sell] }
      status: { enum: [pending, partially_filled, filled, cancelled, rejected], default: pending }

interfaces:
  IExecutionService:
    file: src/services/execution_service.py
    scaffold: repository_pattern
    methods:
      place_order:
        params:
          order: LimitOrder
        returns: ExecutionReport
        ensures:
          - "result.executed_price == order.price"
          - "0 < result.executed_qty <= order.remaining_quantity"
```

### 2. Polyglot Compiler (`compile_lattice.py`)

Transforms YAML into multiple synchronized enforcement artifacts:

- **Pydantic models**—API contracts and validation
- **SQLModel ORM**—Database schema definitions
- **Alembic migrations**—Database version control
- **Pytest contracts**—Auto-generated test suites

### 3. Governance Stack (Four Enforcement Layers)

| Layer | Mechanism | Enforced Property | Latency |
|-------|-----------|-------------------|---------|
| **Meta-Schema Validator** | JSONSchema + LLM repair loop | lattice.yaml validity | <3s |
| **Polyglot Compiler** | Declarative → multi-target emitter | Structural identity | Instant |
| **Sheriff (AST)** | AST analysis + version checks | Import discipline, type hints | Instant |
| **The Gauntlet** | Auto-generated pytest contracts | Semantic post-conditions (ensures) | <8s |

### 4. Agent Roles

#### Architect Agent

- Outputs ONLY valid `lattice.yaml` files
- Never writes implementation code
- Defines entities, invariants, interfaces, and scaffold patterns

#### Mason Agent

- Implements business logic within pre-defined scaffolds
- Imports ONLY from generated shared modules
- Must pass Sheriff syntax checks AND Gauntlet unit tests
- Restarted with exact error feedback on failure

#### Orchestrator

- Uses topological sorting for dependency-aware parallel execution
- Orders file generation based on `depends_on` declarations

---

## Production Results

Deployed across three regulated domains in late 2025:

| System | Domain | Schema Lines | Generated Code | Schema ROI | Structural Defects | Logic Interventions |
|--------|--------|--------------|----------------|------------|-------------------|---------------------|
| Payments Matching | Fintech | 415 | 40,127 | 96× | **0** | 12 (0.03%) |
| Clinical Trials | Healthcare | 380 | 28,446 | 74× | **0** | 45 (0.15%) |
| SaaS Inventory | Legacy Modernization | 210 | 18,200 | 86× | **0** | 8 (0.04%) |

### Key Metrics

- **100% structural compliance**—All T1 errors caught at compile time
- **>99% semantic compliance**—300× improvement over unconstrained benchmarks
- **Schema ROI**—74× to 96× code amplification from schema definition

---

## Competitive Differentiation

| Framework | Schema-First | Hard Types Pre-Gen | Semantic Contracts | DB Sync |
|-----------|--------------|--------------------|--------------------|---------|
| MetaGPT | No | No | No | No |
| SWE-agent | No | No | No | No |
| OpenDevin | No | No | No | Partial |
| **Lattice-Lock** | **Yes** | **Yes** | **Yes** | **Yes** |

---

## Glossary of Key Terms

| Term | Definition |
|------|------------|
| **Architect Agent** | High-level LLM tasked with defining system structure via valid lattice.yaml output |
| **AST Enforcement** | Abstract Syntax Tree analysis by the Sheriff for instant structural validation |
| **Compiler** | Deterministic script transforming lattice.yaml into enforcement artifacts |
| **Constrained Assembly** | Paradigm treating code as filling logic within pre-defined typed modules |
| **Determinism** | Guarantee of consistent, predictable output regardless of LLM probabilistic nature |
| **Interface Drift** | Failure where agents invent non-existent APIs or data structures |
| **lattice.yaml** | Single Source of Truth defining all entities, invariants, and interfaces |
| **Mason Agent** | Implementation-focused LLM filling business logic inside scaffolds |
| **Meta-Schema Validator** | JSONSchema-based validator for lattice.yaml correctness |
| **Polyglot Compiler** | Enhanced compiler emitting Pydantic, SQLModel, Alembic, and Pytest artifacts |
| **Schema ROI** | Ratio of generated code lines to schema definition lines |
| **Semantic Drift** | Code that compiles but violates business invariants |
| **Sheriff** | AST-based enforcer for import discipline and type hints |
| **Style Divergence** | Inconsistent, unmaintainable code from varied agent styles |
| **The Gauntlet** | Auto-generated pytest suite enforcing semantic contracts |
| **Topological Sorting** | Orchestrator method for dependency-aware parallel execution |

---

## Supporting Research Context

The project folder includes related papers examining:

1. **LLM Hallucinations in Practical Code Generation**—Taxonomy of hallucination types (Task Requirement, Factual Knowledge, Project Context conflicts)
2. **SemGuard**—Real-time semantic evaluation during the decoding process
3. **CVCP (Cross-Verification Collaboration Protocol)**—Multi-agent protocols for competitive programming with symmetry-aware coordination
4. **MetaGPT / OpenHands**—Comparative multi-agent frameworks using SOPs and role specialization
5. **Show and Tell**—Prompt strategies for style control in multi-turn code generation
6. **Code in Harmony**—Survey evaluating multi-agent framework architectures and benchmarks

### Lattice-Lock's Unique Contribution

While other frameworks focus on post-hoc repair or runtime feedback loops, Lattice-Lock eliminates defects at the source through **pre-compilation enforcement**. The "Governance Cage" ensures agents operate within strictly defined boundaries before any implementation begins.

---

## Target Publication

**Venue:** ACM Transactions on Software Engineering and Methodology (TOSEM)

**Date:** November 27, 2025

**Author:** Kevin Lappe

---

*Document generated: November 28, 2025*
