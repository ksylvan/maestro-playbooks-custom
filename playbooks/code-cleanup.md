# Maestro - Statement of Work

## Project Overview

Maestro is an open-source Electron desktop application for managing multiple AI coding assistants simultaneously. We're seeking an experienced Electron developer to help mature the codebase and establish infrastructure for sustainable, rapid development.

---

## Scope of Work

### 1. Codebase Audit & Dead Code Removal
- Conduct a comprehensive audit to identify unused code, orphaned components, and stale dependencies
- Remove dead code with validation to ensure no functionality is broken
- Document findings and removals for team awareness

### 2. Component Consolidation & Refactoring
- Identify duplicate or near-duplicate components across the codebase
- Merge shared components into reusable, well-documented abstractions
- Establish patterns and guidelines to prevent future duplication

### 3. File Decomposition for AI Context Optimization
- Break apart large monolithic files (1,000+ lines) into smaller, focused modules
- Target file sizes that fit comfortably within AI context windows (~300-500 lines ideal)
- Maintain clear separation of concerns and logical groupings

### 4. Testing Infrastructure Overhaul
- Audit existing test suite and remove low-value or redundant tests
- Implement end-to-end testing framework for critical user flows
- Establish testing patterns that support rapid AI-assisted development
- Improve test coverage on core functionality

### 5. Performance Profiling & Optimization
- Profile the application to identify UI performance bottlenecks
- Ensure the interface remains snappy under load (multiple sessions, large logs)
- Optimize render cycles, reduce unnecessary re-renders

### 6. Memory Optimization
- Inspect memory consumption patterns, especially with multiple spawned processes
- Identify and fix memory leaks
- Ensure the application remains lightweight during extended use

### 7. Development Infrastructure & Maintainability
- Set up tooling and CI checks to maintain code cleanliness
- Establish architectural guidelines for contributors (AI and human)
- Create infrastructure that supports rapid iteration without accumulating tech debt

### 8. Cross-Platform Validation
- Verify consistent behavior across macOS, Windows, and Linux
- Address any platform-specific issues discovered during audit

### 9. IPC Handler Consolidation
- Standardize IPC handler patterns across `src/main/index.ts` and `src/main/ipc/handlers/`
- Reduce duplication and establish consistent error handling, typing, and response formats
- Document IPC API surface for contributor reference

### 10. Multi-Agent Plugin Architecture
- Refactor `src/main/parsers/` and `src/main/storage/` into a clean plugin-like system
- Enable straightforward addition of new agents (OpenAI Codex, Gemini CLI, Qwen3 Coder)
- Define clear interfaces for agent output parsing, session storage, and capability declaration

### 11. Web/Mobile Code Sharing & Full Web Interface
- Extract shared logic between `src/web/` and `src/renderer/` into `src/shared/`
- Reduce parallel component duplication
- Explore path to full web interface access (not just remote control) for browser-based usage

### 12. Type Definition Consolidation
- Audit type definitions across `src/renderer/types/`, `src/shared/types.ts`, and inline definitions
- Consolidate into a single source of truth with clear organization
- Ensure consistent typing across main and renderer processes

### 13. Error Handling Standardization
- Extend the `AgentError` pattern consistently across the codebase
- Standardize error handling in IPC handlers, hooks, and services
- Ensure user-facing errors are clear and actionable

### 14. Deprecated API Migration
- Migrate legacy Claude-only APIs (e.g., `window.maestro.claude`) to generic provider APIs (`window.maestro.agentSessions`)
- Remove deprecated code paths once migration is complete
- Update documentation and comments to reflect current architecture

---

## Ideal Candidate

- Deep experience with Electron architecture (main/renderer process, IPC, preload scripts)
- Strong TypeScript and React proficiency
- Track record of refactoring and maturing production codebases
- Experience with Electron performance profiling and memory debugging
- Familiarity with cross-platform desktop development challenges
- Understanding of how AI-assisted development impacts code organization

---

## Deliverables

1. Audit report documenting dead code, consolidation opportunities, and large files
2. Refactored codebase with merged components and decomposed files
3. Improved test suite with E2E coverage
4. Performance and memory profiling report with optimizations applied
5. Consolidated IPC handlers with standardized patterns
6. Plugin architecture for multi-agent support
7. Shared web/renderer component library with web interface roadmap
8. Unified type definitions
9. Standardized error handling throughout
10. Completed migration from deprecated Claude-only APIs
11. Documentation of established patterns and infrastructure for ongoing maintenance
