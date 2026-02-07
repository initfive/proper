---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
inputDocuments: ['_bmad-output/planning-artifacts/prd.md', '_bmad-output/planning-artifacts/product-brief-proper-2026-02-06.md']
classification:
  projectType: 'Developer Tool'
  domain: 'Design Infrastructure'
  complexity: 'Medium'
workflowType: 'architecture'
lastStep: 8
status: 'complete'
completedAt: '2026-02-07'
---

# Architecture Decisions - proper

**Date:** 2026-02-07T01:30:00-05:00
**Status:** Approved

## 1. System Overview

### High Level Architecture
*To be defined in later steps*

## 2. Project Context Analysis

### Requirements Overview

**Functional Requirements:**
The system requires a tight integration between three distinct environments:
1.  **Figma Plugin:** Needs to handle extraction of complex layer data and re-injection of new "Code Only Props" layers.
2.  **Rules Engine:** A strict JSON-schema validator that must operate identically in both the browser (Plugin) and Node.js (CLI/Proxy).
3.  **Vercel Proxy:** A stateless API that sanitizes data and orchestrates the LLM interaction for "fuzzy" logic (parsing intent vs. strict schema).
4.  **CLI:** A developer-facing tool that authenticates with Figma to run the same validations in a CI/CD context.

**Non-Functional Requirements:**
- **Performance:** Sub-second response times for "Auto-Scaffold" imply we must use **Optimistic UI** patterns and highly optimized payloads.
- **Security:** "Keyless" and "Ephemeral" requirements mean we should avoid a heavy database. However, **Anonymous Telemetry** is needed for operational observability.
- **Reliability:** 50 concurrent requests target suggests a robust serverless configuration (e.g., Vercel Edge Functions).

**Scale & Complexity:**
- **Primary Domain:** Full-Stack / DevTools
- **Complexity Level:** **Medium-High** (Due to synchronization and telemetry needs)
- **Estimated Components:** 4 (Plugin Client, Proxy Service, Rules Lib, CLI Tool)

### Technical Constraints & Dependencies
- **Platform:** Figma Plugin API & REST API limitations.
- **Infrastructure:** Vercel (Serverless/Edge).
- **Stack:** React, TypeScript, Tailwind/ShadCN.
- **Timeframe:** Hackathon "Steel Thread" focus (MVP).

### Cross-Cutting Concerns Identified
- **Schema Versioning & Synchronization:** A critical concern. The CLI and Plugin must share a synchronized definition of `rules.json` to prevent drift.
- **Shared Validation Logic:** The core validation logic must be portable (Isomorphic TypeScript).
- **Type Safety:** Shared interfaces for Component Data and Audit Results across all 3 apps.
- **Error Handling:** Unified error codes and messages for both Designers (GUI) and Developers (CLI).
- **Observability:** Centralized telemetry to track usage and error rates without compromising user data privacy.

## 3. Starter Template Evaluation

### Selected Starter: Custom Turborepo

**Rationale:**
Selected to provide a **Shared Rules Engine** (`packages/core`) that ensures `rules.json` and validation logic are identical across the Figma Plugin (Client) and Vercel Proxy (Server).

**Architectural Foundation:**
- **Monorepo Structure:** Turborepo with `pnpm` workspaces.
- **Apps:** `apps/plugin` (Plugma - Vite framework for Figma), `apps/proxy` (Next.js).
- **Shared:** `packages/core` (Zod schemas), `packages/config` (TS/Eslint).

## 4. Core Architectural Decisions

### Decision Priority Analysis
**Critical Decisions Made:**
- **Monorepo:** Turborepo (for shared logic provided by Starter).
- **API Protocol:** REST (User Choice).
- **Runtime:** Node.js (Safety).

### Data Architecture
- **State Management:** Stateless / Git-based.
- **Storage:** `rules.json` located in `packages/core`.
- **Persistence:** None (Ephemeral).
- **Rationale:** Aligns with "Keyless" and "MVP" constraints.

### Authentication & Security
- **Pattern:** Shared Secret Header (`x-propper-key`).
- **Secrets:** Managed via Vercel Environment Variables.
- **Rationale:** Simple and effective for Hackathon timeframe.

### API & Communication
- **Protocol:** REST (Standard HTTP).
- **Schema:** Shared Zod Schema from `packages/core`.
- **Rationale:** Standard patterns and decoupling.
- **Implication:** Manual schema usage in route handlers required.

### Infrastructure
- **Target:** Vercel Serverless (Node.js 18+).
- **Region:** Auto.
- **Rationale:** Max compatibility for file system operations.

### Decision Impact Analysis
- **Implementation Sequence:**
    1.  Initialize Turborepo.
    2.  Set up `packages/core` with Zod.
    3.  Build REST Endpoints in Proxy (Node.js).
    4.  Build Plugin UI using REST client.

## 5. Data Model

### Core Entities
*To be defined*

### Interface Contracts
*To be defined*

## 7. Implementation Patterns & Consistency Rules

### System-Wide Enforcement
**ALL Agents MUST:**
1.  **Place Logic in Core:** Any validation rule used in more than one place MUST move to `packages/core`.
2.  **Use Standard Envelope:** All API responses must follow the `{ success, data, error }` schema.
3.  **Co-locate Tests:** Unit tests live next to the file they test (`*.test.ts`).

### Naming Patterns
*   **Files & Directories:** `kebab-case` (e.g., `user-profile.tsx`, `packages/ui-kit`).
*   **React Components:** `PascalCase` (e.g., `UserProfile`).
*   **Functions/Variables:** `camelCase` (e.g., `auditLayer`, `isValid`).
*   **API Routes:** `kebab-case` nouns (e.g., `/api/v1/validation-rules`).
*   **Zod Schemas:** `PascalCase` (e.g., `UserSchema`, `LayerNode`).

### Structural Patterns
*   **Testing Strategy:** Co-located `*.test.ts` files.
*   **Shared Logic:**
    *   `packages/core`: Business logic, Validation Rules, Type Definitions.
    *   `packages/ui`: Shared ShadCN components (future).
*   **Feature Organization:** Domain-based (e.g., `features/auth`, `features/audit`) rather than type-based.

### API & Communication Rules
*   **Response Envelope:**
    ```json
    {
      "success": boolean,
      "data": T | null,
      "error": { "code": string, "message": string } | null
    }
    ```
*   **Date Format:** STRICT ISO 8601 strings (UTC). No timestamps.
*   **Error Handling:**
    *   **Success (`200 OK`):** `success: true`
    *   **Logical Failure (`200 OK`):** `success: false` (e.g., validation failed).
    *   **System Failure (`4xx/5xx`):** Protocol/Server crashes.

### Process Rules
*   **Validation:** "Parse, don't validate." Use Zod `safeParse` at the API boundary.
*   **Optimistic UI:** Plugin UI **MUST** update state immediately for user actions, reverting on API failure to ensure sub-second feel.

## 8. Project Structure & Boundaries

### Complete Project Directory Structure

```text
proper/ (Root)
├── .github/                # CI/CD Workflows
├── .vscode/                # Shared Editor Settings
├── apps/
│   ├── plugin/             # Figma Plugin (Client)
│   │   ├── src/
│   │   │   ├── main/       # Plugin Sandbox Logic (Figma API)
│   │   │   ├── ui/         # React UI (Iframe)
│   │   │   └── shared/     # Shared Types/Utils within Plugin
│   │   ├── plugma.config.ts
│   │   ├── manifest.json
│   │   └── package.json
│   └── proxy/              # Vercel AI Proxy (Server)
│       ├── app/
│       │   ├── api/        # REST Endpoints (Node.js/Edge)
│       │   └── page.tsx    # Landing Page (Optional)
│       ├── lib/            # Server-Side Utilities
│       └── package.json
├── packages/
│   ├── core/               # The "Shared Truth"
│   │   ├── src/
│   │   │   ├── rules/      # Zod Schemas for Validation
│   │   │   └── types/      # Shared TypeScript Interfaces
│   │   └── package.json
│   ├── config/             # Shared Configs (ESLint, TSConfig)
│   └── ui/                 # Shared UI Components (ShadCN - Optional)
├── turbo.json              # Turborepo Pipeline Config
├── package.json            # Root Package.json
└── pnpm-workspace.yaml     # Workspace Definition
```

### Architectural Boundaries

**API Boundaries:**
*   **Plugin <-> Proxy:** Strictly typed REST API over HTTPS. Authenticated via `x-propper-key`.
*   **Proxy <-> LLM:** Vercel AI SDK (Server-Side Only). Keys never leave the Proxy.

**Component Boundaries:**
*   **Plugin UI:** Handles user interaction and optimistic updates.
*   **Plugin Main:** Handles direct Figma document manipulation (reading layers, writing nodes).
*   **Core:** Pure logic only. No side effects. No DOM access. No Node.js APIs.

### Requirements to Structure Mapping

**Functional Requirements:**
*   **Rule Engine:** Mapped to `packages/core/src/rules`.
*   **Figma Extraction:** Mapped to `apps/plugin/src/main/extractors`.
*   **AI Scaffolding:** Mapped to `apps/proxy/app/api/scaffold`.
*   **Audit Logic:** Mapped to `packages/core/src/audit`.

**Cross-Cutting Concerns:**
*   **Telemetry:** implemented in `apps/proxy/lib/telemetry` (Server-side) and `apps/plugin/src/shared/analytics` (Client-side).
*   **Type Safety:** Enforced by `packages/config/tsconfig.base.json`.

## 9. Architecture Validation Results

### Architecture Readiness Assessment
**Overall Status:** READY FOR IMPLEMENTATION
**Confidence Level:** High

### Architecture Completeness Checklist
- [x] Critical decisions documented (Turborepo, Plugma, REST, Node.js)
- [x] "Shared Truth" mechanism defined (`packages/core`)
- [x] Security boundaries established (Air Gap)
- [x] Implementation patterns defined
- [x] Project structure defined
- [x] Requirements mapped to components

## 10. Completion & Handoff

### Workflow Completion
This architecture document represents the agreed-upon technical foundation for **Propper**. It has been validated against the PRD and Product Brief.

### Next Steps for Implementation
1.  **Initialize Monorepo:** Run `npx create-turbo@latest proper`.
2.  **Install Plugma:** Configure `apps/plugin` with Plugma.
3.  **Setup Shared Core:** Create `packages/core` and install Zod.
4.  **Configure Proxy:** Initialize `apps/proxy` with Next.js and Vercel AI SDK.

### Architecture Handoff
**AI Agents:** Refer to this document for all structural and pattern decisions. Do not deviate from the "Air Gap" or "Shared Truth" patterns without manual approval.


