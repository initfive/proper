EXECUTIVE SUMMARY:
Propper is an AI-powered audit tool that reads a Figma component via MCP and tells a designer exactly what is missing before handing off to engineering -- missing props, states, accessibility attributes, naming issues, token misalignment, and composition gaps.MVP: Claude Skill (SKILL.md) that takes a Figma component URL and returns a structured audit report. Uses SouthLeft's bi-directional Figma MCP and DS-Knowledge MCP.

Stretch: Figma Plugin for broader designer adoption.

PROBLEM:
Designers and developers see components differently. A designer builds a button with 4 visual variants. A developer needs that button to have 12+ props, ARIA labels, keyboard handlers, focus management, and composition slots. These gaps are discovered too late -- during handoff or code review -- causing rework and drift.

Figma component properties overlap only about 30-40% with code props. The remaining 60-70% is where handoffs break down.

What gets missed:

  States -- hover, focus, active, disabled, loading, error. Designers skip interactive states because devs handle them in CSS.

  Props -- "disabled" is a boolean, not a variant. "type" is a reserved word in TypeScript. ARIA labels have no visual representation in Figma.

  Composability -- Figma has no "slots." Designers think a card is one thing. Devs see it as a container with composable children.

  Accessibility -- ARIA labels, roles, keyboard navigation, screen reader behavior. Invisible in Figma.

  Overrides -- Code can override a single prop inline. Figma requires you to duplicate the entire variant set.

  Tokens -- Naming mismatches between Figma variables and framework tokens (ShadCN, Tailwind).

  Hidden decisions -- When to show skeleton vs. spinner. Whether a label needs to be its own component. How composition works.

Real examples from the team:

  "Do we need a label component?" -- A designer thought a label was just text above an input. The developer needed it as a separate composable component for accessibility.

  "What is a trigger?" -- Designers had no concept of intangible code components like Trigger or Drawer as behavioral wrappers.

  "We created 10 extra variants for hover states" -- Could have been a script adding 10 lightness points to the primary color, generating the hover token automatically.

  "The developer completely ignored my prop structure" -- Because the Figma props weren't structured in a way that mapped to how the code would be built.

Why existing tools fall short:

  Design Doc by Spectral -- generates specs AFTER the component is built. Doesn't guide the designer BEFORE or DURING.

  Code Connect -- requires both sides already properly structured. Composable components (ShadCN-style) are not well supported.

  Token Studio -- creates JSON tokens but is too technical for designers without code knowledge.

  Manual checklists -- exist at some companies but are inconsistently used and easy to skip.

TARGET USERS:
Primary: Designers building components who lack deep code knowledge. Comfortable with Figma variants and properties but need guidance on what to include so their component is developer-ready.

  Secondary: Engineers reviewing Figma designs during handoff. Need a fast, structured way to audit and communicate what is missing.

  Tertiary: Design system specialists wanting to validate Figma-to-code parity across their component library.

THE SOLUTION:
  1. Claude Skill (MVP)
     A SKILL.md file invoked with a Figma component URL.
     Reads component data via SouthLeft Figma MCP.
     Cross-references best-practice specs via DS-Knowledge MCP.
     Runs 3-check audit.
     Returns structured report with findings and recommendations.

  2. Figma Plugin (Stretch)
     Same audit logic, accessible directly within Figma.
     Designer selects a component, runs the plugin.
     Results appear as a frame next to the component.
     Lower barrier to entry -- no Claude/Cursor access needed.

THE 3-CHECK AUDIT:
CHECK 1: Prop Completeness (core value)
    States: hover, focus, active, disabled, loading, error, empty
    Accessibility: ARIA label prop, role, keyboard navigation notes, focus ring
    Composition: sub-components (Label, Icon, Badge), slot definitions
    Naming: conflicts with code reserved words (e.g. "type"), inconsistent casing
    Types: booleans vs. enums (disabled should be boolean, not a variant)
    Code Connect: whether the prop structure is compatible with mapping

  CHECK 2: Token / Variable Alignment
    Hardcoded values that should be tokens (8px instead of radius-md)
    Missing semantic tokens (no disabled color variable)
    Naming mismatches with target framework (ShadCN, Tailwind, or custom)

  CHECK 3: Auto-Update (stretch goal)
    Push fixes back into the Figma component via bi-directional MCP
    Add missing props, rename mismatched tokens, add hidden layers for a11y
    Opt-in and reviewable -- designer approves changes before commit

EXAMPLE AUDIT OUTPUT
PROPPER AUDIT -- Button
  Score: 6/11 checks passed

  PROPS AND STATES
    [PASS] variant: primary / secondary / outline / ghost
    [PASS] size: sm / md / lg
    [FAIL] Missing: disabled (boolean) -- not exposed as prop
    [FAIL] Missing: loading (boolean) -- no loading state
    [FAIL] Missing state: focus -- no focus ring variant or documentation

  ACCESSIBILITY
    [FAIL] No ARIA label prop -- add hidden text prop for screen reader
    [WARN] No keyboard navigation notes documented

  TOKENS
    [PASS] Background uses variable: color/primary
    [FAIL] Border radius hardcoded (8px) -- should use radius/md
    [WARN] No semantic tokens for disabled state colors

  COMPOSITION
    [INFO] Consider adding icon slot (leading/trailing) via instance swap
    [PASS] No unnecessary variant duplication detected

  CODE CONNECT
    [WARN] Prop "Type" conflicts with TypeScript reserved word -- rename to "variant"
    [INFO] Structure is compatible with Code Connect mapping

  RECOMMENDATIONS
    1. Add disabled boolean prop
    2. Add loading boolean prop
    3. Add focus state (focus ring variant or documentation note)
    4. Add hidden text prop for ARIA label
    5. Replace hardcoded border radius with radius/md token
    6. Rename "Type" prop to "variant" to avoid code conflict

TECHNICAL ARCHITECTURE:
MCP Servers:
    SouthLeft Figma MCP -- reads component JSON (props, variants, variables, styles, layers). Bi-directional (can also write back for Check 3).
    SouthLeft DS-Knowledge MCP -- returns best-practice component specs from TJ Pitre's glossary.

  Framework Options:
    ShadCN -- component-level CSS variables
    Tailwind -- utility-based token system
    Custom -- user-provided token library or style guide

  Data Flow:
    User provides Figma URL + optional framework choice
    --> Claude reads SKILL.md (knows the procedure)
    --> Figma MCP extracts component JSON
    --> DS-Knowledge MCP loads best-practice spec for that component type
    --> Run Check 1 (props) and Check 2 (tokens)
    --> Generate structured audit report with score + recommendations
    --> (Stretch) Run Check 3 (auto-fix via MCP)
    --> Return results to user

USER FLOW:
  1. Designer builds a component in Figma
  2. Copies the component URL
  3. Invokes the skill: "Audit this component: [URL]"
  4. Optionally specifies framework: ShadCN, Tailwind, or custom
  5. Skill reads Figma data via MCP
  6. Cross-references DS-Knowledge MCP for best practices
  7. Returns audit report: missing props, token issues, a11y gaps, Code Connect readiness
  8. Designer updates component based on feedback
  9. Re-runs audit until passing
  10. Component is ready for handoff

MVP SCOPE:
Target components: Button, Input/TextField, Checkbox, Card, Select/Dropdown

  In scope:
    Claude Skill (SKILL.md) with 3-check audit (Checks 1-2 fully, Check 3 as stretch)
    MCP integration (SouthLeft Figma + DS Knowledge)
    Framework support (ShadCN, Tailwind, or custom)
    Structured audit report (PASS/FAIL/WARN/INFO)
    DS prototype in Figma (test components, intentionally flawed for demo)
    Documentation / landing page (Framer)
    3-minute presentation with live demo

  Stretch goals:
    Auto-update component in Figma (Check 3)
    Figma Plugin entry point
    Handoff spec document generation (frame next to the component)
    Code Connect compatibility scoring

  Out of scope:
    Full production Figma plugin
    Custom token library ingestion UI
    Automated code generation from audit
    Non-React framework support

CRITICAL PATH:
MCP Setup (gate -- blocks everything)
    --> Skill v0.1 (skeleton: one check, one component)
      --> Skill v0.5 (all checks working on Button)
        --> Expand to more components (Input, Card, etc.)
          --> Skill v1.0 (polished, tested)
            --> Presentation
              --> Submit

  Skill v0.5 needs: audit checklist + test Button in Figma
  Skill v1.0 needs: user testing feedback

  Parallel work (not blocking): Framer landing page, ShadCN/WAI-ARIA research, presentation outline, user flow docs

TIMELINE:
  FRIDAY EVENING (Hours 0-3) -- SETUP SPRINT
    MCP servers set up and verified
    SKILL.md v0.1 (skeleton with Check 1 on Button)
    Button audit checklist written (all props, states, a11y to check for)
    Test Button built in Figma (intentionally flawed)
    Presentation outline drafted
    Framer landing page shell started
    ShadCN + WAI-ARIA Button spec researched
    EU team signs off ~midnight CET

  FRIDAY NIGHT / SAT MORNING (Hours 3-8)
    Integrate checklist into SKILL.md
    Add Check 2 (token alignment)
    Test against Figma Button
    Review and validate skill output for engineering accuracy
    Test as target user -- document what is confusing or missing

  SATURDAY (Hours 8-16) -- INTEGRATION
    Build additional test components (Input, Card)
    Expand skill to cover more component types
    Refine audit output format
    Build "realistic bad component" for demo (real-world mistakes)
    Finish landing page
    Iterate skill based on all feedback

  SATURDAY EVENING (Hours 16-24) -- POLISH
    Team sync call (overlap window ~3-5pm CET / 9-11am ET)
    Final SKILL.md polish
    Finalize presentation script and deck
    Publish landing page
    Final Figma prototype polish

  SUNDAY -- SUBMIT
    Final review
    Record or prepare presentation
    Submit If things are not working by Saturday morning, cut in this order:
    1. Drop all components except Button (demo one thing perfectly)
    2. Drop Check 3 (auto-update)
    3. Drop Figma Plugin from stretch to vision-only
    4. Drop landing page (present from FigJam instead)
    5. Focus everything on: one Button audit working end-to-end + 3-minute presentation

  One thing working flawlessly beats five things half-working.

RISKS AND FALLBACKS:
MCP doesn't work:
    Plan B -- Figma REST API with personal access token
    Plan C -- Manually paste component JSON into the skill
    Test MCP in the first 2 hours. If it fails, switch immediately.

  Skill output is inaccurate:
    Validate every line against real engineering requirements
    Use a fixed, known test component so correctness can be verified
    Engineering expertise on the team validates before demo

  Timezone gaps cause misalignment:
    Post what you finished + what is next when signing off
    FigJam board as single source of truth for status
    Use overlap windows for sync calls

  Too ambitious -- running out of time:
    Follow the cut list in Section 12
    Protect the critical path: one working audit + the presentation
    The presentation sells the vision even if the demo is narrow

  Presentation isn't compelling:
    Open with a real pain point from the team
    Live demo over slides
    Close with vision: Figma plugin, auto-fix, starter packs

SUCCESS CRITERIA:
Minimum (must ship):
    Claude Skill is invokable and returns a structured audit
    Demo on a real Figma Button that produces accurate results
    3-minute presentation with problem, live demo, future vision

  Target (aim for):
    Skill works on Button + at least one more component type
    All 3 checks functional (even if Check 3 is basic)
    Landing page live on Framer
    Skill uses both MCP servers

  Reach (if everything goes well):
    5 component types working
    Auto-update pushes fixes back to Figma
    Figma Plugin proof-of-concept

VISION (Post-Hackathon):
  Phase 2 (Weeks 1-4):
    Figma Plugin entry point
    Full auto-update via bi-directional MCP
    Multiple framework support simultaneously
    Code Connect compatibility scoring
    Spec doc generation placed next to the component in Figma
    Custom token library support

  Phase 3 (Months 2-6):
    Component starter packs with a11y props pre-built
    Storybook integration for bi-directional validation
    Non-React framework support (Vue, Angular, Svelte)
    Community-contributed component specs
    Team collaboration (shared audits, approval workflows)
    CI/CD integration (audit on PR, audit on Figma publish)