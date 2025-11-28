# Repository Organization Recommendations

This document provides the plan for completing the reorganization of the Lattice Lock Framework, specifically focusing on the migration from the legacy `vibelocity-orchestrator` directory to the new root-level structure.

**Analysis Date:** 2025-11-28
**Status:** MIGRATION IN PROGRESS
**Priority:** High - Consolidation and Cleanup

---

## Executive Summary

**Current State:**
The repository currently exists in a hybrid state:
1. **New Structure (Root):** `agent_definitions`, `developer_documentation`, `scripts`, etc. This is the **TARGET** structure.
2. **Legacy Structure:** `vibelocity-orchestrator/` containing `Agents-v2`, `Developer Docs`, etc. This is **DEPRECATED**.

**Goal:**
Consolidate all valuable assets into the New Structure and remove the Legacy Structure.

---

## 1. Immediate Actions Required

### 1.1 Audit and Migrate `vibelocity-orchestrator`

**Task:** Systematically review contents of `vibelocity-orchestrator/` and move missing files to the root structure.

**Mapping Strategy:**

| Legacy Path (`vibelocity-orchestrator/`) | Target Path (Root) | Action |
|------------------------------------------|--------------------|--------|
| `Agents-v2/{category}/` | `agent_definitions/{category}/` | **Merge/Overwrite** (Verify versions first) |
| `Agents-v2/*.py` | `scripts/transformation/` | **Move** |
| `Agents-v2/*.md` (Plans/Logs) | `research/` or `agent_definitions/docs/` | **Move** |
| `Developer Docs/` | `developer_documentation/` | **Merge** |
| `scripts/` | `scripts/` | **Merge** |
| `tests/` | `tests/` | **Merge** |
| `tools/` | `tools/` | **Merge** |

**Action Items:**
- [ ] **Scripts:** Move `orchestrate_transformations.py` and other root-level scripts in `Agents-v2` to `scripts/`.
- [ ] **Docs:** Move `AGENT_INDEX.md`, `AGENT_MAPPING.md` to `agent_definitions/` or `developer_documentation/`.
- [ ] **Research:** Move execution plans (`CONCURRENT_EXECUTION_PLAN.md`, etc.) to `research/`.
- [ ] **Agents:** Verify that `agent_definitions` contains the latest versions. If `Agents-v2` has newer files, move them over.

### 1.2 Cleanup Root Directory

**Task:** Ensure the root directory follows the new `REPOSITORY_STRUCTURE_STANDARDS.md`.

**Action Items:**
- [ ] Verify `agent_definitions` folder names use `kebab-case`.
- [ ] Verify `developer_documentation` subfolders use `snake_case`.
- [ ] Ensure `version.txt` is accurate.

---

## 2. Detailed Migration Steps

### 2.1 Handling Scripts

**Current Issue:**
Legacy folder contains scripts mixed with agent definitions (e.g., `orchestrate_transformations.py`).

**Resolution:**
Move all Python and Shell scripts to the `scripts/` directory at the root.
- `scripts/setup/`
- `scripts/validation/`
- `scripts/transformation/`
- `scripts/utilities/`

### 2.2 Handling Documentation

**Current Issue:**
Documentation is split between `developer_documentation` (root) and `Developer Docs` (legacy).

**Resolution:**
1. Move all unique files from `Developer Docs` to `developer_documentation`.
2. Rename files to `snake_case.md` or `Title Case.md` (choose one standard, Standards doc says `snake_case` or `kebab-case` for consistency).
3. Update links in `README.md` and other index files.

### 2.3 Handling Agent Definitions

**Current Issue:**
`agent_definitions` (root) seems to be the active set, but `Agents-v2` (legacy) might have variations.

**Resolution:**
Treat `agent_definitions` as the Source of Truth.
1. Compare `Agents-v2` contents with `agent_definitions`.
2. If `Agents-v2` has unique agents, move them to `agent_definitions`.
3. If `Agents-v2` has identical or older files, ignore them.

---

## 3. Finalizing the Transition

Once all useful files are moved:

1. **Archive Legacy:**
   - Create a backup of `vibelocity-orchestrator` (outside the repo or in a `_archive` folder if needed).
   - **Delete** the `vibelocity-orchestrator` directory from the repository.

2. **Verify Integrity:**
   - Run any validation scripts.
   - Check that all links in `README.md` work.
   - Verify agent definitions are valid.

3. **Commit:**
   - Commit the deletion of the legacy folder as "Complete migration to v2 structure".

---

## 4. Checklist

- [ ] `REPOSITORY_STRUCTURE_STANDARDS.md` updated (Done)
- [ ] Scripts moved to `scripts/`
- [ ] Documentation consolidated in `developer_documentation/`
- [ ] Agent definitions consolidated in `agent_definitions/`
- [ ] `vibelocity-orchestrator` folder deleted
- [ ] `.gitignore` updated to exclude legacy paths if necessary

---

## Questions?

Refer to `REPOSITORY_STRUCTURE_STANDARDS.md` for the definitive rules on the new structure.
