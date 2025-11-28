# Lattice-Lock Framework

**A governance-first, deterministic code generation framework for multi-agent LLM systems**

[![Version](https://img.shields.io/badge/version-2.1.0-blue.svg)](./version.txt)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](./LICENSE)
[![Status](https://img.shields.io/badge/status-production--ready-brightgreen.svg)]()

## Overview

Lattice-Lock transforms LLM-based code generation from **probabilistic synthesis** into **constrained assembly**. By enforcing governance through pre-compiled type definitions and semantic contracts, it achieves 100% structural compliance and >99% semantic compliance in production systems.

### The Problem

Current multi-agent coding systems (MetaGPT, SWE-agent, ChatDev, OpenDevin) suffer from three critical threats:

| Threat | Description | Impact |
|--------|-------------|--------|
| **T1: Structural Drift** | Hallucinated types, circular imports, inconsistent signatures | 50-60% defect rate |
| **T2: Stylistic Drift** | Forbidden libraries, unidiomatic patterns, "spaghetti code" | Unmaintainable output |
| **T3: Semantic Drift** | Code compiles but violates business invariants | Silent failures |

### The Solution

Lattice-Lock implements a **"Governance Cage"** architecture that constrains agents to fill business logic within pre-defined, rigorously typed scaffolds:

```
┌─────────────────────────────────────────────────────┐
│         PRE-COMPUTATION PHASE                        │
│   lattice.yaml ──► Compiler ──► Governance Cage     │
│                                  ├── types_vX.py     │
│                                  ├── models_vX.py    │
│                                  ├── migrations/     │
│                                  └── test_contracts/ │
└─────────────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────┐
│      IMPLEMENTATION & ENFORCEMENT PHASE              │
│   Mason Agents ──► Implementation ──► Sheriff ──► Pass/Reject │
└─────────────────────────────────────────────────────┘
```

## Key Features

- **Single Source of Truth**: `lattice.yaml` defines all entities, invariants, and interfaces
- **Deterministic Compilation**: YAML → Python types, ORM models, migrations, tests
- **Four-Layer Governance**: Meta-schema validation, compilation, AST enforcement, semantic testing
- **Zero Structural Defects**: 100% compliance in production deployments
- **Massive Schema ROI**: 74-96× code amplification from schema definitions

## Production Results

Deployed across three regulated domains (2025):

| System | Domain | Schema Lines | Generated Code | Structural Defects | Logic Interventions |
|--------|--------|--------------|----------------|-------------------|---------------------|
| Payments Matching | Fintech | 415 | 40,127 | **0** | 12 (0.03%) |
| Clinical Trials | Healthcare | 380 | 28,446 | **0** | 45 (0.15%) |
| SaaS Inventory | Legacy Modernization | 210 | 18,200 | **0** | 8 (0.04%) |

## Quick Start

### Prerequisites

- Python 3.10+
- YAML support
- LLM API access (Claude 3.5 Sonnet or GPT-4o recommended)

### Installation

```bash
# Clone the repository
git clone https://github.com/klappe-pm/lattice-lock-framework.git
cd lattice-lock-framework

# Install dependencies
pip install -r requirements.txt

# Compile your first lattice
python compile_lattice.py
```

### Basic Usage

1. **Define your schema** in `lattice.yaml`:
```yaml
version: v2.1
generated_module: types_v2

entities:
  LimitOrder:
    description: "A priced limit order"
    fields:
      order_id: uuid
      price: { type: decimal, gt: 0 }
      quantity: { type: decimal, gt: 0 }
      side: { enum: [buy, sell] }
```

2. **Compile to enforceable types**:
```bash
python compile_lattice.py
```

3. **Let Mason agents implement** within the governance cage

4. **Sheriff validates** all code automatically

## Core Components

### 1. lattice.yaml
Versioned declarative schema defining entities, invariants, forbidden imports, and service interfaces.

### 2. Polyglot Compiler
Transforms YAML into synchronized enforcement artifacts:
- Pydantic models (API contracts)
- SQLModel ORM (database schema)
- Alembic migrations (version control)
- Pytest contracts (semantic validation)

### 3. Governance Stack

| Layer | Mechanism | Enforces | Latency |
|-------|-----------|----------|---------|
| Meta-Schema | JSONSchema + LLM repair | YAML validity | <3s |
| Compiler | Declarative emitter | Structural identity | Instant |
| Sheriff | AST analysis | Import discipline, type hints | Instant |
| Gauntlet | Auto-generated tests | Semantic post-conditions | <8s |

### 4. Agent Roles

- **Architect Agent**: Outputs valid `lattice.yaml` files only
- **Mason Agent**: Implements business logic within scaffolds
- **Orchestrator**: Topological sorting for dependency-aware execution

## Architecture

See [context/Lattice-Lock_Project_Context.md](./context/Lattice-Lock_Project_Context.md) for detailed architecture documentation.

## Versioning

Lattice-Lock uses semantic versioning. See [version.txt](./version.txt) for current version and [instructions/versioning_strategy.md](./instructions/versioning_strategy.md) for our versioning approach.

## Documentation

- [Project Context](./context/Lattice-Lock_Project_Context.md) - Comprehensive overview
- [LLM Specification](./research/Lattice-Lock%20LLM%20Specification.txt) - Technical specification
- [Versioning Strategy](./instructions/versioning_strategy.md) - Version management approach

## Contributing

This is a research project being prepared for publication at ACM TOSEM (Transactions on Software Engineering and Methodology). Contributions and feedback are welcome.

## Research & Publication

**Target Venue**: ACM TOSEM
**Author**: Kevin Lappe
**Date**: November 2025

## Competitive Differentiation

| Framework | Schema-First | Hard Types Pre-Gen | Semantic Contracts | DB Sync |
|-----------|--------------|--------------------|--------------------|---------|
| MetaGPT | No | No | No | No |
| SWE-agent | No | No | No | No |
| OpenDevin | No | No | No | Partial |
| **Lattice-Lock** | **Yes** | **Yes** | **Yes** | **Yes** |

## License

MIT License - See [LICENSE](./LICENSE) file for details

## Contact

For questions about this research or collaboration opportunities, please open an issue in this repository.

---

**Status**: Production-Ready | **Version**: 2.1.0 | **Last Updated**: November 2025
