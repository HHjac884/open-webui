# DRY (Don't Repeat Yourself) & Modularity Standards

## Core Principle
Every piece of knowledge or logic must have a single, unambiguous, authoritative representation within this system. Avoid "WET" (Write Everything Twice) code at all costs.

## 1. Code Duplication Thresholds
- **Rule of Three**: You may duplicate logic once if necessary for speed, but on the third occurrence, you MUST refactor it into a reusable abstraction (function, hook, or component).
- **Component Reusability**: Before creating a new UI component, search the `src/components` directory for existing elements that can be extended or parameterized.
- **Logic Extraction**: Extract recurring business logic into centralized utility functions located in `@/utils` or `@/lib`.

## 2. Structural Standards
- **Single Responsibility (SRP)**: Each module, class, or function should handle exactly one concern. If a function is doing multiple things, split it into smaller, reusable units.
- **Centralized Constants**: Never hardcode magic strings or numbers. Use a central `constants.ts` or `.env` file to ensure a single source of truth.
- **Avoid Boilerplate**: Use modern language features (e.g., TypeScript records, utility types, or shorthand syntax) to eliminate repetitive boilerplate code.

## 3. Interaction & Refactoring Workflow
- **Pre-Implementation Check**: Before starting a task, search the codebase for similar implementations to identify reusable patterns.
- **Proactive Refactoring**: If you encounter existing duplication while performing a task, propose a refactoring plan to consolidate the logic before adding new code.
- **DRY Rules**: Do not repeat these instructions in other `.clinerules` files. Reference this file if needed.

## 4. Documentation & Naming
- **Intention-Revealing Names**: Use descriptive, standardized naming conventions (e.g., camelCase for functions) so reusable logic is easy to find via search.
- **Why, Not What**: Only document the *reasoning* behind complex logic; avoid comments that simply repeat what the code is doing.
