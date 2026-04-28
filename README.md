# Design Nexus

The universal nexus for design systems. Transforms Figma design systems into production-ready code documentation.

---

## Overview

Design Nexus is a Claude Code skill that bridges the gap between Figma design systems and developer documentation. It extracts component specifications, design tokens, and implementation guidelines from Figma files and generates comprehensive markdown documentation in your `/specs/` directory.

---

## Features

### 🏋️ Hardcore Mode
Full design system extraction and documentation. Analyzes all components in a Figma file and generates complete specifications.

**Use when**:
- Setting up a new design system
- Documenting an entire component library
- Creating comprehensive reference documentation

### 🪶 Soft Mode
Single component documentation. Analyzes one component and generates its specification.

**Use when**:
- Adding a new component to an existing system
- Updating a specific component's documentation
- Quick reference generation

### 📋 Spec Mode
Custom requirements-based documentation. Generates component specs based on your specific requirements rather than extracting from Figma.

**Use when**:
- Creating specs for custom components
- Documenting non-Figma components
- Following specific documentation requirements

### 🔄 Sync Mode (v3.1+)
Automated drift detection between Figma design systems and /specs/ documentation.

**Use when**:
- Checking for design system updates
- Maintaining sync between Figma and code
- Auditing design system changes

**Key capabilities**:
- Hash-based drift detection
- Categorized change reports (Breaking, Enhancement, Cosmetic)
- Selective update workflows
- Snapshot-based baseline tracking
- Component-specific sync
- Dry-run mode for report-only checks
- **Adaptive paginated extraction (v3.3.0)** - Dynamic 5-20 page batches based on density, 20-30% faster on low-density files
  - **Performance**: ~0.4-0.6s per page (adaptive, density-dependent)
  - **Speedup**: 20-30% faster on low-density files (v3.3.0 adaptive batching)
  - **Session capacity**: 5+ chunks sustained for read-only operations
  - **Scalability**: Validated up to 208 pages, projected to 500+ pages
  - **Reliability**: 100% success rate, 0% timeout failures with dynamic fallback
  - See [PERFORMANCE.md](PERFORMANCE.md) for detailed benchmarks

### 🔧 Fix Mode
Validation and correction of existing specifications. Reviews existing /specs/ files and fixes any issues.

**Use when**:
- Validating existing documentation
- Fixing broken specs
- Ensuring spec quality

---

## Installation

The skill is located at `~/.claude/skills/design-nexus/`. It's automatically available in Claude Code via the `/design-nexus` command.

---

## Usage

### Basic Commands

```bash
# Hardcore Mode - Full system extraction
/design-nexus hardcore https://figma.com/design/[fileKey]/...

# Soft Mode - Single component
/design-nexus soft --component Button https://figma.com/design/...

# Spec Mode - Custom requirements
/design-nexus spec --requirements "Create a Button spec with primary/secondary variants"

# Sync Mode - System-wide drift detection
/design-nexus sync https://figma.com/design/[fileKey]/...

# Sync Mode - Component-specific
/design-nexus sync --component Button https://figma.com/design/...

# Sync Mode - Dry-run (report only)
/design-nexus sync --dry-run https://figma.com/design/...

# Fix Mode - Validate specs
/design-nexus fix
```

### Workflow Examples

**Initial Setup** (Hardcore → Sync):
```bash
# Step 1: Generate initial specs
/design-nexus hardcore https://figma.com/design/abc123/MyDesignSystem

# Step 2: Establish baseline snapshot
/design-nexus sync https://figma.com/design/abc123/MyDesignSystem
# (Baseline created automatically)
```

**Regular Drift Detection**:
```bash
# Check for design changes
/design-nexus sync https://figma.com/design/abc123/MyDesignSystem

# Review the drift report, then choose:
# 1. Update all → regenerates all changed specs
# 2. Update selectively → choose which components to update
# 3. Manual review → no automatic updates
```

**Component-Specific Sync**:
```bash
# Quick check on a single component
/design-nexus sync --component Button https://figma.com/design/abc123/MyDesignSystem
```

---

## Output Structure

Design Nexus creates documentation in your project's `/specs/` directory:

```
/specs/
├── Button.md           # Component specifications
├── Input.md
├── Card.md
└── ...

.design-nexus/
└── snapshots/
    └── [fileKey].json  # Baseline snapshots for sync mode
```

---

## Project Setup

### Git Configuration

Add snapshot directory to `.gitignore` (recommended for team repos):

```gitignore
# Design Nexus snapshots (optional - track for shared baselines)
.design-nexus/snapshots/
```

Or commit snapshots for shared team baselines:

```bash
git add .design-nexus/snapshots/
git commit -m "chore: add design system baseline snapshot"
```

---

## Requirements

- Claude Code
- Figma MCP server (included with Claude Code)
- Git (for snapshot versioning)
- Project with `/specs/` directory

---

## Version History

See [CHANGELOG.md](CHANGELOG.md) for detailed release notes.

- **v3.3.0** (2026-04-28) - Adaptive performance (lazy density detection, dynamic 5-20 page batches, 20-30% faster on low-density files)
- **v3.2.1** (2026-04-28) - Production release with chunked pagination (validated up to 208 pages, 0% timeout failures, ~0.6s per page)
- **v3.2.0** (2026-04-27) - Paginated extraction (single-session, limited to 60-page files due to MCP timeout)
- **v3.1.0** (2026-04-27) - Sync Mode with automated drift detection
- **v3.0.0** (2026-04-24) - Initial release with Hardcore, Soft, Spec, and Fix modes

---

## Documentation

- **SKILL.md** - Complete skill implementation and workflow documentation
- **CHANGELOG.md** - Version history and release notes
- **PERFORMANCE.md** - Performance benchmarks and file size guidance (v3.2.1)
- **V3.3_ROADMAP.md** - Feature roadmap based on v3.2.1 validation
- **SYNC_MODE_VALIDATION.md** - v3.1 implementation validation report
- **SYNC_MODE_TEST_PLAN.md** - Comprehensive test plan for Sync Mode

---

## Support

For issues, questions, or feature requests, refer to the skill documentation in `SKILL.md` or consult the test plans for expected behavior.

---

*Design Nexus - Transform Figma into code-ready documentation*
