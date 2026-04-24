# design-system-architect v3.0 — Specification Document

**Purpose**: Complete specification for rebuilding design-system-architect from the ground up with a new vision: creating a universal source of truth that serves designers, developers, AND AI agents.

**For**: skill-creator agent to implement in a fresh session

**Date**: 2024-04-24

---

## Executive Summary

**Current State (v2.0)**: 3-mode skill (Hardcore/Soft/Spec) that audits Figma design systems and generates AI-consumable artifacts. Apollo v2-specific, primarily serves AI agents.

**Target State (v3.0)**: Universal design system specification generator that creates a `/specs/` directory as the single source of truth, serving designers, developers, tooling, and AI. Works with ANY shadcn/ui-based system. Enables bidirectional sync between Figma ↔ specs ↔ code.

**Core Insight**: Design decisions are infrastructure, not documentation. The /specs/ directory should be the authoritative source that both Figma and code sync against, not a derived artifact.

---

## The Problem We're Solving

### Current Pain Points

1. **Designer-Developer Handoff Gap**
   - Designers work in Figma, developers work in code
   - No shared source of truth
   - Specs get out of sync immediately after handoff
   - "What's the latest?" is asked constantly

2. **AI Can't Bridge the Gap Alone**
   - AI-only artifacts don't help human team members
   - Generates code but doesn't capture WHY decisions were made
   - Can't enforce conventions across arbitrary design systems

3. **System-Specific Solutions Don't Scale**
   - v2.0 was too Apollo-specific
   - Every shadcn/ui-based system follows same conventions but we don't validate them
   - Teams need a tool that works for THEIR system, not Apollo

4. **One-Way Sync is Fragile**
   - Figma → code export breaks when Figma changes
   - No way to validate code changes against design source
   - No continuous monitoring to catch drift

### The Vision: /specs/ as Single Source of Truth

```
┌─────────────────────────────────────────────────┐
│              /specs/ Directory                  │
│         (Human + Machine Readable)              │
│                                                 │
│  • Design decisions with WHY context            │
│  • Token definitions (semantic + primitive)     │
│  • Component contracts (props, variants, states)│
│  • Convention compliance rules                  │
│  • Usage examples + guardrails                  │
└─────────────────────────────────────────────────┘
           ↑                            ↑
           │                            │
    ┌──────┴────────┐          ┌───────┴────────┐
    │     Figma     │          │      Code      │
    │  (Design)     │          │ (Implementation)│
    └──────┬────────┘          └───────┬────────┘
           │                            │
           └────────────┬───────────────┘
                        ↓
                 Continuous Sync
           (bidirectional validation)
```

**Key Principle**: /specs/ is NOT generated from Figma. /specs/ is the CONTRACT that both Figma and code must conform to.

---

## Core Requirements

### 1. Serve All Audiences

**Designers**:
- Read /specs/ to understand component contracts before designing
- Validate Figma designs against spec compliance
- See WHY decisions were made (historical context)

**Developers**:
- Implement components from /specs/ definitions
- Validate code against component contracts
- Export to multiple formats (Style Dictionary, Tailwind, TS types)

**AI Agents**:
- Parse /specs/ as structured context for code generation
- Validate changes don't violate design decisions
- Suggest improvements based on spec gaps

**Tools**:
- Style Dictionary: consume /specs/tokens/*.yml
- Figma Tokens plugin: import/export from /specs/
- Storybook: auto-generate stories from /specs/components/*.md
- TypeScript: generate types from component contracts

### 2. Universal shadcn/ui Support

**Not Apollo-specific**. Works for ANY design system built on shadcn/ui conventions:

- Variant/size prop naming (variant, size, not type/scale)
- Semantic token architecture (primary/secondary/destructive/muted)
- asChild composition pattern
- Compound components (Select.Trigger, Select.Content)
- Accessibility defaults (sr-only, aria-*, focus-visible)

**Convention Validation**: Enforce these patterns in both Figma and code, flagging deviations as errors.

### 3. Bidirectional Sync

**Figma → /specs/**:
- Audit Figma design system
- Generate initial /specs/ if missing
- Flag drift if /specs/ already exists
- Propose updates to /specs/ when Figma changes are valid

**Code → /specs/**:
- Audit codebase component implementations
- Flag violations of /specs/ contracts
- Detect undocumented components
- Propose /specs/ updates when code diverges intentionally

**/specs/ → Exports**:
- Style Dictionary JSON/YAML
- Tailwind config
- Figma Tokens JSON
- TypeScript type definitions
- Storybook story templates

### 4. Design Decisions as Infrastructure

From the LLM design systems article, key insight: WHY matters as much as WHAT.

**Bad (just WHAT)**:
```yaml
Button:
  variants:
    - primary
    - secondary
    - destructive
```

**Good (WHAT + WHY)**:
```yaml
Button:
  variants:
    primary:
      use: "Main user action per screen/section"
      reason: "Visual hierarchy — only one primary action visible at a time"
      examples: ["Submit form", "Create new", "Confirm destructive action"]
      avoid: ["Cancel", "Secondary navigation", "Multiple primaries in same viewport"]
    
    secondary:
      use: "Supporting actions that complement primary"
      reason: "Lower visual weight to maintain hierarchy"
      examples: ["Cancel", "Go back", "View details"]
      
    destructive:
      use: "Actions that delete, remove, or cause irreversible changes"
      reason: "Semantic color (red) signals danger before user acts"
      examples: ["Delete account", "Remove item", "Clear all"]
      avoid: ["Undoable actions", "Non-destructive warnings"]
```

**Every spec entry needs**:
- `use`: When to use this
- `reason`: Why it exists (the design decision behind it)
- `examples`: Concrete use cases
- `avoid`: What NOT to use it for (prevents misuse)

---

## /specs/ Directory Structure

```
/specs/
├── design-decisions.md          # High-level design decisions with context
├── conventions.md                # shadcn/ui compliance rules
├── tokens/
│   ├── colors.yml               # Primitive + semantic color tokens
│   ├── typography.yml           # Font families, sizes, weights, line heights
│   ├── spacing.yml              # Spacing scale (px/rem values)
│   ├── radius.yml               # Border radius values
│   ├── shadows.yml              # Box shadow definitions
│   └── motion.yml               # Transition durations, easings
├── components/
│   ├── Button.md                # Button component spec
│   ├── Input.md                 # Input component spec
│   ├── Select.md                # Select component spec
│   └── ...                      # One file per component
└── exports/                     # Generated outputs (gitignored, rebuilt on demand)
    ├── style-dictionary.json
    ├── tailwind.config.js
    ├── figma-tokens.json
    └── types.ts
```

### Token File Format (YAML)

**Why YAML**: Human-readable, machine-parseable, supports comments, widely adopted (Style Dictionary standard).

**Example: /specs/tokens/colors.yml**

```yaml
# Color Tokens — Primitive + Semantic
# Last updated: 2024-04-24
# Owner: Design team

primitives:
  slate:
    50: "#F8FAFC"
    100: "#F1F5F9"
    200: "#E2E8F0"
    # ... full scale
    900: "#0F172A"
  
  sky:
    50: "#F0F9FF"
    # ... full scale
    700: "#0369A1"

semantic:
  primary:
    value: "{primitives.slate.900}"
    use: "Headings, primary text, dark UI elements"
    reason: "Maximum contrast for readability, establishes visual hierarchy"
    figma: "base/foreground"
    
  secondary:
    value: "{primitives.slate.700}"
    use: "Body text, labels, secondary information"
    reason: "Slightly lower contrast for supporting content"
    figma: "base/foreground-secondary"
    
  cta:
    value: "{primitives.sky.700}"
    use: "Primary action buttons, key interactive elements"
    reason: "Brand color for calls-to-action, stands out from neutrals"
    figma: "accent/primary"
    
  cta-hover:
    value: "{primitives.sky.600}"
    use: "Hover state for cta"
    reason: "Lighter shade signals interactivity"
    figma: "accent/primary-hover"
```

**Required fields**:
- `value`: Token reference or raw value
- `use`: When to use
- `reason`: Why it exists
- `figma`: Corresponding Figma variable name (for sync validation)

### Component File Format (Markdown + YAML frontmatter)

**Why Markdown**: Readable by humans, parseable by machines, supports rich documentation.

**Example: /specs/components/Button.md**

```markdown
---
component: Button
status: stable
owner: Design team
last_updated: 2024-04-24
figma_url: https://figma.com/design/abc123/DesignSystem?node-id=1:234
code_path: src/components/ui/Button.tsx
---

# Button

Primary interactive element for user actions.

## Design Decision

Buttons follow a strict visual hierarchy to guide users toward intended actions:
- **One primary per screen/section** — prevents decision paralysis
- **Destructive variant uses semantic red** — signals danger before irreversible actions
- **Size variants match content density** — lg for marketing, sm for data-dense UIs

## Props

### variant

| Value | Use | Reason | Examples | Avoid |
|---|---|---|---|---|
| `primary` | Main user action | Highest visual weight | Submit form, Create new | Cancel, Multiple primaries |
| `secondary` | Supporting action | Lower visual weight | Go back, Cancel | Primary actions |
| `destructive` | Irreversible action | Semantic red signals danger | Delete, Remove | Warnings, Undoable actions |
| `outline` | Secondary alternative | Lighter than secondary | View details, Learn more | Primary actions |
| `ghost` | Tertiary/inline action | Minimal visual weight | Dismiss, Close | Important actions |
| `link` | Text-like action | Underline on hover | Documentation link | CTAs |

**Default**: `primary`  
**Required**: Yes

### size

| Value | Use | Reason | Examples |
|---|---|---|---|
| `sm` | Compact UIs | Data tables, toolbars | Table actions, Inline tools |
| `default` | Standard UIs | Forms, dialogs | Form submit, Modal actions |
| `lg` | Marketing/landing | Hero sections, CTAs | Sign up, Get started |

**Default**: `default`  
**Required**: No

### disabled

| Value | Use | Reason |
|---|---|---|
| `true` | Prevent interaction | Form validation, loading states |
| `false` | Allow interaction | Default state |

**Default**: `false`  
**Required**: No

### asChild

| Value | Use | Reason |
|---|---|---|
| `true` | Merge with child element | Link wrappers, custom composition |
| `false` | Render as button | Standard use |

**Default**: `false`  
**Required**: No

## States

All variants must support:
- **Default**: Base appearance
- **Hover**: Visual feedback on pointer enter
- **Focus**: Keyboard navigation indicator (focus-visible ring)
- **Active**: Pressed state
- **Disabled**: Non-interactive state (reduced opacity)

**Loading state** (future): Optional, use Spinner component inside Button with disabled={true}

## Accessibility

- Touch target: **minimum 44×44px** on all sizes (use padding to meet minimum)
- Focus ring: **2px solid**, uses cta color, 2px offset
- Disabled: `aria-disabled="true"`, not `disabled` (allows focus for tooltips)
- Labels: Always provide text or `aria-label` if icon-only

## Token Bindings

| Property | Token | Figma Variable |
|---|---|---|
| Background (primary) | `{semantic.cta}` | `accent/primary` |
| Background (secondary) | `transparent` | N/A |
| Border (outline) | `{semantic.border}` | `base/border` |
| Text | `{semantic.primary}` | `base/foreground` |
| Radius | `{radius.lg}` | `radius/lg` |
| Padding (default) | `{spacing.4} {spacing.6}` | `spacing/4`, `spacing/6` |

**No hardcoded values allowed**. All colors, spacing, radius must use tokens.

## Code Example

```tsx
import { Button } from "@/components/ui/Button"

// Primary action
<Button variant="primary" size="default">
  Submit
</Button>

// Destructive action with confirmation
<Button variant="destructive" onClick={handleDelete}>
  Delete Account
</Button>

// Link-styled button
<Button variant="link" asChild>
  <a href="/docs">Documentation</a>
</Button>
```

## Figma Component Map

| Figma Variant | Code Props |
|---|---|
| `Variant=primary, Size=default` | `variant="primary" size="default"` |
| `Variant=secondary, Size=sm` | `variant="secondary" size="sm"` |
| `Variant=destructive, Size=lg` | `variant="destructive" size="lg"` |

**Total variants in Figma**: 18 (6 variants × 3 sizes)  
**All states**: Default, Hover, Focus, Active, Disabled (90 total frames)

## Convention Compliance

✅ **Follows shadcn/ui conventions**:
- Prop names: `variant` (not `type`), `size` (not `scale`)
- Semantic variant names: `primary`, `secondary`, `destructive` (not `filled`, `solid`, `danger`)
- `asChild` composition pattern supported
- Accessibility defaults included

## Known Issues

None.

## Related Components

- **IconButton**: Button with icon-only content
- **ButtonGroup**: Multiple related buttons
- **Spinner**: Loading indicator for buttons
```

---

## Mode Architecture (v3.0)

### Modes Overview

v3.0 keeps the 3-mode structure from v2.0 but fundamentally changes what they do:

| Mode | v2.0 | v3.0 |
|---|---|---|
| **Hardcore** | Full system audit → AI artifacts | Full system audit → /specs/ generation |
| **Soft** | Single component audit → fixes | Single component audit → spec validation |
| **Spec** | Custom requirements validation | Custom requirements → spec updates |
| **NEW: Sync** | N/A | Bidirectional Figma ↔ code validation against /specs/ |

### Mode 1: Hardcore Mode — Full System Specification

**Input**: Figma design system file URL  
**Output**: Complete /specs/ directory

**When to use**:
- First-time setup (no /specs/ exists yet)
- Major design system overhaul
- Quarterly full audit

**Workflow**:

1. **Audit Figma Design System**
   - Scan all variables (colors, typography, spacing, radius, shadows, motion)
   - Scan all component sets (Button, Input, Select, etc.)
   - Map Figma variable names to semantic token names
   - Extract all component variants, props, states
   - Capture component descriptions if present

2. **Generate /specs/ Directory**
   - Create `/specs/tokens/*.yml` from Figma variables
   - Create `/specs/components/*.md` from component sets
   - Generate `/specs/design-decisions.md` (initial template)
   - Generate `/specs/conventions.md` (shadcn/ui compliance rules)
   - Validate all specs against shadcn/ui conventions
   - Flag any convention violations

3. **Human Review + Approval**
   - Show comprehensive report:
     - X tokens extracted
     - Y components documented
     - Z convention violations found
   - Ask: "Generate /specs/ directory with these specs? (yes/no)"

4. **Write /specs/ Files**
   - Write all token YAML files
   - Write all component Markdown files
   - Write design-decisions.md
   - Write conventions.md
   - Write /specs/README.md (explains how to use /specs/)

5. **Export Multi-Format**
   - Generate /specs/exports/style-dictionary.json
   - Generate /specs/exports/tailwind.config.js
   - Generate /specs/exports/figma-tokens.json
   - Generate /specs/exports/types.ts

**Time**: 5-15 minutes (depending on system size)

### Mode 2: Soft Mode — Component Deep-Dive

**Input**: Component page URL or component set node ID  
**Output**: Component spec file + validation report

**When to use**:
- Validate a single component against /specs/
- Document a new component
- Debug component inconsistencies

**Workflow**:

1. **Load Existing Spec (if exists)**
   - Check `/specs/components/{ComponentName}.md`
   - If exists → validation mode
   - If missing → documentation mode

2. **Audit Component in Figma**
   - Run 8-point deep-dive:
     1. Variant Matrix Completeness
     2. State Coverage (Default/Hover/Focus/Active/Disabled)
     3. Token Binding Audit (colors, spacing, radius)
     4. Accessibility (touch targets, contrast, focus rings)
     5. Visual Consistency (cursor, opacity, transitions)
     6. Component Documentation (descriptions, Do/Don't examples)
     7. Code Connect Status (mapped to code?)
     8. Convention Compliance (shadcn/ui patterns)

3. **Validation Report**
   - If spec exists: Compare Figma against spec, flag drift
   - If spec missing: Generate initial spec from Figma state
   - Show compliance score (% following conventions)

4. **Update Spec (after approval)**
   - If drift found: Ask "Update spec to match Figma? (yes/no)"
   - If new component: Ask "Create spec for this component? (yes/no)"
   - Write updated /specs/components/{ComponentName}.md

**Time**: 1-3 minutes

### Mode 3: Spec Mode — Custom Requirements

**Input**: Component + custom requirements from user  
**Output**: Updated spec with custom validation rules

**When to use**:
- Build-to-spec validation ("I need Button to support loading state")
- Custom governance rules ("All buttons must have aria-label")
- Brand-specific constraints ("Primary buttons must use brand color, not sky-700")

**Workflow**:

1. **Capture Custom Requirements**
   - Prompt user: "What are your requirements?"
   - Accept multiple requirements (states, variants, props, tokens, behaviors)
   - Parse into structured validation rules

2. **Validate Against Requirements**
   - Load existing spec
   - Run standard 8-point audit
   - PLUS validate each custom requirement
   - Score: Custom compliance % + Standard compliance %

3. **Report Violations**
   ```markdown
   ## Custom Requirements
   
   ✅ Loading state present across all size variants (3/3 sizes)
   ❌ Icon-only variant missing for Size=sm (found only for default)
   ⚠️  Touch targets ≥ 44px: 2/3 sizes (sm is 36px)
   ```

4. **Update Spec with Custom Rules**
   - Ask: "Add these requirements to spec? (yes/no)"
   - If yes: Update /specs/components/{ComponentName}.md with custom rules
   - Add to `## Custom Requirements` section in spec

**Time**: 1-3 minutes

### Mode 4: Sync Mode — Bidirectional Validation (NEW)

**Input**: Figma file + codebase path  
**Output**: Drift report + sync recommendations

**When to use**:
- Continuous monitoring (daily/weekly)
- Pre-release validation
- After major Figma or code changes

**Workflow**:

1. **Scan Both Sources**
   - Audit Figma design system
   - Audit codebase components (parse React/Vue/etc. files)
   - Load /specs/ as source of truth

2. **Three-Way Comparison**
   ```
   /specs/components/Button.md (SOURCE OF TRUTH)
         ↑              ↑
         │              │
    Figma Button    src/components/ui/Button.tsx
   ```
   
   For each component:
   - Compare Figma variants vs spec → flag Figma drift
   - Compare code props vs spec → flag code drift
   - Compare Figma vs code directly → flag inconsistencies

3. **Drift Report**
   ```markdown
   # Sync Report — 2024-04-24
   
   ## Figma Drift (3 issues)
   
   ❌ Button: Figma has hardcoded #0369A1, spec requires {semantic.cta}
   ❌ Input: Missing "error" variant in Figma (spec defines it)
   ⚠️  Select: Figma has 4 sizes, spec defines 3 (lg size not in spec)
   
   ## Code Drift (2 issues)
   
   ❌ Button: Code uses "type" prop, spec requires "variant"
   ⚠️  Input: Code has "helper-text" variant, not in spec
   
   ## Recommendations
   
   1. Update Figma Button to use variable instead of hardcoded color
   2. Add Input error variant to Figma
   3. Either: Add Select lg size to spec, OR remove from Figma
   4. Rename Button "type" prop to "variant" in code
   5. Either: Add Input helper-text to spec, OR remove from code
   ```

4. **Auto-Fix (optional, with approval)**
   - For Figma drift: Generate Figma Plugin API script to apply token bindings
   - For code drift: Generate PR with code changes to match spec
   - For spec updates: Update /specs/ files to include new valid variants

**Time**: 3-5 minutes (depends on system size)

---

## Technical Implementation Notes

### Figma Plugin API Integration

All Figma operations use the `use_figma` tool (MCP server). Required setup:

1. Load `figma:figma-use` skill BEFORE calling any Figma tools
2. Authenticate via OAuth (user grants permission)
3. Use Plugin API to:
   - Read variable collections (tokens)
   - Read component sets (components)
   - Read component properties (variants)
   - Write token bindings (swap hardcoded values to variables)
   - Write component descriptions

**Example: Read all color variables**

```javascript
// via use_figma tool
const variables = await figma.variables.getLocalVariablesAsync()
const colorVars = variables.filter(v => v.resolvedType === 'COLOR')

for (const variable of colorVars) {
  const name = variable.name // e.g., "base/foreground"
  const value = variable.valuesByMode[Object.keys(variable.valuesByMode)[0]]
  // Convert to /specs/tokens/colors.yml entry
}
```

**Example: Bind token to component**

```javascript
// Replace hardcoded fill with variable
const button = figma.getNodeById('123:456')
const colorVar = figma.variables.getVariableById('VariableID:abc')

button.fills = [{
  type: 'SOLID',
  boundVariables: { color: { type: 'VARIABLE_ALIAS', id: colorVar.id } }
}]
```

### Codebase Component Parsing

For Mode 4 (Sync), parse React/Vue/Svelte component files to extract:

- Component name
- Props interface (TypeScript)
- Prop types (string, boolean, enum, etc.)
- Default values
- Required vs optional

**Example: Parse Button.tsx**

```typescript
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'destructive' | 'outline' | 'ghost' | 'link'
  size?: 'sm' | 'default' | 'lg'
  disabled?: boolean
  asChild?: boolean
}

const Button = ({ variant = 'primary', size = 'default', ...props }: ButtonProps) => {
  // implementation
}
```

Extract → compare against /specs/components/Button.md:
- variant values match spec? ✅
- size values match spec? ✅
- Missing any props from spec? ❌
- Extra props not in spec? ⚠️

### shadcn/ui Convention Rules

Enforce these patterns in all modes:

**Prop naming**:
- ✅ `variant` / ❌ `type`, `kind`, `style`
- ✅ `size` / ❌ `scale`, `dimension`
- ✅ `asChild` for composition / ❌ `as`, `is`

**Variant naming**:
- ✅ `primary`, `secondary`, `destructive`, `outline`, `ghost`, `link`
- ❌ `filled`, `solid`, `danger`, `text`, `flat`

**Size naming**:
- ✅ `sm`, `default`, `lg` (occasionally `xs`, `xl`)
- ❌ `small`, `medium`, `large`, `1`, `2`, `3`

**Token architecture**:
- Semantic tokens reference primitive tokens
- Primitive tokens are raw values
- ✅ `{primitives.slate.900}` → `{semantic.primary}`
- ❌ Direct primitive references in components

**Compound components**:
- ✅ `Select.Trigger`, `Select.Content`, `Select.Item`
- ❌ `SelectTrigger`, `SelectContent`, `SelectItem` (flat naming)

**Accessibility**:
- Always include `aria-*` attributes where applicable
- Use `focus-visible` for focus rings (not `focus`)
- `sr-only` for screen-reader-only text
- Minimum 44×44px touch targets

Flag violations of any of these rules in all audit modes.

### Export Formats

#### Style Dictionary JSON

```json
{
  "color": {
    "primitives": {
      "slate": {
        "900": { "value": "#0F172A" }
      }
    },
    "semantic": {
      "primary": { "value": "{color.primitives.slate.900}" }
    }
  }
}
```

#### Tailwind Config

```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#0F172A',
        secondary: '#334155',
        cta: '#0369A1',
      }
    }
  }
}
```

#### Figma Tokens JSON

```json
{
  "base": {
    "foreground": {
      "value": "#0F172A",
      "type": "color"
    }
  }
}
```

#### TypeScript Types

```typescript
export interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'destructive' | 'outline' | 'ghost' | 'link'
  size?: 'sm' | 'default' | 'lg'
  disabled?: boolean
  asChild?: boolean
}
```

---

## Migration from v2.0 → v3.0

**Breaking Changes**:

1. **Output format**: v2.0 generated AI-consumable JSON artifacts. v3.0 generates /specs/ directory with human+machine readable files.

2. **Apollo-specific → Universal**: v2.0 assumed Apollo design system. v3.0 works for any shadcn/ui-based system.

3. **One-way → Bidirectional**: v2.0 was Figma → artifacts. v3.0 validates Figma ↔ code against /specs/ source of truth.

4. **New Mode 4**: Sync Mode is entirely new functionality.

**Migration Path**:

For existing v2.0 users:
1. Run Hardcore Mode (v3.0) on their Figma file
2. Generate /specs/ directory
3. Commit /specs/ to git as source of truth
4. Add /specs/exports/ to .gitignore (generated files, not source)
5. Use Sync Mode for ongoing validation

**No automatic migration script needed** — v3.0 is designed to be run fresh on any design system.

---

## Success Metrics

How do we know v3.0 is working?

1. **/specs/ adoption**: Teams commit /specs/ to git and reference it in PRs
2. **Designer use**: Designers read /specs/ before designing new components
3. **Developer use**: Developers implement from /specs/, not Figma screenshots
4. **Sync frequency**: Teams run Sync Mode daily/weekly and fix drift
5. **Convention compliance**: New components score >90% on shadcn/ui conventions
6. **Export usage**: Teams use /specs/exports/ for Storybook, Tailwind, etc.

---

## Implementation Phases

### Phase 1: Core /specs/ Generation (Hardcore Mode)

**Goal**: Can audit a Figma design system and generate complete /specs/ directory.

**Deliverables**:
- Token extraction (colors, typography, spacing, radius, shadows, motion)
- Component documentation (all props, variants, states)
- YAML + Markdown file generation
- Convention validation

**Dependencies**:
- `use_figma` tool (Figma MCP server)
- `figma:figma-use` skill
- File write permissions

**Testing**:
- Run on Apollo v2.1 design system (known reference)
- Run on shadcn/ui default components
- Run on arbitrary user system

### Phase 2: Component Validation (Soft Mode)

**Goal**: Can validate single component against existing spec.

**Deliverables**:
- 8-point component audit
- Spec comparison (Figma vs /specs/)
- Drift detection
- Spec updates

**Dependencies**:
- Phase 1 complete
- Read existing /specs/ files

**Testing**:
- Validate Apollo Button component
- Introduce intentional drift in Figma, detect it
- Add new variant to Figma, propose spec update

### Phase 3: Custom Requirements (Spec Mode)

**Goal**: Can capture custom requirements and validate against them.

**Deliverables**:
- Interactive requirement capture
- Custom validation rules
- Spec augmentation with custom rules

**Dependencies**:
- Phase 2 complete

**Testing**:
- Add "loading state required" rule, validate Button
- Add "44px minimum touch target" rule, validate all components
- Add custom brand color requirement

### Phase 4: Bidirectional Sync (Sync Mode)

**Goal**: Can validate Figma + code against /specs/ and detect drift.

**Deliverables**:
- Codebase component parsing
- Three-way comparison (Figma vs /specs/ vs code)
- Drift report generation
- Auto-fix proposals

**Dependencies**:
- Phase 1-3 complete
- Code parsing capabilities (TypeScript, JSX)

**Testing**:
- Sync Apollo v2.1 Figma + codebase
- Introduce Figma drift, detect it
- Introduce code drift, detect it
- Generate Figma fixes via Plugin API

### Phase 5: Multi-Format Exports

**Goal**: Can generate all export formats from /specs/.

**Deliverables**:
- Style Dictionary JSON
- Tailwind config
- Figma Tokens JSON
- TypeScript type definitions

**Dependencies**:
- Phase 1 complete (tokens + components documented)

**Testing**:
- Generate exports for Apollo
- Import Figma Tokens JSON back into Figma
- Use Tailwind config in sample project
- Verify TypeScript types match component props

---

## File Locations

After implementation:

```
~/.claude/skills/design-system-architect/
├── SKILL.md                     # Main skill implementation (v3.0)
├── README.md                    # Public documentation
├── CHANGELOG.md                 # Version history
└── examples/                    # Example /specs/ directories
    ├── apollo-v2/               # Apollo reference implementation
    ├── shadcn-default/          # shadcn/ui defaults
    └── minimal/                 # Minimal starter template
```

---

## Key Learnings from v2.0

1. **Plan before implementing** — v2-3MODE-PLAN.md prevented scope creep
2. **3 modes > 2 modes** — clarity beats cleverness
3. **Mode selection at Phase 0** — explicit user choice prevents confusion
4. **Genericize examples** — skill is for any shadcn/ui system, not just Apollo
5. **AI-only artifacts don't scale** — humans need to read /specs/ too
6. **One-way sync is fragile** — bidirectional validation catches drift
7. **WHY matters as much as WHAT** — design decisions are infrastructure

---

## References

1. **LLM Design Systems Article**: https://hvpandya.com/llm-design-systems  
   Key insight: Design decisions as infrastructure, spec files capture WHY

2. **shadcn/ui Conventions**: https://ui.shadcn.com/docs  
   Prop naming, variant naming, composition patterns

3. **Style Dictionary**: https://amzn.github.io/style-dictionary/  
   Token transformation, multi-format exports

4. **Figma Variables API**: https://figma.com/plugin-docs/api/variables/  
   Reading/writing variables, token bindings

5. **Apollo v2.1 Design System**: Reference implementation for testing

---

## Next Steps for skill-creator

1. **Read this spec completely**
2. **Ask clarifying questions** if any requirements are unclear
3. **Start with Phase 1** (Hardcore Mode — /specs/ generation)
4. **Implement incrementally** (one phase at a time, test each)
5. **Use Apollo v2.1 as test case** (known reference design system)
6. **Update README.md** with v3.0 examples and usage
7. **Tag v3.0.0** when Phase 1-5 complete

---

## Open Questions

1. **Token naming conflicts**: If Figma variable is "base/foreground" but spec wants "semantic.primary", how do we handle the mapping? Auto-rename? Manual approval?

2. **Multi-mode support**: Should /specs/ support light + dark mode tokens? If yes, how to structure YAML?

3. **Component composition**: How to document compound components (Select.Trigger) in /specs/? Single file with sections, or separate files?

4. **Version history**: Should /specs/ include version history of design decisions? Or rely on git history?

5. **Conflict resolution**: When Figma and code both diverge from /specs/ in different ways, which wins? Spec is source of truth, but how to guide user to resolution?

---

**End of Specification**

This document should provide everything needed to rebuild design-system-architect from scratch with the new vision baked in. Questions? Ask before starting implementation.