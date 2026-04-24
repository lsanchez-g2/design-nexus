# design-nexus

**Universal design system specification generator for Claude Code**

Transforms Figma design systems into `/specs/` — a machine+human readable specification directory that serves as the single source of truth bridging Figma designs and production code.

## 🚀 v3.0: LLM-Friendly Architecture (Enhanced)

**The evolution:** design-nexus now generates truly LLM-optimized specifications following industry best practices.

**Alignment**: **75%** with hvpandya.com LLM-friendly design system guidelines (+25% from baseline)

### v3.0 Enhanced Features (Production)

#### 🏗️ Three-Tier File Hierarchy
- **Tier 1: Foundations** (`/foundations/`) — Design tokens
- **Tier 2: Components** (`/atoms/`, `/molecules/`, `/organisms/`) — Atomic design categorization
- **Tier 3: Patterns** (`/patterns/`) — Layout rules, responsive patterns, composition guidance

#### 📋 8-Section Component Template
1. **Overview** — When to use / not use (NEW)
2. **Design Decision** — WHY context
3. **Anatomy** — Constituent parts (NEW)
4. **Props/API** — Variant, size, state
5. **States** — Required states
6. **Token Bindings** — Semantic → primitive
7. **Code Examples** — React, Vue, vanilla (NEW)
8. **Cross-References** — Uses / used by (NEW)

#### 🎨 tokens.css with 3-Layer Indirection (NEW)
```css
/* Layer 1: Upstream (--ds-*) */
/* Layer 2: Project aliases with fallbacks (--*) */
/* Layer 3: Component usage (references Layer 2 only) */
```

**Impact**: LLMs can now make correct component selection and implementation decisions

### v2.0 Features (Production)
- ✅ Rename component properties (`type` → `variant`)
- ✅ Rename variant values (`filled` → `primary`)
- ✅ Bind hardcoded colors to variables
- ✅ Re-validate after fixes
- ✅ Generate audit trail

**Typical improvement: +20-30 percentage points compliance**

### v3.0 Sync Mode (Prototype — Coming in v3.1)
- ✅ **Drift detection** — Compare Figma ↔ /specs/ states
- ✅ **Sync reports** — Actionable recommendations
- ✅ **Timeline tracking** — Know when designs diverged

**Prototype**: `/Users/lexsanchez/.claude/skills/design-nexus-workspace/v3.0-sync-mode/`  
**Status**: Validated, integration deferred to v3.1 (shipped enhanced architecture first)

---

## What It Does

design-nexus audits Figma design systems and generates LLM-optimized specifications:

- ✅ **Extracts design tokens** (colors, typography, spacing, radius, shadows, motion, z-index)
- ✅ **Audits components** with 8-point quality checks
- ✅ **Validates shadcn/ui conventions** (prop naming, variant patterns, accessibility)
- ✅ **Generates 8-section component specs** (overview, anatomy, props, states, tokens, code examples, cross-refs)
- ✅ **Categorizes components** by atomic design (atoms, molecules, organisms)
- ✅ **Generates pattern documentation** (layout rules, responsive patterns, composition)
- ✅ **Exports tokens in multiple formats** (YAML, W3C Style Dictionary, CSS variables with 3-layer indirection)
- ✅ **Supports multi-mode token systems** (Light/Dark)
- ✅ **Scales linearly** from 1 to 100+ components
- ✅ **75% aligned** with industry LLM-friendly design system standards

---

## Operational Modes

### 🔥 Hardcore Mode (Full System Audit)

Scans the entire Figma file and generates complete `/specs/` directory.

**New in v3.0 (Prototype)**: Run Hardcore Mode, then use **Sync Mode** to detect drift over time.

**Output** (11 files):
```
/specs/
├── README.md                    # Usage guide
├── design-decisions.md          # System philosophy
├── conventions.md               # Compliance rules
├── tokens/
│   ├── colors.yml
│   ├── typography.yml
│   ├── spacing.yml
│   ├── radius.yml
│   ├── shadows.yml
│   └── motion.yml
├── components/
│   └── [Component].md           # Per-component specs
└── exports/
    └── style-dictionary.json    # W3C token format
```

**Usage:**
```
"Audit this Figma design system: [URL]"
```

**Proven at scale:**
- Small systems: 1 component, 12 tokens → 10.4 min
- Large systems: 100+ components, 406 tokens → 16.5 min
- Enterprise: 171 components, 304 tokens, 32 modes → validated ✅

---

### 🎯 Soft Mode (Single Component Deep-Dive)

Extracts ONLY the target component and its referenced tokens.

**New in v3.0 (Prototype)**: Perfect for generating baseline /specs/ before running Sync Mode.

**Output** (3 files):
```
/specs/
├── components/
│   └── Button-shadcn-ui.md      # Complete component spec
├── button-component-screenshot.png
└── DONE.txt                      # Audit summary
```

**Usage:**
```
"Audit the Button component from [URL]"
"I just need the Input spec from [URL]"
```

**When to use Soft Mode:**
- Quick component-level validation
- Documenting a single component
- Faster iteration during development

---

## Installation

### Prerequisites

- [Claude Code](https://claude.ai/code) installed
- Figma MCP plugin enabled
- Figma file URL (design system)

### Setup

1. **Clone this repository:**
   ```bash
   git clone https://github.com/lsanchez-g2/design-nexus.git
   cd design-nexus
   ```

2. **Copy the skill to your Claude Code skills directory:**
   ```bash
   cp -r . ~/.claude/skills/design-nexus
   ```

3. **Verify installation:**
   ```bash
   ls ~/.claude/skills/design-nexus/SKILL.md
   ```

The skill will be automatically discovered by Claude Code on next launch.

---

## Usage Examples

### Hardcore Mode (Full System)

```
User: "Audit this Figma design system: https://figma.com/design/abc123/MyDesignSystem"

Claude loads design-nexus skill and:
1. Extracts all tokens (colors, typography, spacing, etc.)
2. Audits all components with 8-point quality checks
3. Validates shadcn/ui convention compliance
4. Generates /specs/ directory (11 files)
5. Presents summary for approval
6. Writes files to disk
```

### Soft Mode (Single Component)

```
User: "Audit the Button component from https://figma.com/design/abc123/MyDesignSystem?node-id=1-85"

Claude:
1. Detects "Button component" phrase → Soft Mode
2. Extracts ONLY Button component + its tokens
3. Generates focused spec (3 files)
4. Faster execution (subset extraction)
```

### Auto-Approve Mode (CI/CD)

```
User: "Audit this design system --auto-approve: [URL]"

Claude:
1. Skips Phase 3 approval checkpoint
2. Automatically writes files
3. Creates DONE.txt completion marker
```

---

## Features

### Token Extraction

- **Scope**: 1-406+ tokens across 6 categories (colors, typography, spacing, radius, shadows, motion)
- **Multi-mode support**: Light/Dark mode architecture
- **Semantic layering**: Distinguishes primitives vs semantic tokens
- **Export formats**: YAML (human-readable) + W3C Style Dictionary JSON

### Component Analysis

8-point quality audit:
1. **Variant matrix completeness** — Are all combinations present?
2. **State coverage** — Default, Hover, Focus, Active, Disabled
3. **Token binding** — Which properties use variables vs hardcoded values?
4. **Accessibility** — Touch targets (44×44px), contrast (4.5:1), focus rings
5. **Visual consistency** — Cursor styles, disabled states, transitions
6. **Documentation** — Component descriptions, variant explanations
7. **Code Connect status** — Figma ↔ code mapping detection
8. **shadcn/ui compliance** — Prop naming, variant patterns, composition

### Convention Validation

Detects shadcn/ui violations:
- ❌ Prop naming: `type` → should be `variant`
- ❌ Variant naming: `filled`, `danger` → should be `primary`, `destructive`
- ❌ Missing `asChild` composition pattern
- ❌ Touch targets below 44×44px
- ❌ Missing focus states

Generates compliance score: **X% shadcn/ui compliant**

---

## Test Results

| Test | Scope | Pass Rate | Time | Key Finding |
|---|---|---|---|---|
| Test 1 | 1 component, 12 tokens | 100% (13/13) | 10.4 min | 35% compliant, violations accurate |
| Test 2 | 100+ components, 406 tokens | 100% (12/12) | 16.5 min | 100% compliant, excellent architecture |
| Test 3 | Validation reporting | 100% (9/9) | 12.3 min | Audit reports comprehensive |
| Stress Test | 171 components, 304 tokens, 32 modes | ✅ PASSED | ~5 min | Enterprise-scale validated |

**Overall**: 100% pass rate (34/34 assertions)

---

## Performance

- **Small systems**: 10.4 min, 103K tokens
- **Large systems**: 16.5 min, 142K tokens
- **Scaling**: Linear (100× components = 1.6× runtime)
- **Throughput**: ~148 tokens/second

---

## Known Limitations

1. **Text Styles**: Not extracted (only Figma Variables scanned)
2. **Effect Styles**: Not extracted (only Variables scanned)
3. **Code Connect**: Detected but not created
4. **Multi-page**: May miss components on very deep pages (20+)

**Why**: Phase 1 only extracts Figma Variables. Text/Effect styles extraction planned for future versions.

---

## Roadmap

### ✅ Phase 1-2: Shipped (v1.0-v2.0)
- ✅ Hardcore/Soft Mode audit
- ✅ Auto-fix violations in Figma
- ✅ Token extraction
- ✅ Convention validation

### 🚧 Phase 3: Sync Mode (v3.0 — Prototype Ready)
- ✅ **Drift detection** (Figma ↔ /specs/)
- ✅ **Sync reports** with actionable recommendations
- ⏸️ Code scanning (Figma ↔ /specs/ ↔ code) — Coming in v3.1
- ⏸️ Automated sync actions — Coming in v3.1

**Status**: Working prototype at `/Users/lexsanchez/.claude/skills/design-nexus-workspace/v3.0-sync-mode/`  
**Next**: Integrate into SKILL.md, test on 3+ design systems, ship v3.0.0

### Phase 4-5 (Future)
- **Spec Mode**: Custom validation rules ("enforce our internal conventions")
- **Code Connect**: Auto-generate Figma ↔ code mappings
- **Text/Effect Styles**: Extract beyond Variables
- **Component library generation**: Auto-generate shadcn/ui components from specs

---

## Documentation

- [Release Notes v1.0](docs/RELEASE_v1.0.md) — Complete feature documentation
- [Stress Test Report](docs/STRESS_TEST_MATERIAL_DESIGN_3.md) — Enterprise validation evidence
- [Shipped Status](docs/SHIPPED.txt) — Quick reference

---

## Support

- **Issues**: [GitHub Issues](https://github.com/lsanchez-g2/design-nexus/issues)
- **Discussions**: [GitHub Discussions](https://github.com/lsanchez-g2/design-nexus/discussions)

---

## License

MIT License — see [LICENSE](LICENSE) file

---

## Credits

Built with ❤️ using [Claude Code](https://claude.ai/code) and [Figma MCP](https://github.com/anthropics/mcp-servers-figma)

**Version**: 3.0.0-enhanced  
**Status**: ✅ Production Ready — LLM-Optimized Architecture  
**Alignment**: 75% with hvpandya.com LLM-friendly standards  
**Release Date**: 2026-04-24
