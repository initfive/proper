# <p align="center">✨ Propper</p>

<p align="center">
  <strong>The AI-powered bridge between design and engineering.</strong>
</p>

<p align="center">
  <a href="#-🛑=the-problem">The Problem</a> •
  <a href="#-✅-the-solution">The Solution</a> •
  <a href="#-key-features">Key Features</a> •
  <a href="#-roadmap">Roadmap</a>
</p>

---

## 🛑 The Problem: The "Translation Gap"

Designers and developers view components through different lenses. A designer sees visual variants; a developer sees a complex API of props, ARIA roles, and state management. 

Figma properties typically capture only ~40% of the context needed for production-ready code. The remaining 60%—things like `disabled` states, `loading` indicators, and `aria-labels`—is often lost in handoff, leading to:
*   **Engineering Waste:** Developers spend cycles interpreting design intent.
*   **Accessibility Failures:** Invisible attributes are forgotten.
*   **Design Drift:** Implementations diverge from "Golden Rules".

## ✅ The Solution: Propper

**Propper** ensures Figma components are "code-ready" before they ever leave the design tool. It acts as an intelligent guardian, auditing components against real-world engineering standards using a bi-directional knowledge base.

### 1. For Designers (The Plugin)
A lightweight Figma plugin that allows for zero-friction auditing. It identifies missing "invisible" props and provides one-click fixes.

### 2. For Engineers (The Skill)
A CLI automation tool that audits local code against the exact same ruleset as Figma, ensuring 1:1 parity and blocking "drift" before it reaches production.

---

## 🔥 Key Features

### 🧠 Knowledge-Driven Validation
Instead of hardcoded rules, Propper leverages the `southleft-ds-mcp` knowledge base to "learn" the requirements for standard components (Buttons, Inputs, Cards) and enforces industry best practices dynamically.

### 🛠️ Auto-Scaffold
The "Killer Feature." If a required prop is missing (e.g., `accessibilityLabel`), Propper can automatically create the standardized "Code Only Props" hidden layer structure in Figma, requiring the designer only to fill in the value.

### 🎓 Education-First Feedback
Propper doesn't just say "Fail." it teaches. It explains *why* a prop is needed (e.g., "This button needs a loading state because it triggers an async action"), upskilling the design team with every audit.

---

## 🗺️ MVP Roadmap (Hackathon)

- [x] **Product Discovery:** Finalize vision and MVP scope.
- [ ] **Knowledge Logic:** Generate `rules.json` from DS Knowledge Base.
- [ ] **Figma Plugin:** Implement Audit + Auto-Scaffold logic.
- [ ] **CLI Skill:** Build the developer audit command.
- [ ] **Vercel Proxy:** Deploy the keyless bridge for the plugin.

---

## 🛠️ Built With

*   **Figma API:** For component inspection and layer manipulation.
*   **MCP (Model Context Protocol):** Leveraging `southleft-ds-mcp` for design system intelligence.
*   **Next.js:** Powering the hosted communication bridge.
*   **BMAD Framework:** For agentic development and workflow management.

---

<p align="center">
  Built for the 2026 Hackathon by the Propper Team.
</p>
