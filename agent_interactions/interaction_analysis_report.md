# Agent Interaction Analysis Report

## Executive Summary

The `agent_interactions` directory contains two key JSON files: `agent_performance.json` and `dependency_map.json`. While these files provide a structured way to track performance and dependencies, they are currently out of sync with the new v3 repository standards.

## Key Findings

### 1. Naming Convention Mismatch

- **Issue**: Both files use **Title-Case-Kebab-Style** agent names (e.g., `Project-Coordination-Agent`, `Architecture-Discovery-Agent`).
- **Standard**: The new standard is **snake_case** (e.g., `project_agent`, `engineering_agent`).
- **Impact**: Automated tools expecting snake_case names will fail to match these records with the actual agent definitions.

### 2. Outdated File References

- **Issue**: `dependency_map.json` references legacy file paths like `Model Orchestrator/model-orchestrator.py`.
- **Standard**: The new structure places source code in `src/`.
- **Impact**: Dependency tracking is inaccurate and points to non-existent or moved files.

### 3. Legacy Agent References

- **Issue**: The files reference agents that may have been renamed or restructured (e.g., `Model-Orchestrator-Tool-Agent` vs `model_orchestration_agent`).
- **Impact**: Inconsistency between the interaction map and the actual agent catalog.

## Proposed Iterations

### Immediate Fixes

1. **Update Agent Names**: Convert all agent keys in both JSON files to `snake_case` to match the `agent_definitions` directory.
2. **Update File Paths**: Update `dependency_map.json` to reflect the new `src/` directory structure.
3. **Consolidate**: Ensure `agent_definitions/agent_interactions.json` (found earlier) is merged or moved into this directory if it contains unique data, or deleted if redundant.

### Strategic Improvements

1. **Automated Generation**: These files appear to be static snapshots. Implement a script to auto-generate `dependency_map.json` based on the actual codebase and agent definitions.
2. **Schema Validation**: Define a JSON schema for these files to ensure future consistency.
