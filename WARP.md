# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

**Lattice-Lock** is a governance-first, deterministic code generation framework designed to eliminate interface drift and style divergence in multi-agent LLM systems. The project transforms code generation from probabilistic synthesis into constrained assembly by pre-compiling schema definitions into enforcement artifacts.

**Current Version**: 2.1.0 (Production-ready, targeting ACM TOSEM publication)

## Core Architecture

The framework operates in two phases:

1. **Pre-Computation Phase**: `lattice.yaml` → Polyglot Compiler → Governance Cage (types, ORM models, migrations, test contracts)
2. **Implementation Phase**: Mason Agents → Implementation Code → Sheriff (AST) + The Gauntlet (pytest) → PASS/REJECT

**Key Innovation**: Agents work within pre-defined, rigorously typed scaffolds rather than generating code from scratch.

## Essential Commands

### Model Orchestrator (63 AI Models)

The orchestrator intelligently routes tasks across 63 AI models from 8 providers (Local/Ollama, OpenAI, Anthropic, Google, xAI Grok, Azure, Bedrock, DIAL).

```bash
# List all available models
./scripts/orchestrator_cli.py list --verbose

# Analyze prompt for optimal model selection
./scripts/orchestrator_cli.py analyze "Your task description"

# Route request with strategy (balanced, cost_optimize, quality_first, speed_priority)
./scripts/orchestrator_cli.py route "Your prompt" --strategy balanced

# Create consensus group (multiple models vote)
./scripts/orchestrator_cli.py consensus "Question" --num 5

# View cost report
./scripts/orchestrator_cli.py cost

# Test orchestrator integration
./scripts/orchestrator_cli.py test
```

### Local Model Management (Ollama)

```bash
# Start Ollama service
./scripts/start_ollama.sh

# Warm up models (pre-load for faster response)
./scripts/warmup_models.sh

# Keep models loaded with optimized keep-alive
python3 scripts/optimized_keep_alive.py

# List all local models
python3 scripts/list_all_models.py

# Monitor RAM usage during execution
python3 scripts/ram_monitor.py

# Test concurrent model capacity
./scripts/test_concurrent_capacity.sh
```

### Testing

```bash
# Run orchestrator tests
python3 scripts/test_orchestrator.py

# Run integration tests
./scripts/run_integration_tests.sh

# Test real-world scenarios
python3 scripts/test_real_world.py

# Test local models
python3 scripts/test_local_models.py

# Test concurrent model execution
python3 scripts/test_concurrent_models.py
```

### Agent Validation

```bash
# Validate agent definitions against framework standards
python3 scripts/validate_agents.py
```

### API Setup

```bash
# Configure API keys for cloud providers
source scripts/setup_api_keys.sh
```

## Repository Structure Standards

**MANDATORY**: All files follow snake_case naming conventions:

```
Lattice Lock Framework/
├── agent_definitions/        # Agent YAML/Markdown definitions by category
│   ├── business_review_agent/
│   ├── engineering_agent/
│   ├── templates_agent/     # Base templates for new agents
│   └── ...
├── agent_memory/            # Universal memory directive & agent state
├── agent_workflows/         # Parallel/Sequential/Hybrid workflow templates
├── agent_interactions/      # Interaction protocols and logs
├── context/                 # Project context & governance documentation
├── developer_documentation/ # Architecture, features, development guides
├── instructions/            # AI/LLM specific instructions & versioning
├── research/                # Academic research & specifications
├── scripts/                 # Executable Python/Shell scripts
│   ├── orchestrator_cli.py
│   ├── model_orchestrator.py
│   ├── zen_mcp_bridge.py
│   └── ...
└── src/                     # Source code modules
```

**Key Rules**:
- Use snake_case for ALL directories and files
- Agent definitions: `{category}_agent/{name}_definition.yaml`
- No spaces or special characters in filenames
- All Python scripts must have `#!/usr/bin/env python3` shebang
- All shell scripts must have `#!/bin/bash` shebang

## Agent System Architecture

### Memory Management

All agents follow the Universal Memory Directive (`agent_memory/universal_memory_directive.md`):

- **Memory Files**: `agent_{name}_memory.md` stored in `agent_memory/agents/`
- **Required Sections**: Agent Purpose, Sub-agents, Agent Summary, Next Steps, Interactions (Agent-to-Agent & Agent-to-Sub-Agent), Token Usage
- **Update Protocol**: Read memory before tasks, update after completion, commit with format `[memory] agent_{name}: {task_completed}`

### Workflow Templates

Three execution patterns in `agent_workflows/`:

1. **Parallel Execution** (`parallel_execution_workflow.md`): 64GB+ RAM, 30-70% time savings, independent work streams
2. **Sequential Execution** (`sequential_execution_workflow.md`): 16GB+ RAM, simplified complexity, dependent tasks
3. **Hybrid Workflow** (`hybrid_workflow.md`): 32GB+ RAM, 30-50% time savings, mixed dependencies

### Agent Templates

Base templates in `agent_definitions/templates_agent/`:

- **Base Agent Template**: Foundation for all agents
- **Analysis Agent Template**: Discovery, mapping, read-only operations
- **Implementation Agent Template**: Code creation/modification with ≥80% test coverage
- **Documentation Agent Template**: Technical writing, user guides, API docs
- **Testing Agent Template**: Unit, integration, E2E test generation

**Template Selection**: See `agent_creation_instructions.md` for decision tree.

## Model Selection Strategy

The orchestrator scores models based on:
- **Task Affinity** (40%): Suitability for task type
- **Performance** (30%): Speed vs reasoning depth
- **Accuracy** (20%): Model accuracy rating
- **Cost Efficiency** (10%): Price/performance ratio

**Task Types Detected**: CODE_GENERATION, CODE_REVIEW, REASONING, VISION, DEBUGGING, SYSTEM_DESIGN, CREATIVE_WRITING, ANALYSIS, TRANSLATION, SUMMARIZATION

**Local Models** (FREE, recommended for most tasks):
- Code: `codellama:34b` (best quality), `magicoder:7b` (optimal balance)
- Reasoning: `deepseek-r1:70b` (ultimate), `qwen2.5:32b` (premium)
- General: `llama3.1:8b`, `qwen3:8b`, `gemma:7b`

**Cloud Models** (use for specialized needs):
- Reasoning: `o1-pro`, `claude-opus-4.1`, `grok-4-fast-reasoning`
- Vision: `gpt-4o`, `grok-2-vision-1212`
- Code: `grok-code-fast-1`, `claude-sonnet-4.5`
- Fast/Cheap: `gpt-4o-mini`, `claude-3-haiku`, `gemini-2.0-flash`

## Development Workflow

### Creating New Agents

1. Select appropriate template from `agent_definitions/templates_agent/`
2. Customize all sections (scope, models, workflows, metrics)
3. Define clear scope boundaries (permitted directories, file types, operations)
4. Specify handoff requirements to other agents
5. Create memory file following Universal Memory Directive
6. Validate with `python3 scripts/validate_agents.py`

### Working with Lattice Schemas

1. Define entities and interfaces in `lattice.yaml`
2. Run polyglot compiler: `python3 compile_lattice.py`
3. Generates: Pydantic models, SQLModel ORM, Alembic migrations, pytest contracts
4. Implement business logic within generated scaffolds
5. Validate with Sheriff (AST) and The Gauntlet (pytest)

**Schema Constraints**:
- Forbidden imports enforced at compilation
- Semantic contracts via `ensures:` clauses
- Field-level invariants (`gt: 0`, `unique: true`, etc.)
- Database persistence configuration

## Version Management

Framework uses **Semantic Versioning 2.0.0**:
- **MAJOR**: Breaking schema/API changes (requires migration)
- **MINOR**: New features, backward-compatible additions
- **PATCH**: Bug fixes, documentation updates

Current framework version in `version.txt`. Schema versions in individual `lattice.yaml` files allow coexistence and gradual migrations.

## Environment Variables

Required for cloud model access:

```bash
export XAI_API_KEY="your-grok-key"
export OPENAI_API_KEY="your-openai-key"
export GOOGLE_API_KEY="your-google-key"
export ANTHROPIC_API_KEY="your-anthropic-key"
export DIAL_API_KEY="your-dial-key"  # Optional
```

Local models use Ollama (default endpoint: `http://localhost:11434/v1`).

## Key Architectural Patterns

### Governance Layers (Four Enforcement Mechanisms)

1. **Meta-Schema Validator**: JSONSchema validation + LLM repair loop (~3s latency)
2. **Polyglot Compiler**: Declarative → multi-target emitter (instant)
3. **Sheriff (AST)**: Import discipline + type hint enforcement (instant)
4. **The Gauntlet**: Auto-generated pytest semantic contracts (~8s latency)

### Multi-Model Interaction Patterns

- **Chain of Thought**: Sequential processing through multiple models
- **Parallel Consensus**: Multiple models vote on answer (configurable diversity)
- **Hierarchical Refinement**: Start cheap, refine with premium models
- **Adaptive Analysis**: Depth adjusts to task complexity

### Resource Management

- 16GB RAM: Sequential workflows, cloud models for heavy workloads
- 32GB RAM: Hybrid workflows, 2-3 concurrent agents, local + cloud mix
- 64GB+ RAM: Full parallel execution, primarily local 70B models

## Production Results

Deployed in regulated domains (fintech, healthcare, SaaS):
- **100% structural compliance**: All interface drift caught at compile time
- **>99% semantic compliance**: 300× improvement over unconstrained systems
- **Schema ROI**: 74×-96× code amplification from schema definition
- **Zero structural defects** across three production systems

## Python Environment

- Python 3.14.0
- pytest available for testing
- Dependencies: aiohttp, requests, tenacity, rich, pydantic, sqlmodel

## Important Notes

- **Never modify generated code directly** - always update `lattice.yaml` and recompile
- **Agent memory files must be updated before every commit**
- **Test coverage ≥80% required** for implementation agents
- **All scope boundaries must be explicit** to prevent agent conflicts
- **Model selection should balance cost, speed, and quality** based on task requirements
- **Parallel execution requires dependency analysis** - use topological sorting for orchestration

## Related Documentation

- Project Context: `context/lattice_lock_project_context.md`
- Orchestrator Guide: `orchestrator_readme.md`
- Memory Directive: `agent_memory/universal_memory_directive.md`
- Workflow Templates: `agent_workflows/workflow_templates_documentation.md`
- Repository Standards: `directory/repository_structure_standards.md`
- Versioning Strategy: `instructions/versioning_strategy.md`
- Research Specification: `research/lattice_lock_llm_specification.txt`
