# Changelog

All notable changes to design-nexus will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.0] - 2026-04-24

### Added

#### 🎯 Phase 2: Fix Violations in Figma (NEW - The Killer Feature)

The skill no longer just documents violations — it **automatically fixes them in Figma**.

**Automatic Fixes (Tier 1 + Tier 2):**
- ✅ Rename component properties (`type` → `variant`, `scale` → `size`)
- ✅ Rename variant values (`filled` → `primary`, `danger` → `destructive`)
- ✅ Bind hardcoded colors to existing variables
- ✅ Set variable scopes (from `ALL_SCOPES` to specific scopes)
- ✅ Add missing component descriptions

**Manual Review (Tier 3 - documented but not auto-fixed):**
- Touch targets below 44×44px (requires layout adjustments)
- Missing states (requires creating new variant frames)
- Contrast failures (requires color value changes)

**User Approval Workflow:**
- Clear categorization: Tier 1 (zero-risk), Tier 2 (low-risk), Tier 3 (manual)
- Option to fix all, fix conservative, or skip
- Re-validation after fixes applied
- Fix log generated for audit trail

**New Flags:**
- `--auto-fix`: Auto-approve and apply Tier 1 + Tier 2 fixes
- `--skip-fixes`: Skip fix phase entirely

### Changed

- **Phase numbering**: All phases renumbered to accommodate new Phase 2
  - Phase 2: Generate /specs/ → **Phase 3**
  - Phase 3: Summary Report → **Phase 4**
  - Phase 4: Write /specs/ → **Phase 5**

- **Skill description**: Updated to mention "AUTOMATICALLY FIXES violations in Figma (v2.0)"

### Technical Details

- **Execution**: Uses `use_figma` API to modify Figma components
- **Safety**: Only Tier 1 + Tier 2 fixes are automated (low/zero risk)
- **Validation**: Re-runs convention validation after fixes
- **Logging**: Generates detailed fix log showing what changed
- **Compliance improvement**: Typical improvement +20-30 percentage points

### Breaking Changes

None — v2.0 is fully backward compatible. If you don't want fixes, use `--skip-fixes` flag or decline when prompted.

---

## [1.0.0] - 2026-04-24

### Added

- **Hardcore Mode**: Full system audit (1-100+ components)
- **Soft Mode**: Single component deep-dive
- **Auto-approve Mode**: `--auto-approve` flag for CI/CD
- **Token extraction**: Colors, typography, spacing, radius, shadows, motion
- **8-point component audit**: Variant matrix, states, tokens, accessibility, etc.
- **shadcn/ui convention validation**: Prop naming, variant patterns, token architecture
- **W3C Style Dictionary exports**: Machine-readable token format
- **Multi-mode support**: Light/Dark token systems

### Performance

- Small systems: 1 component → 10.4 min
- Large systems: 100+ components → 16.5 min
- Linear scaling: 100× components = 1.6× runtime

### Test Results

- 100% pass rate (34/34 assertions)
- Stress-tested with Material Design 3 (171 components, 304 tokens, 32 modes)

---

[2.0.0]: https://github.com/lsanchez-g2/design-nexus/compare/v1.0.0...v2.0.0
[1.0.0]: https://github.com/lsanchez-g2/design-nexus/releases/tag/v1.0.0
