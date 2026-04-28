<div align="center">

<!-- Hero Section -->
<img src="https://img.shields.io/badge/version-3.3.0-blue?style=for-the-badge" alt="Version 3.3.0">
<img src="https://img.shields.io/badge/status-production-success?style=for-the-badge" alt="Production Ready">
<img src="https://img.shields.io/badge/performance-⚡%20fast-orange?style=for-the-badge" alt="Fast Performance">

<h1>
  <img src="https://raw.githubusercontent.com/simple-icons/simple-icons/develop/icons/figma.svg" width="40" height="40" style="vertical-align: middle; margin-right: 10px;">
  Design Nexus
</h1>

<p style="font-size: 1.2em; color: #666; max-width: 600px; margin: 0 auto 30px;">
  The universal nexus for design systems. Transforms Figma design systems into production-ready code documentation.
</p>

</div>

---

## 🎯 What is Design Nexus?

<table>
<tr>
<td width="50%">

**Design Nexus** is a Claude Code skill that bridges the gap between Figma design systems and developer documentation.

It extracts component specifications, design tokens, and implementation guidelines from Figma files and generates comprehensive markdown documentation in your `/specs/` directory.

</td>
<td width="50%">

```
┌─────────────────────────┐
│    Figma Design         │
│    System               │
└───────────┬─────────────┘
            │
            ▼
    ┌───────────────┐
    │ Design Nexus  │
    └───────┬───────┘
            │
            ▼
┌─────────────────────────┐
│   /specs/ Directory     │
│   Production-Ready Docs │
└─────────────────────────┘
```

</td>
</tr>
</table>

---

## ⚡ Performance Metrics (v3.3.0)

<div align="center">

<table>
<tr>
<td align="center" style="padding: 20px;">
  <h3 style="margin: 0; color: #0369A1;">0.4-0.6s</h3>
  <p style="margin: 5px 0 0 0; color: #666; font-size: 0.9em;">per page extraction</p>
</td>
<td align="center" style="padding: 20px;">
  <h3 style="margin: 0; color: #059669;">20-30%</h3>
  <p style="margin: 5px 0 0 0; color: #666; font-size: 0.9em;">faster on low-density files</p>
</td>
<td align="center" style="padding: 20px;">
  <h3 style="margin: 0; color: #7C3AED;">500+</h3>
  <p style="margin: 5px 0 0 0; color: #666; font-size: 0.9em;">pages scalability</p>
</td>
<td align="center" style="padding: 20px;">
  <h3 style="margin: 0; color: #DC2626;">100%</h3>
  <p style="margin: 5px 0 0 0; color: #666; font-size: 0.9em;">success rate</p>
</td>
</tr>
</table>

</div>

<blockquote style="background: #EFF6FF; border-left: 4px solid #0369A1; padding: 15px; margin: 20px 0;">
<strong>🚀 New in v3.3.0:</strong> Adaptive paginated extraction with dynamic 5-20 page batches based on file density. See <a href="PERFORMANCE.md">PERFORMANCE.md</a> for detailed benchmarks.
</blockquote>

---

## 🎨 Modes

<details open>
<summary><h3 style="display: inline;">🏋️ Hardcore Mode</h3></summary>

<div style="padding: 15px; background: #F8FAFC; border-radius: 8px; margin: 10px 0;">

**Full design system extraction and documentation**

Analyzes all components in a Figma file and generates complete specifications.

**Perfect for:**
- ✅ Setting up a new design system
- ✅ Documenting an entire component library  
- ✅ Creating comprehensive reference documentation

```bash
/design-nexus hardcore https://figma.com/design/[fileKey]/...
```

</div>
</details>

<details>
<summary><h3 style="display: inline;">🪶 Soft Mode</h3></summary>

<div style="padding: 15px; background: #F8FAFC; border-radius: 8px; margin: 10px 0;">

**Single component documentation**

Analyzes one component and generates its specification.

**Perfect for:**
- ✅ Adding a new component to an existing system
- ✅ Updating a specific component's documentation
- ✅ Quick reference generation

```bash
/design-nexus soft --component Button https://figma.com/design/...
```

</div>
</details>

<details>
<summary><h3 style="display: inline;">📋 Spec Mode</h3></summary>

<div style="padding: 15px; background: #F8FAFC; border-radius: 8px; margin: 10px 0;">

**Custom requirements-based documentation**

Generates component specs based on your specific requirements rather than extracting from Figma.

**Perfect for:**
- ✅ Creating specs for custom components
- ✅ Documenting non-Figma components
- ✅ Following specific documentation requirements

```bash
/design-nexus spec --requirements "Create a Button spec with primary/secondary variants"
```

</div>
</details>

<details open>
<summary><h3 style="display: inline;">🔄 Sync Mode (v3.1+)</h3></summary>

<div style="padding: 15px; background: #EFF6FF; border-radius: 8px; margin: 10px 0; border: 2px solid #0369A1;">

**Automated drift detection between Figma and /specs/**

**Perfect for:**
- ✅ Checking for design system updates
- ✅ Maintaining sync between Figma and code
- ✅ Auditing design system changes

**Key Capabilities:**

<table style="width: 100%; margin: 15px 0;">
<tr>
<td>📊 Hash-based drift detection</td>
<td>🎯 Categorized change reports</td>
</tr>
<tr>
<td>🔀 Selective update workflows</td>
<td>📸 Snapshot-based baseline tracking</td>
</tr>
<tr>
<td>🎯 Component-specific sync</td>
<td>🔍 Dry-run mode for report-only checks</td>
</tr>
</table>

```bash
# System-wide sync
/design-nexus sync https://figma.com/design/[fileKey]/...

# Component-specific sync
/design-nexus sync --component Button https://figma.com/design/...

# Dry-run (report only, no updates)
/design-nexus sync --dry-run https://figma.com/design/...
```

<blockquote style="background: #DBEAFE; border-left: 4px solid #0369A1; padding: 10px; margin: 15px 0;">
<strong>⚡ v3.3.0 Performance:</strong> Adaptive paginated extraction with dynamic 5-20 page batches based on density tier
</blockquote>

</div>
</details>

<details>
<summary><h3 style="display: inline;">🔧 Fix Mode</h3></summary>

<div style="padding: 15px; background: #F8FAFC; border-radius: 8px; margin: 10px 0;">

**Validation and correction of existing specifications**

Reviews existing /specs/ files and fixes any issues.

**Perfect for:**
- ✅ Validating existing documentation
- ✅ Fixing broken specs
- ✅ Ensuring spec quality

```bash
/design-nexus fix
```

</div>
</details>

---

## 🚀 Quick Start

<table>
<tr>
<td width="33%" align="center">
  <h3>1️⃣ Install</h3>
  <p>Located at <code>~/.claude/skills/design-nexus/</code></p>
  <p>Automatically available via <code>/design-nexus</code></p>
</td>
<td width="33%" align="center">
  <h3>2️⃣ Extract</h3>
  <p>Run Hardcore Mode on your Figma file</p>
  <p><code>/design-nexus hardcore [url]</code></p>
</td>
<td width="33%" align="center">
  <h3>3️⃣ Sync</h3>
  <p>Set up automated drift detection</p>
  <p><code>/design-nexus sync [url]</code></p>
</td>
</tr>
</table>

---

## 📁 Output Structure

```
your-project/
├── specs/                        # 📝 Generated documentation
│   ├── Button.md                 # Component specifications
│   ├── Input.md
│   ├── Card.md
│   └── ...
│
└── .design-nexus/                # 🔧 Sync Mode metadata
    └── snapshots/
        └── [fileKey].json        # Baseline snapshots
```

---

## 📊 Workflow Examples

### Initial Setup (Hardcore → Sync)

<div style="background: #F0FDF4; padding: 20px; border-radius: 8px; border-left: 4px solid #059669;">

```bash
# Step 1: Generate initial specs
/design-nexus hardcore https://figma.com/design/abc123/MyDesignSystem

# Step 2: Establish baseline snapshot
/design-nexus sync https://figma.com/design/abc123/MyDesignSystem
# ✅ Baseline created automatically
```

</div>

### Regular Drift Detection

<div style="background: #EFF6FF; padding: 20px; border-radius: 8px; border-left: 4px solid #0369A1; margin-top: 20px;">

```bash
# Check for design changes
/design-nexus sync https://figma.com/design/abc123/MyDesignSystem
```

**Then choose:**
1. **Update all** → Regenerates all changed specs
2. **Update selectively** → Choose which components to update
3. **Manual review** → No automatic updates

</div>

---

## 🎯 Adaptive Performance (v3.3.0)

<table style="width: 100%;">
<tr style="background: #F8FAFC;">
<th>Density Tier</th>
<th>Batch Size</th>
<th>Speed</th>
<th>Example System</th>
</tr>
<tr>
<td><strong>VERY_LOW</strong><br/><code>&lt;1 comp/page</code></td>
<td>20 pages</td>
<td style="color: #059669;"><strong>0.4s/page</strong><br/>2× faster ⚡</td>
<td>Preline UI (0.3 avg)</td>
</tr>
<tr style="background: #F8FAFC;">
<td><strong>LOW</strong><br/><code>1-3 comp/page</code></td>
<td>15 pages</td>
<td style="color: #059669;"><strong>0.5s/page</strong><br/>1.3× faster ⚡</td>
<td>Flowbite Pro (1.2 avg)</td>
</tr>
<tr>
<td><strong>MEDIUM</strong><br/><code>3-10 comp/page</code></td>
<td>10 pages</td>
<td><strong>0.6s/page</strong><br/>Standard speed</td>
<td>Carbon Design (3.1 avg)</td>
</tr>
<tr style="background: #F8FAFC;">
<td><strong>HIGH</strong><br/><code>10-40 comp/page</code></td>
<td>5 pages</td>
<td style="color: #F59E0B;"><strong>0.8s/page</strong><br/>Conservative</td>
<td>Dense libraries</td>
</tr>
<tr>
<td><strong>EXTREME</strong><br/><code>&gt;40 comp/page</code></td>
<td>User prompt</td>
<td style="color: #DC2626;"><strong>Manual</strong><br/>Strategic sampling</td>
<td>Polaris (55 avg)</td>
</tr>
</table>

<blockquote style="background: #FEF3C7; border-left: 4px solid #F59E0B; padding: 15px; margin: 20px 0;">
<strong>💡 Smart Optimization:</strong> Design Nexus automatically detects file density and adjusts batch sizes for optimal performance. No configuration needed!
</blockquote>

---

## 📚 Version History

<div align="center">

```
v1.0.0 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2026-04-24
  │   Initial release: Hardcore, Soft, Spec, Fix modes
  │
v3.0.0 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2026-04-24
  │   LLM-friendly architecture
  │
v3.1.0 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2026-04-27
  │   🔄 Sync Mode with drift detection
  │
v3.2.0 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2026-04-27
  │   📦 Paginated extraction (single-session)
  │
v3.2.1 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2026-04-28
  │   🚀 Production release (208 pages validated)
  │
v3.3.0 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2026-04-28 ⭐ LATEST
      ⚡ Adaptive performance (20-30% faster)
```

</div>

<details>
<summary><strong>📝 View detailed changelog</strong></summary>

- **v3.3.0** (2026-04-28) - Adaptive performance (lazy density detection, dynamic 5-20 page batches, 20-30% faster on low-density files)
- **v3.2.1** (2026-04-28) - Production release with chunked pagination (validated up to 208 pages, 0% timeout failures, ~0.6s per page)
- **v3.2.0** (2026-04-27) - Paginated extraction (single-session, limited to 60-page files due to MCP timeout)
- **v3.1.0** (2026-04-27) - Sync Mode with automated drift detection
- **v3.0.0** (2026-04-24) - Initial release with Hardcore, Soft, Spec, and Fix modes

See [CHANGELOG.md](CHANGELOG.md) for complete release notes.

</details>

---

## 🔧 Requirements

<table style="width: 100%;">
<tr>
<td align="center" width="25%">
  <h3>☁️</h3>
  <strong>Claude Code</strong>
</td>
<td align="center" width="25%">
  <h3>🎨</h3>
  <strong>Figma MCP Server</strong><br/>
  <small>(included with Claude Code)</small>
</td>
<td align="center" width="25%">
  <h3>📦</h3>
  <strong>Git</strong><br/>
  <small>(for snapshot versioning)</small>
</td>
<td align="center" width="25%">
  <h3>📁</h3>
  <strong>/specs/ Directory</strong><br/>
  <small>(in your project)</small>
</td>
</tr>
</table>

---

## 📖 Documentation

<table>
<tr>
<td><a href="SKILL.md"><strong>SKILL.md</strong></a></td>
<td>Complete skill implementation and workflow documentation</td>
</tr>
<tr>
<td><a href="CHANGELOG.md"><strong>CHANGELOG.md</strong></a></td>
<td>Version history and release notes</td>
</tr>
<tr>
<td><a href="PERFORMANCE.md"><strong>PERFORMANCE.md</strong></a></td>
<td>Performance benchmarks and file size guidance</td>
</tr>
<tr>
<td><a href="V3.3.0_RELEASE.md"><strong>V3.3.0_RELEASE.md</strong></a></td>
<td>v3.3.0 release notes and validation plan</td>
</tr>
</table>

---

## ⚙️ Git Configuration

<details>
<summary><strong>🔧 Setup for team repos</strong></summary>

<br/>

Add to `.gitignore` (recommended):

```gitignore
# Design Nexus snapshots (optional - track for shared baselines)
.design-nexus/snapshots/
```

Or commit snapshots for shared team baselines:

```bash
git add .design-nexus/snapshots/
git commit -m "chore: add design system baseline snapshot"
```

</details>

---

## 🆘 Support

<div align="center">

<table>
<tr>
<td align="center" width="50%">
  <h3>📚 Documentation</h3>
  <p>Refer to <a href="SKILL.md">SKILL.md</a> for complete documentation</p>
</td>
<td align="center" width="50%">
  <h3>🧪 Testing</h3>
  <p>Check test plans for expected behavior</p>
</td>
</tr>
</table>

</div>

---

<div align="center" style="margin-top: 40px; padding-top: 20px; border-top: 2px solid #E2E8F0;">

<p style="color: #666; font-style: italic;">
  Design Nexus - Transform Figma into code-ready documentation
</p>

<p>
  <img src="https://img.shields.io/badge/Made%20with-Claude%20Code-blue?style=flat-square" alt="Made with Claude Code">
  <img src="https://img.shields.io/badge/Powered%20by-Figma%20MCP-orange?style=flat-square" alt="Powered by Figma MCP">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="MIT License">
</p>

</div>
