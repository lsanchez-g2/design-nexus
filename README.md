# design-nexus

**Universal design system specification generator for Claude Code**

Transforms Figma design systems into `/specs/` — a machine+human readable specification directory that serves as the single source of truth bridging Figma designs and production code.

---

## What It Does

design-nexus audits Figma design systems and generates comprehensive specifications:

- ✅ **Extracts design tokens** (colors, typography, spacing, radius, shadows, motion)
- ✅ **Audits components** with 8-point quality checks
- ✅ **Validates shadcn/ui conventions** (prop naming, variant patterns, accessibility)
- ✅ **Generates W3C Style Dictionary exports**
- ✅ **Supports multi-mode token systems** (Light/Dark)
- ✅ **Scales linearly** from 1 to 100+ components

---

## Operational Modes

### 🔥 Hardcore Mode (Full System Audit)

Scans the entire Figma file and generates complete `/specs/` directory.

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

### Phase 2-5 (Future)

- **Spec Mode**: Custom validation rules ("enforce our internal conventions")
- **Sync Mode**: Bidirectional diff (Figma ↔ /specs/ ↔ code)
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

**Version**: 1.0.0  
**Status**: ✅ Production Ready  
**Release Date**: 2026-04-24
