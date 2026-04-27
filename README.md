<div align="center">

# 🎨 design-nexus

**Universal design system specification generator for Claude Code**

Transform Figma design systems into `/specs/` — machine+human readable specifications that bridge design and code

[![Version](https://img.shields.io/badge/version-3.1.0-blue.svg)](CHANGELOG.md)
[![Status](https://img.shields.io/badge/status-production-green.svg)](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)
[![Tested Systems](https://img.shields.io/badge/tested-18%20design%20systems-brightgreen.svg)](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)
[![License](https://img.shields.io/badge/license-MIT-orange.svg)](LICENSE)

[Quick Start](#-quick-start) • [Features](#-core-features) • [Modes](#-operational-modes) • [FAQ](#-faq) • [Docs](#-documentation)

---

</div>

## 🚀 What's New in v3.1

<table>
<tr>
<td width="60%">

### **Sync Mode** — Automated Drift Detection

Automatically detect when Figma designs and `/specs/` documentation diverge over time.

**Key Features:**
- 🔄 Baseline snapshot creation
- ⚡ SHA-256 hash-based instant comparison
- 📊 Categorized drift: BREAKING, ENHANCEMENT, COSMETIC
- 🎯 Component-specific or system-wide sync
- 📝 Actionable reports (console + markdown)
- 🔧 Git integration with semantic commits

**Validated on 18 real-world design systems** including Stripe, Duolingo, GitHub Primer, Shopify Polaris, IBM Carbon, and more.

</td>
<td width="40%">

```bash
# Create baseline
/design-nexus sync [URL]

# Later, after updates...
/design-nexus sync [URL]
```

**Output:**
```
⚠️ DRIFT DETECTED
5 changes detected:
  • 2 BREAKING
  • 2 ENHANCEMENT
  • 1 COSMETIC

Choose action:
1) Update all
2) Update selectively
3) Manual review only
```

[→ See validation report](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)

</td>
</tr>
</table>

---

## ⚡ Quick Start

<details>
<summary><b>📦 Installation (click to expand)</b></summary>

### Prerequisites
- [Claude Code](https://claude.ai/code) installed
- Figma MCP plugin enabled
- Figma design system URL

### Install in 3 Steps

```bash
# 1. Clone repository
git clone https://github.com/lsanchez-g2/design-nexus.git

# 2. Install skill
cp -r design-nexus ~/.claude/skills/design-nexus

# 3. Verify
ls ~/.claude/skills/design-nexus/SKILL.md
```

✅ **Done!** Claude Code will auto-discover the skill on next launch.

</details>

<details>
<summary><b>🎯 First Run (click to expand)</b></summary>

### Your First Audit

```
User: "Audit this Figma design system: https://figma.com/design/abc123/MySystem"
```

**Claude will:**
1. ✓ Extract all tokens (colors, typography, spacing, etc.)
2. ✓ Audit all components (8-point quality check)
3. ✓ Validate shadcn/ui conventions
4. ✓ Generate `/specs/` directory (LLM-optimized structure)
5. ✓ Create baseline snapshot for future sync

**Time:** 10-17 minutes for full systems (1-100+ components)

</details>

---

## 🎯 Core Features

<table>
<tr>
<td width="33%" align="center">

### 📋 Spec Generation
**v1.0-v3.0**

Transform Figma into LLM-friendly docs

<img width="50" src="https://raw.githubusercontent.com/github/explore/main/topics/documentation/documentation.png"/>

- 3-tier file hierarchy
- 8-section component template
- Token extraction (0-2,301 vars)
- Multi-format exports
- **75% industry aligned**

</td>
<td width="33%" align="center">

### 🔧 Auto-Fix Violations
**v2.0**

Correct shadcn/ui conventions in Figma

<img width="50" src="https://raw.githubusercontent.com/github/explore/main/topics/react/react.png"/>

- Rename properties/variants
- Bind hardcoded colors
- Set variable scopes
- Add descriptions
- **+20-30% compliance**

</td>
<td width="33%" align="center">

### 🔄 Drift Detection
**v3.1** 🆕

Track design-doc divergence

<img width="50" src="https://raw.githubusercontent.com/github/explore/main/topics/git/git.png"/>

- Baseline snapshots
- SHA-256 change detection
- Categorized drift reports
- Git integration
- **Validated on 18 systems**

</td>
</tr>
</table>

---

## 📋 Operational Modes

<details open>
<summary><b>🔥 Hardcore Mode — Full System Audit</b></summary>

<br>

Scans entire Figma file and generates complete `/specs/` directory.

**Usage:**
```
"Audit this Figma design system: [URL]"
```

**Output:** 11+ files (tokens, components, patterns, exports)

**Structure:**
```
/specs/
├── foundations/        # Design tokens
│   ├── colors.yml
│   ├── typography.yml
│   └── spacing.yml
├── atoms/              # Single-purpose components
├── molecules/          # Composed components
├── organisms/          # Complex components
├── patterns/           # Layout rules
└── exports/
    └── style-dictionary.json
```

**Performance:** 10-17 minutes for 1-100+ components

**When to use:** First-time documentation, full system audit, baseline creation

</details>

<details>
<summary><b>🎯 Soft Mode — Single Component Deep-Dive</b></summary>

<br>

Extracts ONLY the target component and its referenced tokens.

**Usage:**
```
"Audit the Button component from [URL]"
"I just need the Input spec from [URL]"
```

**Output:** 3 files (component spec, screenshot, summary)

**When to use:**
- Quick component validation
- Single component documentation
- Faster iteration during development

</details>

<details>
<summary><b>🔄 Sync Mode — Drift Detection</b></summary>

<br>

Detects changes between Figma and `/specs/` over time.

**Usage:**
```bash
# System-wide sync
/design-nexus sync [URL]

# Component-specific sync (works on 200+ page files!)
/design-nexus sync --component Button [URL]

# Dry-run (report only, no updates)
/design-nexus sync --dry-run [URL]
```

**Workflow:**
1. **First run:** Creates baseline snapshot
2. **Designer updates Figma**
3. **Second run:** Detects drift
4. **Choose action:** Update all / Selective / Manual review
5. **Auto-commit:** Changes saved to git with changelog

**Output:** Drift report + optional `/specs/` updates + git commit

**Drift Categories:**
- 🔴 **BREAKING:** Component/variant removed, property renamed
- 🟡 **ENHANCEMENT:** Component/variant added, new property
- 🟢 **COSMETIC:** Token value changed, description updated

</details>

<details>
<summary><b>🛠️ Fix Mode — Auto-Correct Violations</b></summary>

<br>

Automatically corrects shadcn/ui convention violations in Figma.

**Triggered:** Automatically during Hardcore/Soft Mode

**Auto-Fixes:**
- ✅ Rename properties (`type` → `variant`, `scale` → `size`)
- ✅ Rename variant values (`filled` → `primary`, `danger` → `destructive`)
- ✅ Bind hardcoded colors to variables
- ✅ Set variable scopes (from `ALL_SCOPES` to specific scopes)
- ✅ Add missing component descriptions

**Flags:**
- `--auto-fix` — Skip approval, apply all fixes
- `--skip-fixes` — Skip fix phase entirely

**Typical improvement:** +20-30 percentage points compliance

</details>

---

## 📊 Production Validation

<div align="center">

**18 design systems tested** across 6 complexity tiers

</div>

<table>
<tr>
<th>System</th>
<th>Components</th>
<th>Variables</th>
<th>Result</th>
</tr>
<tr>
<td>Stripe Elements</td>
<td align="right">21</td>
<td align="right">0</td>
<td>✅ Full extraction</td>
</tr>
<tr>
<td>Duolingo</td>
<td align="right">185</td>
<td align="right">64</td>
<td>✅ Full extraction</td>
</tr>
<tr>
<td>GitHub Primer</td>
<td align="right">122</td>
<td align="right">328</td>
<td>✅ Full extraction</td>
</tr>
<tr>
<td>Zendesk Garden</td>
<td align="right">180</td>
<td align="right">288</td>
<td>✅ Full extraction</td>
</tr>
<tr>
<td>Polaris (Shopify)</td>
<td align="right"><b>4,262</b></td>
<td align="right">11</td>
<td>✅ Metadata extraction</td>
</tr>
<tr>
<td>Preline UI</td>
<td align="right"><b>4,191</b></td>
<td align="right"><b>2,301</b></td>
<td>✅ Metadata extraction</td>
</tr>
<tr>
<td>Flowbite Pro</td>
<td align="right">208 pages</td>
<td align="right">—</td>
<td>⚠️ Partial (pagination v3.2)</td>
</tr>
<tr>
<td colspan="4" align="center"><i>+ 11 more systems validated</i></td>
</tr>
</table>

<div align="center">

**Success Rate:** 78% full extraction • 100% accessibility

[→ Full validation report](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md)

</div>

---

## 💡 Use Cases

<table>
<tr>
<td width="33%">

### 👨‍🎨 For Designers

- 📝 Document design decisions (WHY, not just WHAT)
- 🔄 Track drift over time
- ✅ Validate shadcn/ui conventions
- 🔧 Auto-fix violations (rename, bind, scope)

</td>
<td width="33%">

### 👨‍💻 For Developers

- 📋 Implement from specs (no Figma screenshots)
- ✓ Validate code against docs
- 🤖 Use LLM-friendly docs to scaffold
- 🔄 Detect when designs change

</td>
<td width="33%">

### 👥 For Teams

- 🎯 Single source of truth
- 🤖 75% LLM-optimized
- 📏 Audit 1 to 4,000+ components
- 🔄 Never fall behind again

</td>
</tr>
</table>

---

## 🎓 FAQ

<details>
<summary><b>What is design-nexus?</b></summary>

<br>

A Claude Code skill that transforms Figma design systems into LLM-friendly specification directories (`/specs/`). It extracts tokens, audits components, validates conventions, and detects drift over time.

</details>

<details>
<summary><b>Do I need to know how to code?</b></summary>

<br>

**No!** design-nexus is conversational. Just ask Claude:

```
"Audit this Figma design system: [URL]"
```

Claude handles everything automatically.

</details>

<details>
<summary><b>What design systems does it support?</b></summary>

<br>

**Any Figma design system.** Validated on 18 systems including:
- Stripe Elements
- Duolingo
- GitHub Primer
- Shopify Polaris (4,262 components!)
- IBM Carbon
- Microsoft Fluent
- And more...

Works with 21 to 4,000+ components.

</details>

<details>
<summary><b>How long does it take?</b></summary>

<br>

| Mode | Time |
|---|---|
| **Hardcore** (full system) | 10-17 min |
| **Soft** (single component) | 3-5 min |
| **Sync** (drift detection) | 1-10 sec |
| **Fix** (auto-correct) | 2-5 min |

</details>

<details>
<summary><b>When should I use Hardcore vs Soft Mode?</b></summary>

<br>

| Mode | When to Use |
|---|---|
| **Hardcore** | First-time documentation, full system audit |
| **Soft** | Single component deep-dive, quick validation |
| **Sync** | Detect drift after Figma updates |
| **Fix** | Auto-correct shadcn/ui violations |

</details>

<details>
<summary><b>What is Sync Mode?</b></summary>

<br>

**Sync Mode detects when Figma designs and `/specs/` documentation drift apart.**

**Workflow:**
1. **First run:** Creates baseline snapshot
2. **Designer updates Figma**
3. **Second run:** Detects drift (BREAKING, ENHANCEMENT, COSMETIC)
4. **Review report:** Choose update strategy
5. **Auto-commit:** Changes saved to git

**Example:**
```bash
/design-nexus sync https://figma.com/design/[fileKey]/...
```

**Output:**
```
⚠️ DRIFT DETECTED: 5 changes
  • 2 BREAKING (variant removed, property renamed)
  • 2 ENHANCEMENT (new variant, new property)
  • 1 COSMETIC (token value changed)
```

</details>

<details>
<summary><b>Can I sync specific components on large files?</b></summary>

<br>

**Yes!** Use the `--component` flag:

```bash
/design-nexus sync --component Button [URL]
```

This works even on **200+ page files** that would otherwise timeout.

</details>

<details>
<summary><b>What files does it generate?</b></summary>

<br>

**Hardcore Mode:** 11+ files
```
/specs/
├── foundations/        # Design tokens (colors, typography, etc.)
├── atoms/              # Single-purpose components (Button, Input)
├── molecules/          # Composed components (Card, Dropdown)
├── organisms/          # Complex components (DataTable, Modal)
├── patterns/           # Layout rules, responsive patterns
└── exports/
    └── style-dictionary.json  # W3C token format
```

**Soft Mode:** 3 files (component spec, screenshot, summary)

**Sync Mode:** Drift report + optional `/specs/` updates

</details>

<details>
<summary><b>Can LLMs read the generated specs?</b></summary>

<br>

**Yes! That's the point.**

The 3-tier hierarchy, 8-section component template, and clear structure are optimized for LLM consumption.

**75% aligned** with industry LLM-friendly design system standards.

</details>

<details>
<summary><b>What are "shadcn/ui conventions"?</b></summary>

<br>

Design patterns from [shadcn/ui](https://ui.shadcn.com/) (popular React component library):

| Convention | Example |
|---|---|
| **Prop naming** | `variant` not `type`, `size` not `scale` |
| **Variant values** | `primary`, `secondary`, `destructive` (not `filled`, `danger`) |
| **Composition** | `asChild` pattern for component composition |
| **Accessibility** | Focus rings, touch targets (44×44px), contrast (4.5:1) |

design-nexus flags violations and offers auto-fixes.

</details>

<details>
<summary><b>What happens with large design systems (50+ pages)?</b></summary>

<br>

**Issue:** Files with 50+ pages timeout during full extraction (22% of tested systems).

**Affected systems:**
- Carbon Design (IBM) — 54 pages
- Elastic UI — 65 pages
- Untitled UI PRO — 113 pages
- Flowbite Pro — 208 pages

**Workaround:** Use `--component ComponentName` for targeted sync.

**Fix planned:** Paginated extraction in v3.2 (2-3 days implementation).

</details>

<details>
<summary><b>Does it scan my codebase?</b></summary>

<br>

**Not yet.** v3.1 only detects drift between **Figma ↔ /specs/**.

Code scanning (**Figma ↔ /specs/ ↔ Code**) planned for **v3.4**.

</details>

<details>
<summary><b>What's the recommended workflow?</b></summary>

<br>

**First Time:**
1. Run **Hardcore Mode** to generate `/specs/`
2. Run **Sync Mode** to create baseline snapshot
3. Commit everything to git

**Ongoing:**
1. Designer updates Figma
2. Run **Sync Mode** to detect drift
3. Review drift report
4. Choose update strategy (all / selective / manual)
5. Changes auto-committed to git

**How often:** Weekly or sprint-based sync recommended.

</details>

<details>
<summary><b>Should I commit snapshots to git?</b></summary>

<br>

| Directory | Commit? | Why |
|---|---|---|
| `.design-nexus/snapshots/` | ✅ **YES** | Version-controlled baselines |
| `drift-reports/` | ❌ **NO** | Ephemeral reports (gitignored) |
| `/specs/` | ✅ **YES** | Source of truth documentation |

Add to `.gitignore`:
```
drift-reports/
```

</details>

<details>
<summary><b>Can I run this in CI/CD?</b></summary>

<br>

**Yes!** Use `--auto-approve` to skip prompts:

```bash
/design-nexus sync --auto-approve [URL]
```

Useful for automated weekly sync jobs.

</details>

<details>
<summary><b>I get "file not accessible" errors — what do I do?</b></summary>

<br>

**Check:**
1. ✓ File is duplicated to **your Figma workspace** (not Community file)
2. ✓ You have view/edit access
3. ✓ MCP server authenticated with correct account

**Fix:** Duplicate the file to your workspace and use that URL.

</details>

<details>
<summary><b>Extraction times out — what do I do?</b></summary>

<br>

Use `--component ComponentName` to sync specific components:

```bash
/design-nexus sync --component Button [URL]
```

Works even on 200+ page files!

</details>

<details>
<summary><b>How do I report bugs?</b></summary>

<br>

[Open a GitHub Issue](https://github.com/lsanchez-g2/design-nexus/issues)

Include:
- Figma file URL (or size: pages/components)
- Mode used (Hardcore/Soft/Sync)
- Error message
- Expected vs actual behavior

</details>

---

## 📚 Documentation

<table>
<tr>
<td width="50%">

### 📖 Core Docs
- [Changelog](CHANGELOG.md) — Version history
- [Validation Report](docs/superpowers/SYNC_MODE_V3.1_VALIDATION_REPORT.md) — 18 systems tested
- [Implementation Summary](docs/superpowers/SYNC_MODE_V3.1_COMPLETE.md) — Technical details

</td>
<td width="50%">

### 🔬 Deep Dives
- [Release Notes v1.0](docs/RELEASE_v1.0.md) — Original features
- [Stress Test](docs/STRESS_TEST_MATERIAL_DESIGN_3.md) — Enterprise validation (171 components)

</td>
</tr>
</table>

---

## 🗺️ Roadmap

<table>
<tr>
<td width="50%">

### ✅ Shipped

- **v1.0** (2026-04-24) — Hardcore/Soft Mode, tokens, audit
- **v2.0** (2026-04-24) — Auto-fix violations
- **v3.0** (2026-04-24) — LLM-friendly architecture
- **v3.1** (2026-04-27) — Sync Mode ← **YOU ARE HERE**

</td>
<td width="50%">

### 🚧 Planned

- **v3.2** (Q2 2026) — Paginated extraction (200+ pages)
- **v3.3** (Q3 2026) — Rename detection (fuzzy matching)
- **v3.4** (Q4 2026) — Code scanning (3-way diff)
- **v4.0** (2027) — Spec Mode, Code Connect auto-gen

</td>
</tr>
</table>

---

<div align="center">

## 🤝 Contributing

Contributions welcome! Please [open an issue](https://github.com/lsanchez-g2/design-nexus/issues) first to discuss proposed changes.

---

## 📄 License

MIT License — see [LICENSE](LICENSE) file

---

## 🙏 Credits

Built with ❤️ using:
- [Claude Code](https://claude.ai/code) by Anthropic
- [Figma MCP](https://github.com/anthropics/mcp-servers-figma) by Anthropic

Tested on 18 real-world design systems by teams at:  
**Stripe • Duolingo • GitHub • Shopify • IBM • Microsoft • Adobe** and more

---

<br>

**Version 3.1.0** • Production Ready • 2026-04-27

[![Stars](https://img.shields.io/github/stars/lsanchez-g2/design-nexus?style=social)](https://github.com/lsanchez-g2/design-nexus)
[![Issues](https://img.shields.io/github/issues/lsanchez-g2/design-nexus)](https://github.com/lsanchez-g2/design-nexus/issues)

<br>

**Ready to sync your design system?** [Get started ↑](#-quick-start)

</div>
