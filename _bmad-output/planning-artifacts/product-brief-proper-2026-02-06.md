---
stepsCompleted: [1, 2, 3, 4, 5, 6]
inputDocuments: ['/Users/derek/code/2026/hackathon/proper/_bmad-output/planning-artifacts/brainstorming.md']
date: 2026-02-06
author: Derek
---

# Product Brief: proper

<!-- Content will be appended sequentially through collaborative workflow steps -->

## Executive Summary

**Propper** is an AI-powered bridge between design and engineering that ensures Figma components are "code-ready" before they ever leave the design tool. By leveraging bi-directional MCP connectivity, Propper audits Figma components against code-level requirements—checking for missing states, accessibility attributes, token misalignment, and composability gaps. It replaces manual checklists and "hidden layer" hacks with an intelligent, automated guardian that empowers designers to deliver engineering-grade assets, eliminating the 60-70% of component definition that is typically lost in handoff.

---

## Core Vision

### Problem Statement
Designers and developers view components through fundamentally different lenses. While a designer sees visual variants and layout, a developer sees a complex API of props, accessibility roles, keyboard interactions, and token mappings. Current tools like Figma Properties only capture ~30-40% of this necessary context.

The industry creates manual workarounds—like Nathan Curtis's "Code Only Props" hidden layers—to try and bridge this gap, but these are manual, inconsistent, and invisible to the toolchain. This "Translation Gap" leads to broken handoffs, accessibility failures, and significant rework as developers are forced to guess intent or request changes late in the cycle.

### Problem Impact
*   **Engineering Waste:** Developers spend cycles interpreting designs or re-building components that lack state or structure.
*   **Accessibility Failures:** Critical attributes like `aria-labels` and focus management are often invisible in Figma, leading to non-compliant products.
*   **Design Drift:** Without a single source of truth for "completedness," implementation drifts from design intent.
*   **Friction:** The "ping-pong" between design and dev during handoff destroys velocity and morale.

### Proposed Solution
**Propper** provides a dual-interface solution to meet both users where they are:
1.  **For Engineers (The Skill):** A powerful CLI/Agent skill backed by a local MCP server that can deeply audit components against local code standards and "Golden Master" definitions.
2.  **For Designers (The Plugin):** A lightweight, "keyless" Figma Plugin that sends component data to a hosted proxy (which calls the LLM), allowing for zero-friction auditing directly within the design canvas.

Both interfaces validate against the same logical rules: verifying that "invisible" props (like `disabled` states or `aria-labels`) are explicitly defined.

**Knowledge-Driven Validation:**  
Instead of hardcoding rules or relying on complex logic, Propper leverages the `southleft-ds-mcp` Knowledge Base to "learn" the requirements for standard components (like Buttons or Cards) and enforces those industry best practices dynamically.

**Killer Feature: "Auto-Scaffold"**
To address the usability friction of defining hidden props, the Plugin includes an "Auto-Scaffold" button. If a required prop is missing (e.g., `accessibilityLabel`), Propper creates the standardized "Code Only Props" hidden layer structure automatically, requiring the designer only to fill in the value (or press Approve on a suggested value), rather than managing layer hierarchy manually.

### Key Differentiators
*   **Bi-Directional Context:** Unlike static linters, Propper reads from Figma and compares against *Code Best Practices* (DS-Knowledge), validating the *intent*, not just the structure.
*   **The "Code-Only" Guardian:** We specifically target the "invisible" 60%—the props that don't have visual variants but are required for code (as highlighted by the "Code Only Props" methodology).
*   **Education-First:** The audit doesn't just say "Fail"; it teaches the designer *why* a prop is needed (e.g., "This button needs a `loading` state because it triggers an API call"), effectively upskilling the design team.

## Target Users

### Primary Users: "Morgan" - The Design Systems Lead
**Role:** Senior Product Designer / Design Systems Architect.
**Motivation:** Wants to maintain a pristine, scalable component library. Hates "design drift" and messy handoffs.
**The Pain:** Spends 50% of their time policing other designers' work. "I have to send this ticket back because you forgot the focus state again."
**Success Vision:** A "Gatekeeper" tool that does the policing for them. They want to set the rules ("All inputs must have labels") and let Propper enforce them. They trust Propper to be the "Bad Cop" so they can be the mentor.

### Secondary Users: "Alex" - The Product Designer
**Role:** Mid-level Product Designer.
**Motivation:** Wants to ship features and be seen as competent by engineering.
**The Pain:** Anxiety during handoff. "I don't know code, so I don't know what I'm missing."
**Success Vision:** A "Safety Net." They run Propper before handoff to catch mistakes privately. The "Auto-Scaffold" feature reduces their workload by doing the technical setup for them.

### Tertiary Users: "Sam" - The Developer
**Role:** Frontend Engineer.
**Motivation:** Wants complete specs so they can build without asking questions.
**The Pain:** Guessing intent. "Is this meant to be a button or a link? What happens when I tab to it?"
**Success Vision:** Receiving a Figma file that maps 1:1 to the component API.

### User Journey (The "Ideal Loop")
1.  **Discovery:** Morgan (Lead) installs the Propper Plugin for the team to standardize valid components.
2.  **Creation:** Alex (Designer) builds a new "Card" component.
3.  **Audit:** Alex select the Card and clicks "Audit with Propper."
4.  **Feedback/Auto-Fix:** Propper highlights missing `aria-label` and offers to "Create Prop Layer". Alex clicks "Auto-Scaffold" to fix the hierarchy instantly.
5.  **Completion:** Alex fills in the missing data.
6.  **Handoff:** Alex hands off to Sam (Dev).
7.  **Validation:** Sam runs the `propper-skill` in their terminal to verify the component matches the codebase expectations before starting the build.

### Risks and Mitigations (War Room Outcomes)
*   **Risk:** "Consistency Drift" between Plugin (LLM) and Skill (Local Logic).
*   **Mitigation:** For MVP, we maintain a unified `rules.md` file that is fed to both the System Prompt (Plugin) and Local Logic (Skill) to ensure the same 5 critical checks (e.g., `disabled`, `loading`, `aria-label`) are enforced identically.

## Success Metrics

### User Success Metrics
*   **Audit Rate:** Design Teams run an audit on >80% of new components before handoff.
*   **Fix Rate:** Users click "Auto-Scaffold" on >50% of specific error types (proving utility).
*   **Confidence Score:** Users report feeling "More Confident" in their handoff quality (measured via qualitative feedback).
*   **Handoff Velocity:** Developers report fewer "return to sender" tickets for basic property gaps.

### Business Objectives (Hackathon MVP)
*   **Demo Impact:** deliver a "Wow" moment by showing a side-by-side fix (Figma audit -> Auto-fix -> Code audit passes).
*   **Adoption Friction:** Achieve < 2 minutes "Time to First Audit" (via keyless plugin).
*   **Reliability:** Zero "False Failures" on the 5 core rules (Button, Input, Checkbox, Card, Select).

### Key Performance Indicators (KPIs)
*   **Latency:** Audit returns results in < 5 seconds.
*   **Accuracy:** Audit correctly identifies 100% of missing props in the "Golden Master" test set.
*   **Usage:** >10 audits run per active user session.

## MVP Scope

### Core Features (The "Thin Slice")
1.  **Knowledge-Driven Rules Engine:**
    *   **Pre-Generated Rules:** We use `southleft-ds-mcp` to generate a static `rules.json` file containing best-practice props for key components (Button, Input, Card). This file acts as the "Brain" for both tools.
    *   **Naming Standards:** Enforces standard prop names found in the Knowledge Base.
2.  **The Figma Plugin (Designer):**
    *   **Hosted Proxy (Vercel):** A simple Next.js app that serves the plugin backend and holds the `rules.json`.
    *   **Audit Interface:** Select layer -> Plugin checks struct against `rules.json`.
    *   **Auto-Scaffold:** One-click generation of the "Code Only Props" hidden frame structure.
    *   **Keyless Entry:** Users just install and run; the Vercel proxy manages the API connection (for demo).
3.  **The Code Skill (Developer):**
    *   **CLI Command:** `propper audit <component_path>`.
    *   **Logic:** Audits local code/JSON against the SAME `rules.json` file from the repo.

### Out of Scope for MVP
*   **Live MCP Connection:** The plugin won't talk to a live local MCP server. It uses the "snapshot" `rules.json`.
*   **User Accounts:** No login, no history, no team management.
*   **Custom Rules:** Rules are hardcoded via the JSON file; users cannot add their own via UI.
*   **Full Component Library:** Focus on the "Big 3" components only.

### MVP Success Criteria
*   **The "Loop" Works:** Can a designer fix a component in Figma using Auto-Scaffold, and have the Developer's CLI immediately report "PASS"?
*   **Demo Reliability:** The Vercel deployment stays stable during the presentation.
*   **Speed:** Audit returns in < 5 seconds.

### Future Vision
*   **Propper Cloud:** Hosted version with team accounts and history.
*   **CI/CD Integration:** Run `propper audit` in GitHub Actions to block PRs if Figma drift is detected.
*   **AST Parsing:** Deep code analysis for any framework.
*   **Custom Rule Builder:** "Teach" Propper new rules by showing it examples.
