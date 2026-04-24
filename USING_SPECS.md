# Using /specs/ — Where the Generated Files Matter

After running design-nexus, you get a `/specs/` directory. This guide shows you **where and how to use these files** in your workflow.

---

## Who Uses /specs/?

### 👩‍🎨 **Designers**
Use /specs/ to:
- See current compliance score before designing new components
- Check which conventions to follow (prop naming, variant naming)
- Understand why certain tokens exist (`use` and `reason` fields)
- Validate designs against documented standards before handoff

**Key files**:
- `conventions.md` — What rules to follow
- `design-decisions.md` — Why the system works this way
- `tokens/*.yml` — Which colors/spacing/etc. to use

---

### 👨‍💻 **Developers**
Use /specs/ to:
- Implement components without asking "what's this variant called?"
- Map Figma variants to code props (see "Figma Component Map" tables)
- Know which tokens to reference in code (semantic vs primitive)
- Validate code against design system contract during code review

**Key files**:
- `components/*.md` — Component contracts (props, variants, states)
- `tokens/*.yml` — Token definitions (import these values)
- `exports/style-dictionary.json` — Machine-readable tokens for build systems

---

### 🤖 **AI Agents (Claude, GPT, etc.)**
Use /specs/ as context when:
- Generating component code from Figma designs
- Answering "how should I implement X?" questions
- Validating generated code against design system standards
- Suggesting refactors ("this uses hardcoded #0369A1, should use {cta}")

**Key files**:
- ALL `.md` and `.yml` files (structured, parseable context)
- `exports/style-dictionary.json` — Token values for code generation

---

## Where to Use /specs/ Files

### 1. **In Your Codebase** (Committed to Git)

```bash
your-project/
├── src/
│   └── components/
│       └── ui/
│           ├── button.tsx          # Implementation
│           └── button.spec.md      # ← Symlink to /specs/components/Button.md
├── design/
│   └── specs/                      # ← design-nexus output
│       ├── README.md
│       ├── conventions.md
│       ├── tokens/
│       └── components/
└── tailwind.config.js              # ← Imports from /specs/tokens/
```

**Why**: /specs/ becomes the source of truth for both design and code.

**How**:
1. Run design-nexus: `"Audit design system: [Figma URL]"`
2. Save output to `./design/specs/`
3. Commit to git: `git add design/specs/`
4. Reference in code reviews: "Does this match `/design/specs/components/Button.md`?"

---

### 2. **In Tailwind Config** (Token Import)

```javascript
// tailwind.config.js
const fs = require('fs');
const yaml = require('yaml');

// Import semantic tokens from /specs/
const colorsYaml = fs.readFileSync('./design/specs/tokens/colors.yml', 'utf8');
const tokens = yaml.parse(colorsYaml);

module.exports = {
  theme: {
    extend: {
      colors: {
        // Map semantic tokens to Tailwind colors
        primary: tokens.semantic.primary.value,
        secondary: tokens.semantic.secondary.value,
        cta: tokens.semantic.cta.value,
        destructive: tokens.semantic.destructive.value,
        // ... etc
      }
    }
  }
}
```

**Why**: Single source of truth — changing `/specs/tokens/colors.yml` updates entire codebase.

---

### 3. **In Style Dictionary** (Multi-Platform Tokens)

```json
// style-dictionary.config.json
{
  "source": ["design/specs/exports/style-dictionary.json"],
  "platforms": {
    "css": {
      "transformGroup": "css",
      "buildPath": "src/styles/",
      "files": [{
        "destination": "tokens.css",
        "format": "css/variables"
      }]
    },
    "ios": {
      "transformGroup": "ios",
      "buildPath": "ios/tokens/",
      "files": [{
        "destination": "Colors.swift",
        "format": "ios-swift/class.swift"
      }]
    },
    "android": {
      "transformGroup": "android",
      "buildPath": "android/tokens/",
      "files": [{
        "destination": "colors.xml",
        "format": "android/colors"
      }]
    }
  }
}
```

**Run**: `style-dictionary build`

**Output**:
- `src/styles/tokens.css` — CSS custom properties
- `ios/tokens/Colors.swift` — iOS color constants
- `android/tokens/colors.xml` — Android color resources

**Why**: One token source (`/specs/exports/style-dictionary.json`) → all platforms.

---

### 4. **In Documentation Sites** (Storybook, Docusaurus)

```markdown
<!-- docs/components/button.mdx -->
import ButtonSpec from '../design/specs/components/Button.md';

# Button Component

<ButtonSpec />

## Usage in Code

Based on the spec above, implement like this:

\`\`\`tsx
<Button variant="primary" size="default">Click me</Button>
\`\`\`

See `/design/specs/components/Button.md` for full prop definitions.
```

**Why**: Design specs and code docs stay in sync (both pull from /specs/).

---

### 5. **In CI/CD Validation** (Automated Compliance Checks)

```yaml
# .github/workflows/design-system-lint.yml
name: Design System Compliance

on: [pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Check /specs/ exists
        run: test -d design/specs || exit 1
      
      - name: Validate component props match spec
        run: |
          # Parse Button.md, extract expected props
          # Scan src/components/ui/button.tsx
          # Compare: does code match spec?
          node scripts/validate-component-props.js
      
      - name: Validate token usage
        run: |
          # Scan codebase for hardcoded colors
          # Flag any #0369A1 that should use {cta} token
          node scripts/validate-token-usage.js
```

**Why**: Prevent drift — code changes that violate /specs/ fail CI.

---

### 6. **In Code Generators** (Automate Boilerplate)

```bash
# Generate shadcn/ui component from /specs/
npx design-nexus-codegen \
  --spec design/specs/components/Button.md \
  --output src/components/ui/button.tsx \
  --framework react
```

**Output**:
```tsx
// Auto-generated from /specs/components/Button.md
import { cva, type VariantProps } from "class-variance-authority"

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-xl font-700",
  {
    variants: {
      variant: {
        primary: "bg-cta text-white hover:bg-cta-hover",
        secondary: "border-2 border-border",
        destructive: "bg-destructive text-white",
        // ... from spec
      },
      size: {
        sm: "h-10 px-5 text-sm",
        default: "h-11 px-7 text-lg",
        lg: "h-12 px-8 text-xl",
      }
    }
  }
)

export const Button = ...
```

**Why**: Implementation matches spec 100% (machine-generated from /specs/).

---

### 7. **In Design Reviews** (Figma Comments)

When reviewing a Figma design:

```
Designer: "Added a new 'subtle' variant to Button"

Developer: "Per /specs/conventions.md, we use 'secondary' not 'subtle' for 
           shadcn/ui compliance. Can you rename it?"

Designer: *checks /specs/conventions.md*
          "Oh you're right! Updated to 'secondary'. Re-review please."
```

**Why**: /specs/ is the shared reference — no more "I thought we decided X" arguments.

---

### 8. **In Onboarding** (New Team Members)

```markdown
# Welcome to the Team!

To understand our design system:

1. Read `/design/specs/README.md` — high-level overview
2. Read `/design/specs/design-decisions.md` — learn the WHY
3. Read `/design/specs/conventions.md` — learn the rules
4. Browse `/design/specs/components/*.md` — see component contracts
5. Reference `/design/specs/tokens/*.yml` when implementing

Questions? All answers are in /specs/ — read first, then ask.
```

**Why**: /specs/ documents the system knowledge that would otherwise live in people's heads.

---

### 9. **In Design System Migrations** (Refactoring)

```bash
# Before migration to shadcn/ui:
$ grep -r "type=" src/components/
src/components/Button.tsx:  type?: "filled" | "outline"

# Check /specs/conventions.md → "prop should be 'variant', values should be 'primary' | 'secondary'"

# After migration:
$ grep -r "variant=" src/components/
src/components/Button.tsx:  variant?: "primary" | "secondary"

# Validate against /specs/:
$ node scripts/validate-migration.js
✅ All components match /specs/ conventions
```

**Why**: /specs/ is the migration target — defines what "done" looks like.

---

### 10. **In design-nexus Sync Mode** (v3.0 Drift Detection)

```bash
# Generate baseline /specs/
$ claude "Audit design system: https://figma.com/..."
# → writes to /design/specs/

# 2 weeks later, designer updates Figma...

# Run Sync Mode (v3.0)
$ cd design-nexus-workspace/v3.0-sync-mode
$ node sync-analyzer.js

Output:
  ⚠️ DRIFT DETECTED
  - Button: Added 'loading' variant in Figma (not in /specs/)
  - Input: Removed 'error' variant in Figma (still in /specs/)
  
  Recommendations:
  1. Update /specs/ from Figma (if Figma is source of truth)
  2. Restore variants in Figma (if /specs/ is source of truth)
```

**Why**: /specs/ is the baseline — Sync Mode detects when Figma diverges.

---

## Best Practices

### ✅ DO:
- **Commit /specs/ to git** — it's source code, not generated temp files
- **Update /specs/ regularly** — re-run design-nexus after Figma changes
- **Reference /specs/ in PRs** — "This implements /specs/components/Button.md"
- **Validate code against /specs/** — CI checks that code matches spec
- **Link /specs/ in Storybook** — show spec alongside component docs

### ❌ DON'T:
- **Don't edit /specs/ manually** — regenerate from Figma instead
- **Don't ignore compliance warnings** — fix violations or update Figma
- **Don't skip /specs/design-decisions.md** — the WHY is as important as the WHAT
- **Don't commit /specs/exports/** — it's generated, belongs in `.gitignore`

---

## File-Specific Use Cases

| File | Use By | For What |
|------|--------|----------|
| `README.md` | Everyone | Quick reference, getting started |
| `design-decisions.md` | Designers, PMs | Understanding WHY choices were made |
| `conventions.md` | Developers | Knowing what rules to follow |
| `tokens/*.yml` | Developers | Importing token values into code |
| `components/*.md` | Developers | Implementing components correctly |
| `exports/style-dictionary.json` | Build systems | Generating platform-specific tokens |

---

## Integration Examples

### React + Tailwind
```tsx
// src/components/ui/button.tsx
// Implements /specs/components/Button.md

import { cva } from "class-variance-authority"

const buttonVariants = cva(
  "rounded-xl font-700 transition-colors",
  {
    variants: {
      // From /specs/components/Button.md → Props → variant table
      variant: {
        primary: "bg-cta text-white hover:bg-cta-hover",     // Tokens from /specs/tokens/colors.yml
        secondary: "border-2 border-border text-secondary",
        destructive: "bg-destructive text-white",
      },
      // From /specs/components/Button.md → Props → size table
      size: {
        sm: "h-10 px-5 text-sm",
        default: "h-11 px-7 text-lg",
        lg: "h-12 px-8 text-xl",
      }
    }
  }
)
```

### Vue + SCSS
```scss
// src/styles/tokens.scss
// Generated from /specs/tokens/colors.yml

$primary: #0F172A;     // From semantic.primary
$cta: #0369A1;         // From semantic.cta
$destructive: #EF4444; // From semantic.destructive
```

### Vanilla JS + CSS
```css
/* public/styles/design-tokens.css */
/* Generated from /specs/exports/style-dictionary.json via Style Dictionary */

:root {
  --color-primary: #0F172A;
  --color-cta: #0369A1;
  --color-destructive: #EF4444;
  --spacing-4: 1rem;
  --radius-lg: 0.75rem;
}
```

---

## Summary

**The /specs/ directory is not just documentation — it's infrastructure.**

Use it to:
1. **Implement** components correctly (developers)
2. **Design** new features consistently (designers)
3. **Validate** compliance automatically (CI/CD)
4. **Generate** code from specs (build tools)
5. **Onboard** new team members (documentation)
6. **Migrate** legacy systems (refactoring guide)
7. **Detect drift** over time (Sync Mode v3.0)

**The goal**: Figma, /specs/, and code all stay in sync — /specs/ is the contract that binds them.

---

*Generated by design-nexus v3.0*
