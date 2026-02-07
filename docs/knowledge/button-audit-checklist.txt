PROPPER — BUTTON AUDIT CHECKLIST (MVP)
=====================================

Purpose:
Validate whether a Figma Button component is developer-ready
before handoff to engineering.

Component Scope:
Button (primary MVP target)

Total Checks: 11


--------------------------------------------------
1. PROPS & STATES
--------------------------------------------------

[ ] RULE 1 — Variant prop exists
PASS when:
- Component exposes a "variant" prop
- Variant values are semantic (primary, secondary, ghost, outline)

FAIL when:
- Visual styles are duplicated as separate components
- Variant prop is named "type"

Reason:
Variant maps directly to component APIs and avoids duplication.


[ ] RULE 2 — Size prop exists
PASS when:
- Component exposes a "size" prop (sm / md / lg)

FAIL when:
- Sizes are separate components or hardcoded

Reason:
Size is structural, not stylistic.


[ ] RULE 3 — Disabled is a boolean prop
PASS when:
- "disabled" exists as a boolean prop
- Disabled visuals derive from this prop

FAIL when:
- Disabled is implemented as a variant
- Disabled state is missing

Reason:
Booleans map to code; variants cause combinatorial explosion.


[ ] RULE 4 — Loading is a boolean prop
PASS when:
- "loading" exists as a boolean prop
- Loading state is visually defined or documented

FAIL when:
- No loading prop exists
- Loading is assumed to be handled only in code

Reason:
Loading is a user-visible state and must be designed.


[ ] RULE 5 — Focus state is defined
PASS when:
- Focus or focus-visible state exists (ring, outline, etc.)
- OR focus behavior is explicitly documented

FAIL when:
- No focus treatment exists

Reason:
Focus is mandatory for keyboard and accessibility compliance.


--------------------------------------------------
2. ACCESSIBILITY
--------------------------------------------------

[ ] RULE 6 — Accessible label exists
PASS when:
- Button has visible text
- OR a hidden text / aria-label prop exists

FAIL when:
- Icon-only button has no accessible label
- No ARIA considerations are documented

Reason:
Screen readers require text; icons are invisible.


[ ] RULE 7 — Keyboard interaction is defined
PASS when:
- Button supports keyboard interaction (Enter / Space)
- OR expected behavior is documented

WARN when:
- Behavior is assumed but undocumented

Reason:
Keyboard behavior is often skipped but critical.


--------------------------------------------------
3. TOKENS & VARIABLES
--------------------------------------------------

[ ] RULE 8 — No hardcoded layout values
PASS when:
- Border radius uses semantic token (e.g. radius/md)
- Spacing uses tokens

FAIL when:
- Hardcoded values are used (e.g. 8px)

Reason:
Tokens are the contract between design and code.


[ ] RULE 9 — Semantic color tokens are used
PASS when:
- Background, text, and border colors use semantic tokens
- Disabled state uses semantic tokens

FAIL when:
- Raw hex values are used
- Disabled state lacks token definition

Reason:
State theming and dark mode depend on semantics.


--------------------------------------------------
4. COMPOSITION & NAMING
--------------------------------------------------

[ ] RULE 10 — Icon composition is possible
PASS when:
- Leading and/or trailing icon slots exist
- OR icon usage is documented

INFO when:
- Icon support is intentionally not required

Reason:
Composition beats variant explosion.


[ ] RULE 11 — Naming is code-safe
PASS when:
- Prop names do not conflict with reserved words
- Naming is consistent and code-friendly

FAIL when:
- Props named "type", "class", "default", etc.

Reason:
Naming conflicts cause friction and rewrites.


--------------------------------------------------
OUTPUT EXPECTATION
--------------------------------------------------

Audit output should include:
- PASS / FAIL / WARN / INFO per rule
- Total score (passed / total)
- Clear, actionable recommendations

Example:
"Add disabled boolean prop"
"Replace 8px radius with radius/md token"

--------------------------------------------------
END OF CHECKLIST
--------------------------------------------------