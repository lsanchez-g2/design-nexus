# design-nexus

**Universal design system specification generator for Claude Code**

Transforms Figma design systems into `/specs/` — a machine+human readable specification directory that bridges Figma designs and production code.

[![Version](https://img.shields.io/badge/version-3.1.0-blue.svg)](CHANGELOG.md)
[![Status](https://img.shields.io/badge/status-production-green.svg)](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)
[![Tested Systems](https://img.shields.io/badge/tested-18%20design%20systems-brightgreen.svg)](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)

---

## 🚀 What's New in v3.1

### Sync Mode (SHIPPED)
**Automatically detect when Figma and /specs/ drift apart**

```bash
# Create baseline
/design-nexus sync https://figma.com/design/[fileKey]/...

# Later, after designer updates Figma...
/design-nexus sync https://figma.com/design/[fileKey]/...
# ⚠️ DRIFT DETECTED: 5 changes (2 breaking, 2 enhancements, 1 cosmetic)
# Choose: Update all | Update selectively | Manual review
```

**Validated on 18 real-world design systems** (Stripe, Duolingo, GitHub, Shopify, IBM, Microsoft, and more).

[→ See validation report](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)

---

## ⚡ Quick Start

### Prerequisites
- [Claude Code](https://claude.ai/code) installed
- Figma MCP plugin enabled
- Figma design system URL

### Installation

```bash
# Clone repository
git clone https://github.com/lsanchez-g2/design-nexus.git

# Install skill
cp -r design-nexus ~/.claude/skills/design-nexus

# Verify
ls ~/.claude/skills/design-nexus/SKILL.md
```

### First Run

```
User: "Audit this Figma design system: https://figma.com/design/abc123/MySystem"

Claude loads design-nexus and:
✓ Extracts all tokens (colors, typography, spacing, etc.)
✓ Audits all components (8-point quality check)
✓ Validates shadcn/ui conventions
✓ Generates /specs/ directory (LLM-optimized structure)
✓ Creates baseline snapshot for future sync
```

---

## 🎯 Core Features

### 1. **Spec Generation** (v1.0-v3.0)
Transforms Figma designs into LLM-friendly documentation:

- **3-Tier File Hierarchy**: `foundations/` → `atoms/molecules/organisms/` → `patterns/`
- **8-Section Component Template**: Overview, Anatomy, Props, States, Tokens, Code Examples, Cross-Refs
- **Token Extraction**: Colors, typography, spacing, radius, shadows, motion (0-2,301 variables tested)
- **Multi-Format Export**: YAML, W3C Style Dictionary, CSS variables with 3-layer indirection
- **75% aligned** with industry LLM-friendly design system standards

### 2. **Auto-Fix Violations** (v2.0)
Automatically corrects shadcn/ui convention violations in Figma:

- Rename properties (`type` → `variant`, `scale` → `size`)
- Rename variant values (`filled` → `primary`, `danger` → `destructive`)
- Bind hardcoded colors to variables
- Set variable scopes (from `ALL_SCOPES` to specific scopes)
- Add missing component descriptions

**Typical improvement**: +20-30 percentage points compliance

### 3. **Drift Detection** (v3.1) 🆕
Detects when Figma and /specs/ diverge:

- **Baseline snapshots**: SHA-256 hash-based change detection
- **Categorized drift**: BREAKING, ENHANCEMENT, COSMETIC
- **Actionable reports**: Console + markdown with recommendations
- **Git integration**: Semantic commits with changelog
- **Selective updates**: Update all, component-specific, or manual review

---

## 📋 Operational Modes

### 🔥 Hardcore Mode (Full System Audit)
Scans entire Figma file and generates complete `/specs/` directory.

**Usage**: `"Audit this Figma design system: [URL]"`

**Output**: 11+ files (tokens, components, patterns, exports)

**Performance**: 10-17 minutes for 1-100+ components

### 🎯 Soft Mode (Single Component)
Extracts ONLY the target component and its referenced tokens.

**Usage**: `"Audit the Button component from [URL]"`

**Output**: 3 files (component spec, screenshot, summary)

**When to use**: Quick validation, single component documentation

### 🔄 Sync Mode (Drift Detection) 🆕
Detects changes between Figma and /specs/ over time.

**Usage**: 
```bash
# System-wide sync
/design-nexus sync [URL]

# Component-specific sync (works on 200+ page files!)
/design-nexus sync --component Button [URL]

# Dry-run (report only)
/design-nexus sync --dry-run [URL]
```

**Output**: Drift report + optional /specs/ updates + git commit

### 🛠️ Fix Mode (v2.0)
Auto-corrects shadcn/ui convention violations in Figma.

**Usage**: Automatically triggered during Hardcore/Soft Mode

**Flags**: `--auto-fix` (skip approval), `--skip-fixes` (skip entirely)

---

## 📊 Production Validation

**18 design systems tested** across 6 complexity tiers:

| System | Components | Variables | Result |
|---|---:|---:|---|
| Stripe Elements | 21 | 0 | ✅ Full extraction |
| Duolingo | 185 | 64 | ✅ Full extraction |
| GitHub Primer | 122 | 328 | ✅ Full extraction |
| Zendesk Garden | 180 | 288 | ✅ Full extraction |
| Polaris (Shopify) | 4,262 | 11 | ✅ Metadata extraction |
| Preline UI | 4,191 | 2,301 | ✅ Metadata extraction |
| Flowbite Pro | 208 pages | — | ⚠️ Partial (pagination v3.2) |

**Success Rate**: 78% full extraction, 100% accessibility

[→ Full validation report](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)

---

## 💡 Use Cases

### For Designers
- **Document design decisions**: Capture WHY, not just WHAT
- **Track drift**: Know when designs diverge from docs
- **Validate conventions**: Ensure shadcn/ui compliance
- **Auto-fix violations**: Rename properties, bind tokens automatically

### For Developers
- **Implement from specs**: No more Figma screenshot handoffs
- **Validate code**: Check if implementation matches /specs/
- **Generate components**: Use LLM-friendly docs to scaffold code
- **Stay in sync**: Detect when designs change

### For Design Systems Teams
- **Single source of truth**: Bridge Figma ↔ Docs ↔ Code
- **LLM-optimized**: 75% aligned with industry standards
- **Audit at scale**: 1 to 4,000+ components validated
- **Automated drift detection**: Never fall behind again

---

## 🎓 FAQ

### General

**Q: What is design-nexus?**  
A: A Claude Code skill that transforms Figma design systems into LLM-friendly specification directories (`/specs/`). It extracts tokens, audits components, validates conventions, and detects drift over time.

**Q: Do I need to know how to code?**  
A: No! design-nexus is conversational. Just ask Claude to "audit this Figma design system: [URL]" and it handles everything.

**Q: What design systems does it support?**  
A: Any Figma design system. Validated on 18 systems including Stripe, Duolingo, GitHub Primer, Shopify Polaris, IBM Carbon, and more. Works with 21 to 4,000+ components.

**Q: How long does it take?**  
A: 10-17 minutes for full systems (1-100+ components). Single components take 3-5 minutes.

### Installation & Setup

**Q: How do I install it?**  
A: Clone the repo and copy to `~/.claude/skills/design-nexus`. Claude Code auto-discovers it. See [Installation](#-quick-start) above.

**Q: What are the prerequisites?**  
A: Claude Code + Figma MCP plugin. No other dependencies.

**Q: Can I use it with my company's private Figma files?**  
A: Yes! As long as you have access to the file via your Figma account, design-nexus can read it.

### Modes & Features

**Q: When should I use Hardcore vs Soft Mode?**  
A: **Hardcore** = full system audit (first-time documentation). **Soft** = single component deep-dive (quick validation or incremental work).

**Q: What is Sync Mode?**  
A: Detects when Figma designs and /specs/ documentation drift apart. Creates baseline snapshots, compares states, generates drift reports, and optionally updates /specs/.

**Q: How does drift detection work?**  
A: 
1. First run: Creates baseline snapshot (`.design-nexus/snapshots/[fileKey].json`)
2. Designer updates Figma
3. Second run: Compares current Figma state vs baseline
4. Reports drift (BREAKING, ENHANCEMENT, COSMETIC changes)
5. Offers: Update all, Update selectively, Manual review only

**Q: Can I sync specific components on large files?**  
A: Yes! Use `--component ComponentName` flag. Works even on 200+ page files: `/design-nexus sync --component Button [URL]`

**Q: What is Fix Mode?**  
A: Auto-corrects shadcn/ui convention violations in Figma (renames properties, binds tokens, adds descriptions). Typical improvement: +20-30% compliance.

### Generated Output

**Q: What files does it generate?**  
A: 
- **Hardcore Mode**: 11+ files (foundations/, atoms/, molecules/, organisms/, patterns/, exports/)
- **Soft Mode**: 3 files (component spec, screenshot, summary)
- **Sync Mode**: Drift report + optional /specs/ updates

**Q: What format are the specs?**  
A: Markdown with YAML frontmatter (human-readable) + W3C Style Dictionary JSON + CSS variables (machine-readable).

**Q: Can LLMs read the generated specs?**  
A: Yes! That's the point. The 3-tier hierarchy, 8-section component template, and clear structure are optimized for LLM consumption. 75% aligned with industry standards.

**Q: Where are specs saved?**  
A: `./specs/` in your project directory. Commit to git for version control.

### Conventions & Standards

**Q: What are "shadcn/ui conventions"?**  
A: Design patterns from shadcn/ui (popular React component library):
- Prop naming: `variant` not `type`, `size` not `scale`
- Variant values: `primary`, `secondary`, `destructive` (not `filled`, `danger`)
- `asChild` composition pattern
- Accessibility defaults (focus rings, touch targets, contrast)

**Q: Does it enforce specific frameworks?**  
A: No. Specs are framework-agnostic. Code examples show React, Vue, and vanilla JS, but you can implement in any framework.

**Q: What if my design system doesn't follow shadcn/ui conventions?**  
A: design-nexus flags violations and offers auto-fixes (v2.0). You can skip fixes with `--skip-fixes` if you prefer your own conventions.

### Technical & Limitations

**Q: What happens with large design systems (50+ pages)?**  
A: Files with 50+ pages timeout during full extraction (22% of tested systems). Workaround: Use `--component ComponentName` for targeted sync. Fix planned in v3.2 (paginated extraction).

**Q: Which systems had timeout issues?**  
A: Carbon Design (IBM, 54p), Elastic UI (65p), Untitled UI PRO (113p), Flowbite Pro (208p). All still accessible via `--component` flag.

**Q: Does it scan my codebase?**  
A: Not yet. v3.1 only detects drift between Figma ↔ /specs/. Code scanning (Figma ↔ /specs/ ↔ Code) planned for v3.4.

**Q: Can it detect renamed components?**  
A: Not yet. Renamed components show as removed + added in drift reports. Fuzzy matching planned for v3.3.

**Q: What about Text Styles and Effect Styles?**  
A: Currently only Figma Variables are extracted. Text/Effect styles support planned for future versions.

### Workflow & Best Practices

**Q: What's the recommended workflow?**  
A:
1. **First time**: Run Hardcore Mode to generate /specs/
2. **Create baseline**: Run Sync Mode to create snapshot
3. **Over time**: Designer updates Figma
4. **Detect drift**: Run Sync Mode again → see what changed
5. **Update docs**: Choose update strategy (all, selective, manual)
6. **Commit**: Changes auto-committed to git with changelog

**Q: Should I commit snapshots to git?**  
A: **YES**. Snapshots (`.design-nexus/snapshots/`) should be version-controlled. Drift reports (`drift-reports/`) should be gitignored (ephemeral).

**Q: How often should I run Sync Mode?**  
A: Whenever designers push Figma updates. Weekly or sprint-based is typical. Can also integrate into CI/CD.

**Q: Can I run this in CI/CD?**  
A: Yes! Use `--auto-approve` flag to skip approval prompts: `/design-nexus sync --auto-approve [URL]`

### Troubleshooting

**Q: I get "file not accessible" errors**  
A: Make sure:
1. File is duplicated to your Figma workspace (not Community file)
2. You have view/edit access
3. MCP server is authenticated with correct account

**Q: Extraction times out**  
A: Use `--component ComponentName` to sync specific components instead of full file.

**Q: Drift report shows too many changes**  
A: Use `--component` flag to narrow scope, or review drift report and choose "Manual review only" to skip auto-updates.

**Q: How do I report bugs?**  
A: [GitHub Issues](https://github.com/lsanchez-g2/design-nexus/issues)

---

## 📚 Documentation

- **[Changelog](CHANGELOG.md)** — Version history
- **[Validation Report](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)** — Production testing (18 systems)
- **[Implementation Summary](docs/superpowers/SYNC_MODE_V3.1_COMPLETE.md)** — Technical details
- **[Release Notes v1.0](docs/RELEASE_v1.0.md)** — Original feature documentation
- **[Stress Test](docs/STRESS_TEST_MATERIAL_DESIGN_3.md)** — Enterprise validation (171 components)

---

## 🗺️ Roadmap

### ✅ Shipped
- **v1.0** (2026-04-24): Hardcore/Soft Mode, token extraction, 8-point audit
- **v2.0** (2026-04-24): Auto-fix violations in Figma
- **v3.0** (2026-04-24): LLM-friendly architecture (3-tier hierarchy, 8-section template)
- **v3.1** (2026-04-27): Sync Mode (drift detection) ← **YOU ARE HERE**

### 🚧 Planned
- **v3.2** (Q2 2026): Paginated extraction (handle 200+ page files)
- **v3.3** (Q3 2026): Rename detection (fuzzy matching)
- **v3.4** (Q4 2026): Code scanning (3-way diff: Figma ↔ /specs/ ↔ Code)
- **v4.0** (2027): Spec Mode (custom validation rules), Code Connect auto-generation

---

## 🤝 Contributing

Contributions welcome! Please open an issue first to discuss proposed changes.

---

## 📄 License

MIT License — see [LICENSE](LICENSE) file

---

## 🙏 Credits

Built with ❤️ using:
- [Claude Code](https://claude.ai/code) by Anthropic
- [Figma MCP](https://github.com/anthropics/mcp-servers-figma) by Anthropic
- Tested on 18 real-world design systems by amazing teams at Stripe, Duolingo, GitHub, Shopify, IBM, and more

---

**Version**: 3.1.0  
**Status**: ✅ Production Ready  
**Release Date**: 2026-04-27  
**Tested Systems**: 18 (21 to 4,262 components)

🚀 **Ready to sync your design system? [Get started](#-quick-start)**
