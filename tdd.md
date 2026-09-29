---
name: test driven development
description: Enforce strict Red-Green-Refactor lifecycles for TypeScript applications focusing exclusively on pre-agreed 'seams' for Unit and Integration vertical slicing
keywords: TypeScript TDD, Bun, Biome, vertical slice, test seams, Zod validation.
---


# test-driven development

## Core Trigger Criteria

* **Trigger Conditions:** Invoke when planning or implementing TS features via TDD, establishing testing boundaries, or creating automated quality pipelines with Bun.
* **Exclusions:** Do NOT use this skill for End-to-End (E2E) testing, legacy JavaScript, or basic UI styling without logic.

## Execution Workflow

1. **Context Discovery:** Check the workspace for `biome.json` and `.husky/` to verify the environment state.
2. **Phase 1 [Define Seam]:** Analyze the codebase to map the Entry Seam (e.g., HTTP controller, public module) and Exit Seam (e.g., database client, external API). **STOP AND ASK THE USER TO AGREE ON THE SEAM** before writing any code.
3. **Phase 2 [Write Failing Seam Test]:** Once agreed, write a test exclusively against the public Entry Seam using the Bun test runner. Mock dependencies at the Exit Seam. Run the suite to verify it fails for the expected reason.
4. **Phase 3 [Implement Vertical Slice]:** Write the absolute minimum TypeScript and Zod validation code necessary to satisfy the seam test interface. Treat the inside of the slice as a black box. Do not over-engineer.
5. **Phase 4 [Pass Test]:** Run the test suite using `bun test` to ensure the vertical slice correctly fulfills the seam's contract.
6. **Phase 5 [Refactor Internals]:** Clean up variables, extract helpers, or optimize algorithms within the vertical slice. Run `bunx @biomejs/biome check --write .` to ensure formatting and linting compliance in fractions of a second. The test must remain green.

## Strict Architectural Rules

* **Test Configuration & Scoping Matrix:**
* *Integration (Primary):* Entry Seam = API Router/Controller; Exit Seam = Third-Party APIs. Target = Layer cohesion and data flow.
* *Unit (Secondary):* Entry Seam = Public Module/Service; Exit Seam = Database clients/File Systems. Target = Pure business logic and edge cases.
* **No Test Leaks:** Tests must never touch private methods or internal helpers. If you rewrite internal logic during refactoring, the public seam test must remain completely untouched.
* **No Ghost Code:** Never add branches, parameters, or edge-case handlers to the production code unless explicitly driven by a failing test at the seam.
* **Performance & Tooling:** Execute all scripts via Bun. Delegate all formatting and core linting to Biome for maximum speed and a single source of truth. Use Zod for strict data validation at the architectural boundaries.

## Expected Output Format

Ensure the final response strictly follows this structural layout:

* **Direct Solution:** Lead immediately with the proposed seam definitions or the resulting code blocks (depending on the workflow phase).
* **Key Changes:** A bulleted list highlighting architectural boundaries, test targets, or internal refactoring decisions.
* **Verification Steps:** Short, punchy commands to validate the slice (e.g., `bun test <file>`, `bunx @biomejs/biome check`).
