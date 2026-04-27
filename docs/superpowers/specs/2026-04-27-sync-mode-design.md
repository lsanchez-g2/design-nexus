# Design Specification: Sync Mode v3.1

**Date**: 2026-04-27  
**Author**: Design Nexus Team  
**Status**: Approved for Implementation  
**Target**: design-nexus v3.1.0

---

## Executive Summary

Sync Mode adds automated drift detection between Figma design systems and `/specs/` documentation. It compares current Figma state against a baseline snapshot, categorizes changes (Breaking/Enhancement/Cosmetic), generates actionable reports, and offers tiered update options (update all, selective, or manual review).

**Key Features**:
- ✅ Automated drift detection (Figma ↔ /specs/)
- ✅ Smart fallback (uses /specs/ as baseline if no snapshot exists)
- ✅ Tiered response actions (update all, selective, manual review)
- ✅ System-wide OR component-specific sync
- ✅ Project-local snapshot storage (committed to git)
- ✅ Self-contained (no external dependencies, pure SKILL.md workflow)

**Success Metrics**:
- Detects 100% of component/variant changes with 0% false positives
- Executes in <10 seconds for single component
- Works on all tested design systems (shadcn/ui, Ant Design, Chakra UI, Material Design)

---

## Problem Statement

Traditional design system workflows suffer from designer-developer drift:

1. **Designer updates Figma** (new variant, changed color, added state)
2. **Devs don't notice** or don't have time to update code
3. **Production UI drifts** from design
4. **Trust erodes** in the design system
5. **Teams stop using it** and build custom components

**Current state (v3.0)**:
- design-nexus generates great `/specs/` documentation
- BUT specs become stale the moment Figma changes
- No way to detect drift without manual comparison

**Target state (v3.1)**:
- Automated drift detection via `/design-nexus sync`
- Actionable reports with categorized changes
- One-click sync to update `/specs/`
- Snapshots track design system evolution over time

---

## Architecture Overview

### High-Level Workflow

```
┌─────────────────────────────────────────────────┐
│ User: /design-nexus sync [--component Button]  │
└──────────────────┬──────────────────────────────┘
                   │
                   ▼
         ┌─────────────────┐
         │ Phase 0: Detect │
         │   MODE = SYNC   │
         └────────┬────────┘
                  │
                  ▼
    ┌─────────────────────────────┐
    │ Phase 1-Sync: Load States   │
    │ • Extract current Figma     │
    │ • Load baseline snapshot    │
    │ • Fallback: use /specs/     │
    └──────────┬──────────────────┘
               │
               ▼
    ┌─────────────────────────────┐
    │ Phase 2-Sync: Diff Detection│
    │ • Compare states            │
    │ • Categorize changes        │
    │ • Calculate impact          │
    └──────────┬──────────────────┘
               │
               ▼
    ┌─────────────────────────────┐
    │ Phase 3-Sync: Report + Ask  │
    │ • Show drift summary        │
    │ • Offer: Update/Select/Skip │
    └──────────┬──────────────────┘
               │
               ▼
    ┌─────────────────────────────┐
    │ Phase 4-Sync: Execute       │
    │ • Update specs (if chosen)  │
    │ • Save new snapshot         │
    │ • Commit changes            │
    └─────────────────────────────┘
```

### File Structure

```
project-root/
├── .design-nexus/
│   └── snapshots/
│       └── [fileKey].json           # Latest snapshot (e.g., 5nGs36szQAw5jZAOGYkBcV.json)
├── specs/                           # Existing /specs/ directory
│   ├── foundations/
│   ├── atoms/
│   ├── molecules/
│   └── ...
└── drift-reports/                   # Generated reports (gitignored by default)
    └── drift-[timestamp].md
```

**Storage Strategy**:
- **Snapshots**: `.design-nexus/snapshots/[fileKey].json` (committed to git, ~10-50KB each)
- **Drift reports**: `drift-reports/drift-[timestamp].md` (gitignored, for local review)
- **Git tracking**: Snapshots YES (version-controlled baseline), reports NO (ephemeral)

### Integration with Existing Modes

**Phase 0 Mode Detection** (existing):
```javascript
// Current modes
if (prompt.includes('audit') || prompt.includes('--hardcore')) {
  MODE = 'HARDCORE';
} else if (prompt.match(/audit the (\w+) component/)) {
  MODE = 'SOFT';
  TARGET_COMPONENT = match[1];
}

// NEW: Sync Mode detection
if (prompt.includes('sync')) {
  MODE = 'SYNC';
  if (prompt.includes('--component')) {
    TARGET_COMPONENT = extractComponentName(prompt);
  }
}
```

**Reuse Existing Logic**:
- ✅ Phase 1 Figma extraction (Pass 1: tokens, Pass 2: components)
- ✅ Mode detection pattern
- ✅ Git commit workflow
- ❌ Phase 2 Fix Mode (not applicable to Sync)
- ❌ Phase 3-5 processing (Sync has its own workflow)

---

## Snapshot Format & Storage

### JSON Structure

```json
{
  "version": "3.1.0",
  "timestamp": "2026-04-27T10:30:00Z",
  "figma": {
    "fileKey": "5nGs36szQAw5jZAOGYkBcV",
    "fileName": "shadcn-ui Design System",
    "fileUrl": "https://figma.com/design/5nGs36szQAw5jZAOGYkBcV/..."
  },
  "components": {
    "Button": {
      "nodeId": "1:85",
      "type": "COMPONENT_SET",
      "variantCount": 19,
      "properties": {
        "variant": {
          "type": "VARIANT",
          "values": ["default", "primary", "destructive", "outline", "ghost", "link"]
        },
        "size": {
          "type": "VARIANT",
          "values": ["sm", "default", "lg", "icon"]
        },
        "state": {
          "type": "VARIANT",
          "values": ["default", "hover", "focus", "disabled"]
        }
      },
      "variableBindings": {
        "fills": ["base/foreground"],
        "strokes": ["base/border"]
      },
      "hash": "a3f8c9d2e1f4a5b6c7d8e9f0a1b2c3d4"
    },
    "Input": {
      "nodeId": "2:120",
      "type": "COMPONENT_SET",
      "variantCount": 12,
      "properties": {
        "variant": {
          "type": "VARIANT",
          "values": ["default", "error"]
        },
        "size": {
          "type": "VARIANT",
          "values": ["sm", "default", "lg"]
        }
      },
      "variableBindings": {
        "fills": ["base/background"],
        "strokes": ["base/border"]
      },
      "hash": "b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9"
    }
  },
  "variables": {
    "collections": [
      {
        "id": "VariableCollectionId:123",
        "name": "Base",
        "modes": ["Light", "Dark"],
        "variableCount": 42
      }
    ],
    "variables": [
      {
        "id": "VariableID:456",
        "name": "base/foreground",
        "type": "COLOR",
        "values": {
          "Light": "#0F172A",
          "Dark": "#F8FAFC"
        },
        "scopes": ["TEXT_FILL", "FRAME_FILL"]
      },
      {
        "id": "VariableID:457",
        "name": "base/border",
        "type": "COLOR",
        "values": {
          "Light": "#E2E8F0",
          "Dark": "#334155"
        },
        "scopes": ["STROKE_COLOR"]
      }
    ]
  },
  "metadata": {
    "totalComponents": 23,
    "totalVariables": 127,
    "extractionDuration": 4.2,
    "mode": "HARDCORE"
  }
}
```

### Key Fields Explained

**`hash`** (per component):
- SHA-256 hash of properties + variableBindings + variantCount
- Used for quick change detection (compare hashes first before deep diff)
- Recalculated on every snapshot

**`variableBindings`** (per component):
- Lists which Figma variables are bound to this component
- Format: `{ "fills": ["base/foreground"], "strokes": ["base/border"] }`
- Detects token architecture drift (hardcoded → variable or vice versa)

**`variantCount`**:
- Total number of variant combinations (e.g., 3 sizes × 6 variants × 2 states = 36)
- Quick sanity check for variant drift

### Storage Rules

1. **Filename**: `[fileKey].json` (e.g., `5nGs36szQAw5jZAOGYkBcV.json`)
2. **Location**: `.design-nexus/snapshots/`
3. **Git tracking**: YES (commit snapshots)
4. **Overwrite policy**: Always overwrite on new snapshot (one snapshot per file)
5. **Size limit**: None for v3.1 (snapshots are small, ~10-50KB)

---

## Diff Detection Algorithm

### Change Categories

**BREAKING** (High Priority):
- Component removed
- Variant removed
- Required property removed
- Token binding removed (component now uses hardcoded values)

**ENHANCEMENT** (Medium Priority):
- New component added
- New variant added
- New property added
- New token binding (hardcoded value replaced with variable)

**COSMETIC** (Low Priority):
- Token value changed (e.g., `#0F172A` → `#1E293B`)
- Property default value changed
- Component name changed (detected as removed + added)

### Comparison Algorithm

```javascript
function detectDrift(currentState, baselineState) {
  const changes = {
    breaking: [],
    enhancement: [],
    cosmetic: [],
    stats: {
      totalChanges: 0,
      affectedComponents: new Set()
    }
  };

  // 1. Compare components
  for (const [name, current] of Object.entries(currentState.components)) {
    const baseline = baselineState.components[name];
    
    if (!baseline) {
      // New component added
      changes.enhancement.push({
        type: 'COMPONENT_ADDED',
        component: name,
        nodeId: current.nodeId,
        variantCount: current.variantCount,
        action: 'Generate /specs/ file for new component'
      });
      changes.stats.affectedComponents.add(name);
      continue;
    }

    // Quick hash check
    if (current.hash === baseline.hash) {
      // No changes, skip detailed comparison
      continue;
    }

    // Detailed comparison (hash mismatch)
    changes.stats.affectedComponents.add(name);
    compareVariants(current, baseline, changes);
    compareProperties(current, baseline, changes);
    compareTokenBindings(current, baseline, changes);
  }

  // 2. Check for removed components
  for (const [name, baseline] of Object.entries(baselineState.components)) {
    if (!currentState.components[name]) {
      changes.breaking.push({
        type: 'COMPONENT_REMOVED',
        component: name,
        nodeId: baseline.nodeId,
        action: 'Mark as deprecated in /specs/ + add migration guide'
      });
      changes.stats.affectedComponents.add(name);
    }
  }

  // 3. Compare variables (token architecture drift)
  compareVariables(currentState.variables, baselineState.variables, changes);

  // 4. Calculate stats
  changes.stats.totalChanges = 
    changes.breaking.length + 
    changes.enhancement.length + 
    changes.cosmetic.length;

  return changes;
}

function compareVariants(current, baseline, changes) {
  const currentVariants = new Set(current.properties.variant.values);
  const baselineVariants = new Set(baseline.properties.variant.values);

  // New variants
  for (const variant of currentVariants) {
    if (!baselineVariants.has(variant)) {
      changes.enhancement.push({
        type: 'VARIANT_ADDED',
        component: current.name,
        property: 'variant',
        value: variant,
        action: `Add variant="${variant}" to /specs/`
      });
    }
  }

  // Removed variants
  for (const variant of baselineVariants) {
    if (!currentVariants.has(variant)) {
      changes.breaking.push({
        type: 'VARIANT_REMOVED',
        component: baseline.name,
        property: 'variant',
        value: variant,
        action: `Remove variant="${variant}" from /specs/ + document breaking change`
      });
    }
  }
}

function compareProperties(current, baseline, changes) {
  const currentProps = Object.keys(current.properties);
  const baselineProps = Object.keys(baseline.properties);

  // New properties
  for (const prop of currentProps) {
    if (!baselineProps.includes(prop)) {
      changes.enhancement.push({
        type: 'PROPERTY_ADDED',
        component: current.name,
        property: prop,
        values: current.properties[prop].values,
        action: `Add property "${prop}" to Props/API section`
      });
    }
  }

  // Removed properties
  for (const prop of baselineProps) {
    if (!currentProps.includes(prop)) {
      changes.breaking.push({
        type: 'PROPERTY_REMOVED',
        component: baseline.name,
        property: prop,
        action: `Remove property "${prop}" from /specs/`
      });
    }
  }
}

function compareTokenBindings(current, baseline, changes) {
  const currentBindings = current.variableBindings || {};
  const baselineBindings = baseline.variableBindings || {};

  // New bindings (hardcoded → variable)
  for (const [layer, tokens] of Object.entries(currentBindings)) {
    if (!baselineBindings[layer] || baselineBindings[layer].length === 0) {
      changes.enhancement.push({
        type: 'TOKEN_BINDING_ADDED',
        component: current.name,
        layer: layer,
        tokens: tokens,
        action: `Update Token Bindings section: ${layer} now uses ${tokens.join(', ')}`
      });
    }
  }

  // Removed bindings (variable → hardcoded)
  for (const [layer, tokens] of Object.entries(baselineBindings)) {
    if (!currentBindings[layer] || currentBindings[layer].length === 0) {
      changes.breaking.push({
        type: 'TOKEN_BINDING_REMOVED',
        component: baseline.name,
        layer: layer,
        tokens: tokens,
        action: `ALERT: ${layer} no longer uses variables (now hardcoded)`
      });
    }
  }
}

function compareVariables(currentVars, baselineVars, changes) {
  // Compare variable values (cosmetic changes)
  for (const currentVar of currentVars.variables) {
    const baselineVar = baselineVars.variables.find(v => v.id === currentVar.id);
    if (!baselineVar) continue;

    for (const [mode, value] of Object.entries(currentVar.values)) {
      if (baselineVar.values[mode] !== value) {
        changes.cosmetic.push({
          type: 'VARIABLE_VALUE_CHANGED',
          variable: currentVar.name,
          mode: mode,
          before: baselineVar.values[mode],
          after: value,
          action: `Update foundations/colors.yml: ${currentVar.name} (${mode})`
        });
      }
    }
  }
}
```

### Hash Calculation

```javascript
function calculateComponentHash(component) {
  const crypto = require('crypto');
  
  const hashInput = JSON.stringify({
    properties: component.properties,
    variableBindings: component.variableBindings,
    variantCount: component.variantCount
  }, Object.keys(component.properties).sort()); // Deterministic key order
  
  return crypto
    .createHash('sha256')
    .update(hashInput)
    .digest('hex')
    .slice(0, 32); // First 32 chars
}
```

---

## User Workflow & Output

### Command Invocation

**System-wide sync** (all components):
```bash
/design-nexus sync https://figma.com/design/5nGs36szQAw5jZAOGYkBcV/...
```

**Component-specific sync**:
```bash
/design-nexus sync --component Button https://figma.com/design/...
```

**Dry-run** (report only, no updates):
```bash
/design-nexus sync --dry-run https://figma.com/design/...
```

### Console Output Examples

**Scenario 1: Drift Detected**

```
🔄 Sync Mode: Detecting drift...

📸 Loaded baseline snapshot (2026-04-25 10:30 AM)
🎨 Extracted current Figma state (23 components, 127 variables)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 DRIFT DETECTED: 8 changes (2 breaking, 4 enhancements, 2 cosmetic)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️  BREAKING CHANGES (2):
  • Button: Variant "subtle" removed
    → Action: Mark as deprecated in /specs/, add migration guide
  
  • Input: Property "error" removed
    → Action: Remove from /specs/, document breaking change

✨ ENHANCEMENTS (4):
  • Button: New variant "loading" added
    → Action: Add to /specs/atoms/Button.md
  
  • Card: New component added (8 variants)
    → Action: Generate /specs/molecules/Card.md
  
  • Button: Token binding added (fills → base/foreground)
    → Action: Update Token Bindings section
  
  • Input: New property "success" added
    → Action: Add to Props/API section

🎨 COSMETIC CHANGES (2):
  • base/foreground: #0F172A → #1E293B (Light mode)
    → Action: Update foundations/colors.yml
  
  • Button: Default size changed (sm → default)
    → Action: Update Props/API section

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Affected components: Button, Input, Card
Drift report saved: drift-reports/drift-2026-04-27-103045.md

What would you like to do?

1. Update all — Regenerate all affected /specs/ files from current Figma
2. Update selectively — Choose which components to update
3. Manual review only — Save report, no updates

Your choice (1/2/3):
```

**Scenario 2: In Sync**

```
🔄 Sync Mode: Detecting drift...

📸 Loaded baseline snapshot (2026-04-25 10:30 AM)
🎨 Extracted current Figma state (23 components, 127 variables)

✅ NO DRIFT DETECTED

Your design system is in sync! 🎉

Baseline: 2026-04-25 10:30 AM
Current: 2026-04-27 10:30 AM
Components checked: 23
Variables checked: 127
```

**Scenario 3: First Run (No Baseline)**

```
🔄 Sync Mode: Detecting drift...

📸 No baseline snapshot found at .design-nexus/snapshots/5nGs36szQAw5jZAOGYkBcV.json

Checking for /specs/ directory... ✓ Found (generated 2026-04-25)

Using /specs/ as implicit baseline.
Extracting current Figma state...

🎨 Extracted current Figma state (23 components, 127 variables)

[continues with normal drift detection]
```

**Scenario 4: First Run (No Baseline, No /specs/)**

```
🔄 Sync Mode: Detecting drift...

📸 No baseline snapshot found
📂 No /specs/ directory found

Creating baseline snapshot from current Figma state...

🎨 Extracted (23 components, 127 variables)
💾 Saved baseline: .design-nexus/snapshots/5nGs36szQAw5jZAOGYkBcV.json

✅ Baseline created

Run `/design-nexus sync` again to detect drift against this baseline.
```

### Drift Report Format

**File**: `drift-reports/drift-2026-04-27-103045.md`

```markdown
# Drift Report — 2026-04-27 10:30:45

**Baseline**: 2026-04-25 10:30 AM  
**Current**: 2026-04-27 10:30 AM  
**Total Changes**: 8 (2 breaking, 4 enhancements, 2 cosmetic)  
**Affected Components**: Button, Input, Card

---

## ⚠️ Breaking Changes (2)

### Button: Variant "subtle" removed

**Type**: VARIANT_REMOVED  
**Impact**: Existing code using `variant="subtle"` will break  
**Action**: Mark as deprecated in /specs/, add migration guide to CHANGELOG.md

**Migration**:
```diff
- <Button variant="subtle">Text</Button>
+ <Button variant="ghost">Text</Button>
```

---

### Input: Property "error" removed

**Type**: PROPERTY_REMOVED  
**Impact**: Error state no longer available  
**Action**: Remove from /specs/, document breaking change

**Migration**:
```diff
- <Input error={true} />
+ <Input variant="destructive" />
```

---

## ✨ Enhancements (4)

### Button: New variant "loading" added

**Type**: VARIANT_ADDED  
**Property**: variant  
**Value**: loading  
**Action**: Add to /specs/atoms/Button.md

**Usage**:
```tsx
<Button variant="loading">Submitting...</Button>
```

---

### Card: New component added

**Type**: COMPONENT_ADDED  
**Node ID**: 3:150  
**Variant Count**: 8  
**Action**: Generate /specs/molecules/Card.md

**Variants**:
- variant: default, outline
- elevation: none, sm, md, lg

---

### Button: Token binding added

**Type**: TOKEN_BINDING_ADDED  
**Layer**: fills  
**Tokens**: base/foreground  
**Before**: Hardcoded #0F172A  
**After**: Bound to {base/foreground}  
**Action**: Update Token Bindings section in Button.md

---

### Input: New property "success" added

**Type**: PROPERTY_ADDED  
**Property**: success  
**Values**: true, false  
**Action**: Add to Props/API section in Input.md

---

## 🎨 Cosmetic Changes (2)

### base/foreground: Color value changed

**Type**: VARIABLE_VALUE_CHANGED  
**Variable**: base/foreground  
**Mode**: Light  
**Before**: #0F172A  
**After**: #1E293B  
**Action**: Update foundations/colors.yml

---

### Button: Default size changed

**Type**: PROPERTY_DEFAULT_CHANGED  
**Property**: size  
**Before**: sm  
**After**: default  
**Action**: Update Props/API section in Button.md

---

## Recommended Actions

1. ✅ **Update /specs/** to match current Figma (run option 1: Update all)
2. ⚠️ **Document breaking changes** in CHANGELOG.md
3. 📢 **Notify team** of removed variants via Slack/Discord
4. 🧪 **Test code** against new specs (run validation workflow)
5. 💾 **Commit snapshot** to git (`.design-nexus/snapshots/5nGs36szQAw5jZAOGYkBcV.json`)

---

*Generated by design-nexus v3.1.0 Sync Mode*
```

---

## Implementation Details

### Phase 0: Mode Detection

**Add SYNC mode detection to existing Phase 0**:

```javascript
// Existing mode detection
if (userPrompt.includes('--hardcore') || userPrompt.match(/audit.*design system/)) {
  MODE = 'HARDCORE';
} else if (userPrompt.match(/audit the (\w+) component/)) {
  MODE = 'SOFT';
  TARGET_COMPONENT = extractComponentName(userPrompt);
} else if (userPrompt.includes('--auto-approve')) {
  AUTO_APPROVE_MODE = true;
}

// NEW: Sync mode detection
if (userPrompt.includes('sync')) {
  MODE = 'SYNC';
  
  // Check for component-specific sync
  const componentMatch = userPrompt.match(/--component\s+(\w+)/i);
  if (componentMatch) {
    TARGET_COMPONENT = componentMatch[1];
    console.log(`🎯 Component-specific sync: ${TARGET_COMPONENT}`);
  } else {
    console.log(`🔄 System-wide sync: All components`);
  }
  
  // Check for dry-run flag
  if (userPrompt.includes('--dry-run')) {
    DRY_RUN_MODE = true;
    console.log(`👀 Dry-run mode: Report only, no updates`);
  }
}
```

### Phase 1-Sync: Load States

**Extract current Figma state** (reuse existing Phase 1 logic):

```javascript
async function extractCurrentState(fileKey, targetComponent) {
  // Invoke figma:figma-use skill (REQUIRED)
  await invokeSkill('figma:figma-use');
  
  // Extract components (reuse Pass 2 from existing Phase 1)
  const components = await extractComponents(fileKey, targetComponent);
  
  // Extract variables (reuse Pass 1 from existing Phase 1)
  const variables = await extractVariables(fileKey);
  
  return {
    timestamp: new Date().toISOString(),
    figma: {
      fileKey,
      fileName: figmaFileName,
      fileUrl: figmaFileUrl
    },
    components,
    variables,
    metadata: {
      totalComponents: Object.keys(components).length,
      totalVariables: variables.variables.length,
      extractionDuration: extractionTime
    }
  };
}
```

**Load baseline snapshot**:

```javascript
function loadBaseline(fileKey) {
  const snapshotPath = `.design-nexus/snapshots/${fileKey}.json`;
  
  if (fs.existsSync(snapshotPath)) {
    const snapshot = JSON.parse(fs.readFileSync(snapshotPath, 'utf8'));
    console.log(`📸 Loaded baseline snapshot (${snapshot.timestamp})`);
    return snapshot;
  }
  
  // Fallback: Try to construct baseline from /specs/
  if (fs.existsSync('specs/')) {
    console.log(`📂 /specs/ found, using as implicit baseline`);
    return constructBaselineFromSpecs();
  }
  
  // No baseline exists
  console.log(`📸 No baseline found`);
  return null;
}

function constructBaselineFromSpecs() {
  // Parse /specs/ directory to create baseline structure
  const components = {};
  
  // Read all component files (atoms/, molecules/, organisms/)
  const componentFiles = glob.sync('specs/{atoms,molecules,organisms}/*.md');
  
  for (const file of componentFiles) {
    const content = fs.readFileSync(file, 'utf8');
    const component = parseComponentSpec(content);
    components[component.name] = {
      nodeId: component.figmaNodeId,
      type: 'COMPONENT_SET',
      variantCount: component.variants.length,
      properties: component.properties,
      variableBindings: component.tokenBindings,
      hash: calculateComponentHash(component)
    };
  }
  
  // Parse variables from foundations/ (colors.yml, spacing.yml, etc.)
  const variables = parseVariablesFromFoundations();
  
  return {
    version: '3.1.0',
    timestamp: fs.statSync('specs/').mtime.toISOString(),
    figma: { fileKey: 'unknown', fileName: 'Unknown', fileUrl: '' },
    components,
    variables,
    metadata: { totalComponents: Object.keys(components).length }
  };
}

function parseVariablesFromFoundations() {
  // Parse YAML files in foundations/ directory
  const foundationsPath = 'specs/foundations/';
  if (!fs.existsSync(foundationsPath)) {
    return { collections: [], variables: [] };
  }
  
  const variables = [];
  const tokenFiles = glob.sync(`${foundationsPath}*.yml`);
  
  for (const file of tokenFiles) {
    const content = fs.readFileSync(file, 'utf8');
    const tokens = yaml.parse(content);
    
    // Extract variable definitions from YAML structure
    // Example: colors.yml has { primitives: {}, semantics: {} }
    if (tokens.semantics) {
      for (const [name, value] of Object.entries(tokens.semantics)) {
        variables.push({
          id: `semantic-${name}`,
          name,
          type: 'COLOR', // Infer type from file name
          values: { Light: value },
          scopes: ['TEXT_FILL', 'FRAME_FILL', 'SHAPE_FILL']
        });
      }
    }
  }
  
  return {
    collections: [{ id: 'base', name: 'Base', modes: ['Light'], variableCount: variables.length }],
    variables
  };
}
```

### Phase 2-Sync: Diff Detection

**Implement the comparison algorithm** (detailed in "Diff Detection Algorithm" section):

```javascript
function runSyncPhase2(currentState, baselineState) {
  console.log(`\n🔍 Detecting drift (Figma ↔ /specs/)...`);
  
  const changes = detectDrift(currentState, baselineState);
  
  if (changes.stats.totalChanges === 0) {
    console.log(`✅ NO DRIFT DETECTED\n`);
    console.log(`Your design system is in sync! 🎉\n`);
    console.log(`Baseline: ${baselineState.timestamp}`);
    console.log(`Current: ${currentState.timestamp}`);
    return null; // Exit early, no drift
  }
  
  // Drift detected, continue to Phase 3
  return changes;
}
```

### Phase 3-Sync: Report + Ask

**Generate and display drift report**:

```javascript
function runSyncPhase3(changes, currentState, baselineState) {
  // Console output
  displayDriftSummary(changes);
  
  // Markdown report
  const reportPath = `drift-reports/drift-${timestamp()}.md`;
  generateDriftReport(changes, currentState, baselineState, reportPath);
  console.log(`\nDrift report saved: ${reportPath}\n`);
  
  // Offer actions
  if (DRY_RUN_MODE) {
    console.log(`👀 Dry-run mode: No updates will be applied.\n`);
    return 3; // Manual review only
  }
  
  console.log(`What would you like to do?\n`);
  console.log(`1. Update all — Regenerate all affected /specs/ files from current Figma`);
  console.log(`2. Update selectively — Choose which components to update`);
  console.log(`3. Manual review only — Save report, no updates\n`);
  
  const choice = promptUser('Your choice (1/2/3): ');
  return parseInt(choice);
}

function displayDriftSummary(changes) {
  console.log(`\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━`);
  console.log(` DRIFT DETECTED: ${changes.stats.totalChanges} changes`);
  console.log(` (${changes.breaking.length} breaking, ${changes.enhancement.length} enhancements, ${changes.cosmetic.length} cosmetic)`);
  console.log(`━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━\n`);
  
  if (changes.breaking.length > 0) {
    console.log(`⚠️  BREAKING CHANGES (${changes.breaking.length}):`);
    for (const change of changes.breaking.slice(0, 3)) {
      console.log(`  • ${change.component}: ${change.type.replace(/_/g, ' ').toLowerCase()}`);
      console.log(`    → ${change.action}\n`);
    }
    if (changes.breaking.length > 3) {
      console.log(`  ... and ${changes.breaking.length - 3} more\n`);
    }
  }
  
  if (changes.enhancement.length > 0) {
    console.log(`✨ ENHANCEMENTS (${changes.enhancement.length}):`);
    for (const change of changes.enhancement.slice(0, 3)) {
      console.log(`  • ${change.component}: ${change.type.replace(/_/g, ' ').toLowerCase()}`);
      console.log(`    → ${change.action}\n`);
    }
    if (changes.enhancement.length > 3) {
      console.log(`  ... and ${changes.enhancement.length - 3} more\n`);
    }
  }
  
  if (changes.cosmetic.length > 0) {
    console.log(`🎨 COSMETIC CHANGES (${changes.cosmetic.length}):`);
    for (const change of changes.cosmetic.slice(0, 2)) {
      const desc = change.variable 
        ? `${change.variable}: ${change.before} → ${change.after}`
        : `${change.component}: ${change.type.replace(/_/g, ' ').toLowerCase()}`;
      console.log(`  • ${desc}`);
      console.log(`    → ${change.action}\n`);
    }
    if (changes.cosmetic.length > 2) {
      console.log(`  ... and ${changes.cosmetic.length - 2} more\n`);
    }
  }
  
  console.log(`Affected components: ${Array.from(changes.stats.affectedComponents).join(', ')}`);
}
```

### Phase 4-Sync: Execute

**Update /specs/ based on user choice**:

```javascript
async function runSyncPhase4(choice, changes, currentState) {
  if (choice === 3) {
    console.log(`\n📋 Manual review mode: No updates applied.\n`);
    console.log(`Review the drift report and update /specs/ manually.\n`);
    return;
  }
  
  let componentsToUpdate = [];
  
  if (choice === 1) {
    // Update all affected components
    componentsToUpdate = Array.from(changes.stats.affectedComponents);
    console.log(`\n🔄 Updating all affected components...\n`);
  } else if (choice === 2) {
    // Selective update
    console.log(`\nSelect components to update:\n`);
    const affectedComponents = Array.from(changes.stats.affectedComponents);
    affectedComponents.forEach((comp, i) => {
      console.log(`${i + 1}. ${comp}`);
    });
    console.log(`\nEnter numbers separated by commas (e.g., 1,3,5): `);
    const selections = promptUser('').split(',').map(s => parseInt(s.trim()) - 1);
    componentsToUpdate = selections.map(i => affectedComponents[i]);
  }
  
  // Regenerate /specs/ for selected components
  for (const componentName of componentsToUpdate) {
    console.log(`  ↻ Regenerating ${componentName}...`);
    await regenerateComponentSpec(componentName, currentState);
  }
  
  // Save new snapshot
  const snapshotPath = `.design-nexus/snapshots/${currentState.figma.fileKey}.json`;
  fs.writeFileSync(snapshotPath, JSON.stringify(currentState, null, 2));
  console.log(`\n💾 Saved new baseline snapshot: ${snapshotPath}\n`);
  
  // Commit changes
  await commitChanges(componentsToUpdate, snapshotPath);
  
  console.log(`✅ Sync complete!\n`);
  console.log(`Updated ${componentsToUpdate.length} component(s)`);
  console.log(`Snapshot saved and committed to git\n`);
}

async function regenerateComponentSpec(componentName, currentState) {
  // Reuse existing Phase 3 spec generation logic from Hardcore/Soft modes
  // Input: component data from currentState.components[componentName]
  // Output: /specs/atoms|molecules|organisms/[componentName].md
  
  const component = currentState.components[componentName];
  const category = categorizeComponent(component); // ATOM, MOLECULE, or ORGANISM
  const specPath = `specs/${category.toLowerCase()}s/${componentName}.md`;
  
  const spec = await generateComponentSpec(component, currentState.variables);
  fs.writeFileSync(specPath, spec);
}

async function commitChanges(componentsToUpdate, snapshotPath) {
  // Git add updated specs + snapshot
  await bash(`git add specs/ ${snapshotPath}`);
  
  // Commit message
  const message = `sync: update ${componentsToUpdate.join(', ')} from Figma

Detected drift and synced /specs/ with current Figma state.

Components updated: ${componentsToUpdate.length}
Snapshot: ${path.basename(snapshotPath)}

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>`;
  
  await bash(`git commit -m "${message}"`);
  console.log(`\n✅ Changes committed to git\n`);
}
```

---

## Error Handling

### Scenario 1: Figma API Failure

```javascript
try {
  const currentState = await extractCurrentState(fileKey, targetComponent);
} catch (error) {
  if (error.message.includes('timeout')) {
    console.error(`❌ Failed to extract Figma state: API timeout (30s exceeded)\n`);
    console.error(`Retrying (attempt 2/3)...\n`);
    // Retry with exponential backoff
  } else if (error.message.includes('Network error')) {
    console.error(`❌ Failed: Network error\n`);
    console.error(`Could not complete sync. Baseline snapshot preserved.\n`);
    console.error(`Try again later or check Figma file access permissions.\n`);
    process.exit(1);
  } else {
    throw error; // Unexpected error, bubble up
  }
}
```

### Scenario 2: Malformed Snapshot

```javascript
function loadBaseline(fileKey) {
  const snapshotPath = `.design-nexus/snapshots/${fileKey}.json`;
  
  if (!fs.existsSync(snapshotPath)) return null;
  
  try {
    const snapshot = JSON.parse(fs.readFileSync(snapshotPath, 'utf8'));
    
    // Validate version
    if (snapshot.version !== '3.1.0') {
      console.warn(`⚠️  Baseline snapshot has incompatible version\n`);
      console.warn(`Expected: 3.1.0`);
      console.warn(`Found: ${snapshot.version}\n`);
      console.warn(`Options:`);
      console.warn(`1. Delete snapshot and create new baseline`);
      console.warn(`2. Abort sync\n`);
      const choice = promptUser('Your choice (1/2): ');
      
      if (choice === '1') {
        fs.unlinkSync(snapshotPath);
        console.log(`🗑️  Deleted old snapshot\n`);
        return null;
      } else {
        process.exit(0);
      }
    }
    
    return snapshot;
  } catch (error) {
    console.error(`❌ Baseline snapshot is corrupted or invalid JSON\n`);
    console.error(`File: ${snapshotPath}\n`);
    console.error(`Error: ${error.message}\n`);
    console.warn(`Options:`);
    console.warn(`1. Delete snapshot and create new baseline`);
    console.warn(`2. Abort sync\n`);
    const choice = promptUser('Your choice (1/2): ');
    
    if (choice === '1') {
      fs.unlinkSync(snapshotPath);
      return null;
    } else {
      process.exit(0);
    }
  }
}
```

### Scenario 3: No /specs/ Directory (First Run)

```javascript
if (!baselineState) {
  // No snapshot and no /specs/
  console.log(`\n📸 No baseline snapshot found`);
  console.log(`📂 No /specs/ directory found\n`);
  console.log(`Creating baseline snapshot from current Figma state...\n`);
  
  const currentState = await extractCurrentState(fileKey, targetComponent);
  
  // Save as baseline
  const snapshotPath = `.design-nexus/snapshots/${fileKey}.json`;
  fs.mkdirSync(path.dirname(snapshotPath), { recursive: true });
  fs.writeFileSync(snapshotPath, JSON.stringify(currentState, null, 2));
  
  console.log(`💾 Saved baseline: ${snapshotPath}\n`);
  console.log(`✅ Baseline created\n`);
  console.log(`Run \`/design-nexus sync\` again to detect drift against this baseline.\n`);
  process.exit(0);
}
```

### Scenario 4: Component Not Found (Component-Specific Sync)

```javascript
if (TARGET_COMPONENT) {
  const component = currentState.components[TARGET_COMPONENT];
  if (!component) {
    console.error(`❌ Component "${TARGET_COMPONENT}" not found in Figma\n`);
    console.error(`Available components:\n`);
    for (const name of Object.keys(currentState.components)) {
      console.error(`  • ${name}`);
    }
    console.error(`\nCheck component name spelling or run system-wide sync.\n`);
    process.exit(1);
  }
}
```

---

## Testing Strategy

### Test Suite

**Test 1: shadcn/ui Button (Baseline)**
- Run Soft Mode → generate /specs/
- Snapshot created automatically
- Modify Figma: Add variant "loading"
- Run `/design-nexus sync` → detect enhancement
- Verify drift report accuracy (1 ENHANCEMENT change)

**Test 2: Ant Design Button (No Variables)**
- Run Soft Mode → generate /specs/
- Snapshot created (no variable bindings)
- Modify Figma: Change hardcoded color #0369A1 → #0284C7
- Run `/design-nexus sync` → detect cosmetic change
- Verify hardcoded color tracking works

**Test 3: Material Design Button (Multi-Component Set)**
- Run Soft Mode on Button (filled)
- Snapshot created (1 of 5 component sets)
- Run `/design-nexus sync --component "Button - text"` → detect new component
- Verify component-specific sync works

**Test 4: Chakra UI Button (INSTANCE_SWAP Properties)**
- Run Soft Mode → generate /specs/
- Snapshot created (icon property type: INSTANCE_SWAP)
- Modify Figma: Change icon property type to BOOLEAN
- Run `/design-nexus sync` → detect breaking change
- Verify property type change detection

**Test 5: First Run (No Baseline, No /specs/)**
- Fresh project, no prior work
- Run `/design-nexus sync` → create baseline
- Verify snapshot saved to `.design-nexus/snapshots/`
- Run again → "NO DRIFT DETECTED"

**Test 6: First Run (No Baseline, Has /specs/)**
- Existing project with /specs/ (from Hardcore mode)
- No snapshot yet
- Run `/design-nexus sync` → use /specs/ as baseline
- Modify Figma → detect drift immediately

**Test 7: Update All (Option 1)**
- Detect drift (3 components changed)
- Choose option 1 (Update all)
- Verify all 3 /specs/ files regenerated
- Verify snapshot updated
- Verify git commit created

**Test 8: Update Selectively (Option 2)**
- Detect drift (3 components changed)
- Choose option 2 (Update selectively)
- Select components 1 and 3
- Verify only 2 /specs/ files regenerated
- Verify snapshot updated

**Test 9: Manual Review Only (Option 3)**
- Detect drift
- Choose option 3 (Manual review)
- Verify drift report saved to `drift-reports/`
- Verify no /specs/ files modified
- Verify no git commit created

**Test 10: Dry-Run Mode**
- Run `/design-nexus sync --dry-run`
- Verify drift report generated
- Verify no /specs/ files modified
- Verify no snapshot created
- Verify no git commit

### Edge Cases

**Edge Case 1: Component Renamed**
- Detected as: COMPONENT_REMOVED + COMPONENT_ADDED
- Drift report shows both changes
- User can manually merge if same nodeId

**Edge Case 2: Snapshot Outdated (Concurrent Modification)**
- User A runs sync, gets baseline timestamp T1
- User B modifies Figma at T2 (after T1)
- User A's sync completes at T3, saves snapshot with T3 timestamp
- User B's changes are in the new snapshot (correct behavior)
- Race condition is acceptable (last write wins)

**Edge Case 3: Large Design System (100+ Components)**
- Performance test: Extract 100 components
- Target: <30 seconds for full extraction
- Target: <5 seconds for diff detection
- If too slow, add progress bar + optimize hash comparison

**Edge Case 4: Empty /specs/ Directory**
- /specs/ exists but is empty (no component files)
- Treat as "no baseline" (can't construct baseline from empty directory)
- Create new baseline snapshot

**Edge Case 5: Mixed Drift (Breaking + Enhancement)**
- Breaking changes take priority in report
- User sees breaking changes first (top of console output)
- Recommendations emphasize breaking changes (notify team, document, test)

---

## Success Criteria

### Functional Requirements

✅ **Drift Detection Accuracy**
- Detects 100% of component additions/removals
- Detects 100% of variant additions/removals
- Detects 100% of property additions/removals
- Detects 100% of token binding changes
- Detects 100% of token value changes
- **0% false positives** (no spurious drift reports)

✅ **Snapshot Management**
- Snapshots are created correctly (valid JSON structure)
- Snapshots are committable (small, diffable, ~10-50KB)
- Snapshots persist across sessions
- Snapshots load correctly on subsequent runs

✅ **User Actions**
- Option 1 (Update all) regenerates all affected /specs/ files
- Option 2 (Update selectively) updates only chosen components
- Option 3 (Manual review) saves report, makes no changes
- Dry-run mode generates report only, no modifications

✅ **Error Handling**
- Gracefully handles missing baseline (creates one)
- Gracefully handles missing /specs/ (fallback to baseline creation)
- Gracefully handles Figma API failures (retry + clear error messages)
- Gracefully handles malformed snapshots (offer to delete + recreate)

✅ **Git Integration**
- Snapshots are committed with descriptive messages
- /specs/ updates are committed with drift summary
- Commits reference affected components

### Performance Requirements

✅ **Speed**
- Single component sync: <10 seconds
- System-wide sync (10 components): <30 seconds
- System-wide sync (100 components): <60 seconds
- Diff detection: <5 seconds (hash-based quick check)

✅ **Resource Usage**
- Snapshot size: <50KB per file
- Memory usage: <500MB during extraction
- No disk space leaks (one snapshot per file, no historical)

### Quality Requirements

✅ **Reliability**
- Works on all 4 tested design systems (shadcn/ui, Ant Design, Chakra UI, Material Design)
- Handles edge cases (renamed components, concurrent modifications, empty /specs/)
- No data loss (snapshots are atomic writes, failures don't corrupt baseline)

✅ **Usability**
- Clear console output (color-coded, categorized changes)
- Actionable drift reports (markdown format, includes migration guides)
- Tiered options (user has control over updates)
- Dry-run mode for safe exploration

✅ **Maintainability**
- Self-contained in SKILL.md (no external dependencies)
- Modular functions (easy to test and extend)
- Clear separation of concerns (extract → diff → report → execute)

---

## Future Enhancements (Post-v3.1)

**v3.2: Historical Snapshots**
- Keep multiple snapshots per file (`.design-nexus/snapshots/[fileKey]-[timestamp].json`)
- Time-travel debugging: "Show me what changed between v1.0 and v2.0"
- Snapshot retention policy: Keep last 10 snapshots, auto-delete older ones

**v3.3: Scheduled Sync**
- Integrate with Claude Code's scheduling system
- Daily/weekly automated sync checks
- Slack/Discord notifications when drift detected

**v3.4: Code Scanning**
- Third data source: actual codebase
- 3-way diff: Figma ↔ /specs/ ↔ Code
- Detect implementation drift (code doesn't match /specs/)

**v3.5: Auto-PR Generation**
- Automatically create GitHub PR when drift detected
- PR description includes drift report
- Tag designers for review

**v3.6: Analytics Dashboard**
- Track drift frequency per component
- Identify components with high churn (frequent changes)
- Suggest consolidation or deprecation

---

## Appendix: File Changes

### Modified Files

**`.claude/skills/design-nexus/SKILL.md`**
- Add MODE = 'SYNC' detection to Phase 0
- Add Phase 1-Sync, Phase 2-Sync, Phase 3-Sync, Phase 4-Sync workflows
- Add snapshot load/save functions
- Add diff detection algorithm
- Add drift report generation
- Add selective update logic

### New Files

**`.design-nexus/snapshots/[fileKey].json`** (per project, gitignored by default but recommended to commit)
- Baseline snapshot for drift detection

**`drift-reports/drift-[timestamp].md`** (per sync run, gitignored)
- Human-readable drift report

**`.gitignore`** (append)
```
drift-reports/
```

**`.design-nexus/.gitignore`** (create)
```
# Uncomment to exclude snapshots from git (not recommended)
# snapshots/
```

---

## Sign-Off

**Design approved by**: User  
**Date**: 2026-04-27  
**Next step**: Create implementation plan (invoke writing-plans skill)  

**Estimated implementation time**: 6-8 hours
- Phase 0 integration: 1 hour
- Phase 1-Sync (extraction + snapshot load): 2 hours
- Phase 2-Sync (diff detection): 2 hours
- Phase 3-Sync (report generation): 1 hour
- Phase 4-Sync (update execution): 1 hour
- Testing + debugging: 1 hour

**Target delivery**: v3.1.0 release within 2 weeks

---

*Generated by brainstorming skill for design-nexus v3.1 Sync Mode*
