# Button

**Category**: Atom  
**Component Type**: Interactive  
**Figma Node ID**: `1:85`  
**Complexity**: Low (0-1 child types: TEXT, INSTANCE, VECTOR)

---

## 1. Overview

### When to Use

Use the Button component when you need to:
- Trigger an action or event (submit form, open dialog, navigate)
- Provide clear call-to-action in the interface
- Offer users a way to complete a task or make a decision
- Create clickable interactive elements with consistent styling

**Specific use cases:**
- Form submissions ("Submit", "Save Changes", "Continue")
- Navigation ("Go Back", "Next Step", "Learn More")
- Destructive actions ("Delete", "Remove", "Cancel Subscription")
- Secondary actions ("Cancel", "Skip", "Not Now")
- Icon-only actions (close, menu toggle, expand/collapse)

### When NOT to Use

**Don't use Button when:**
- **Navigating between pages** → Use `Link` component instead (preserves browser navigation, better SEO)
- **Toggling state on/off** → Use `Switch` or `Toggle` component (clearer affordance for binary state)
- **Selecting from multiple options** → Use `Radio` or `Checkbox` components (better for multiple choices)
- **Opening a menu** → Use `DropdownMenu` or `Select` component (provides menu-specific interaction patterns)
- **Non-interactive visual elements** → Use `Badge` or `Tag` components (avoid misleading clickable appearance)

### Alternatives

| Use Case | Use This Instead | Why |
|---|---|---|
| Navigate to another page | `Link` component | Preserves browser history, right-click context menu, better SEO |
| Toggle on/off state | `Switch` component | Clear affordance for binary state, better accessibility |
| Select single option from list | `Radio` component | Correct semantic HTML, keyboard navigation |
| Select multiple options | `Checkbox` component | Allows multiple selections, clearer interaction model |
| Open dropdown menu | `DropdownMenu` component | Provides menu-specific keyboard navigation and ARIA attributes |
| Display status or label | `Badge` component | Non-interactive, doesn't mislead users into thinking it's clickable |

### Related Components

**Composition patterns:**
- **Button + Icon**: Use `Icon` component inside Button (see "with icon" variant)
- **Button + Dropdown**: Combine with `DropdownMenu` for split buttons
- **Button + Tooltip**: Wrap with `Tooltip` for icon-only buttons (provide accessible label)
- **Button + Badge**: Show notification count or status indicator on button
- **ButtonGroup**: Multiple buttons as a logical group (segmented control pattern)

**Used within:**
- `Dialog` — Primary/secondary actions in footer
- `Card` — Call-to-action in card footer
- `Form` — Submit/cancel actions
- `Modal` — Confirm/dismiss actions
- `AlertDialog` — Destructive action confirmation

---

## 2. Design Decision

### WHY This Component Exists

The Button component exists to provide **consistent, accessible, and predictable interactive triggers** across the entire application.

**Core problems solved:**
1. **Visual consistency** — All clickable actions look and behave the same way
2. **Accessibility** — Keyboard navigation, screen reader support, focus management built-in
3. **Hierarchy** — Visual weight matches action importance (primary > secondary > destructive > ghost)
4. **Cognitive load** — Users don't have to guess which elements are clickable
5. **Maintainability** — Single source of truth for button styling reduces code duplication

**Design system role:**
- **Foundation for interactive patterns** — Many components compose Button internally (Dialog, Modal, etc.)
- **Enforces visual hierarchy** — Variant system prevents arbitrary styling
- **Accessibility baseline** — Ensures all interactive elements meet WCAG 2.2 AA standards

**Historical context:**
This is a shadcn/ui-inspired Button component with **extended variant coverage** including icon-only and loading states that go beyond the base shadcn implementation.

---

## 3. Anatomy

### Constituent Parts

```
┌─────────────────────────────────────┐
│  Button Container                   │
│  ┌───────────────────────────────┐  │
│  │ [Icon] Button Text            │  │ ← Optional leading icon
│  └───────────────────────────────┘  │
│  ↑       ↑                           │
│  8px     Text content                │
│  padding                             │
└─────────────────────────────────────┘
  ↑
  Corner radius: 6px
```

**1. Container** (Frame/Component)
- Background fill (variant-dependent)
- Border stroke (outline variant only)
- Corner radius (6px standard, 96px for circle variant)
- Padding (horizontal: 16px, vertical: 8px; adjusted for icon-only)
- Auto-layout (horizontal, space-between, center alignment)

**2. Text Label** (TEXT node)
- Font family: (inherited from design system)
- Font size: (variant-dependent)
- Font weight: (typically semi-bold or bold)
- Color: Contrasts with background (WCAG AA compliant)
- **Accessibility**: Always visible to screen readers

**3. Icon** (INSTANCE/VECTOR, optional)
- Size: 16×16px or 20×20px (variant-dependent)
- Color: Matches text color
- Position: Leading (before text) or standalone (icon-only variants)
- **Accessibility**: Decorative (`aria-hidden="true"`) when text is present

**4. Loading Indicator** (GROUP/ELLIPSE, loading variant only)
- Spinner animation
- Replaces or overlays text/icon
- Color: Matches text color at 50% opacity
- **Accessibility**: Announced as "loading" to screen readers

### Visual Hierarchy

**Spacing:**
- Horizontal padding: 16px (standard), 8px (icon-only), 12px (circle icon-only)
- Vertical padding: 8px (standard), consistent across all heights
- Icon-to-text gap: 8px (when icon + text present)

**Typography:**
- Text alignment: Center
- Line height: Auto (single-line buttons)
- Text color: High contrast with background (4.5:1 minimum)

**Color Relationships:**
- Primary: Dark background (#0F172A), white text
- Destructive: Red background (#EF4444), white text
- Outline: Transparent background, border (#E2E8F0), dark text
- Ghost: Transparent background, dark text (hover adds background)
- Link: Transparent background, underlined text (hover only)

---

## 4. Props/API

### Component Properties

| Property | Type | Options | Default | Description |
|---|---|---|---|---|
| `variant` | VARIANT | `default`, `primary`, `destructive`, `outline`, `subtle`, `ghost`, `link`, `with icon`, `just icon`, `just icon circle`, `loading` | `default` | Visual style and semantic meaning of the button |
| `state` | VARIANT | `Default`, `hover` | `Default` | Interaction state (controlled in Figma, managed via CSS in code) |

### Variant Definitions

#### Standard Variants

**`default` (primary action)**
- Background: `#0F172A` (dark slate)
- Text: White
- Hover: `#334155` (lighter slate)
- **Use for**: Primary call-to-action, most important action on the page
- **Example**: "Submit", "Continue", "Save Changes"

**`primary`** (alias for default)
- Identical to `default`
- **Convention note**: shadcn/ui uses "default" as the variant name, but this design system includes "primary" as an explicit variant for clarity

**`destructive` (dangerous action)**
- Background: `#EF4444` (red-500)
- Text: White
- Hover: `#DC2626` (red-600)
- **Use for**: Irreversible or dangerous actions that require user attention
- **Example**: "Delete Account", "Remove Item", "Cancel Subscription"
- **Best practice**: Always pair with confirmation dialog

**`outline` (secondary action)**
- Background: White/transparent
- Border: `#E2E8F0` (slate-200)
- Text: Dark slate
- Hover: `#F1F5F9` background (slate-100)
- **Use for**: Secondary actions, less important than primary but still relevant
- **Example**: "Cancel", "Go Back", "Skip"

**`subtle`** (low-emphasis action)
- Background: `#F1F5F9` (slate-100)
- Text: Dark slate
- Hover: `#E2E8F0` (slate-200)
- **Use for**: Tertiary actions, minimal visual weight
- **Example**: "View Details", "Learn More", "Settings"
- **⚠️ Convention violation**: shadcn/ui uses "secondary" instead of "subtle" — recommend renaming

**`ghost` (minimal action)**
- Background: Transparent
- Text: Dark slate
- Hover: `#F1F5F9` background (slate-100)
- **Use for**: Actions that should be discoverable but not prominent
- **Example**: Icon buttons in toolbars, "Dismiss", "Close"

**`link` (text link styled as button)**
- Background: Transparent (no hover background)
- Text: Dark slate, underlined on hover
- **Use for**: Button that should behave like a text link
- **Example**: "Read Documentation", "Terms of Service"
- **⚠️ Best practice**: Consider using actual `Link` component for navigation

#### Icon Variants

**`with icon` (text + leading icon)**
- Layout: Icon (16×16px) + 8px gap + Text
- Padding: Same as default (16px horizontal)
- **Use for**: Actions where icon provides additional context or recognition
- **Example**: "Download Report" (with download icon), "Share" (with share icon)
- **⚠️ Convention violation**: Should use composition (`<Button><Icon />Text</Button>`) instead of dedicated variant

**`just icon` (icon only, square)**
- Size: 32×32px total (8px padding + 16×16px icon)
- Background: White
- Border: `#E2E8F0` (slate-200)
- **Use for**: Space-constrained interfaces, icon-only actions
- **Example**: Close button, menu toggle, expand/collapse
- **⚠️ Accessibility**: Must include `aria-label` for screen readers
- **⚠️ Touch target violation**: 32×32px is below 44×44px minimum — needs resize to 44×44px

**`just icon circle` (icon only, circular)**
- Size: 40×40px total (12px padding + 16×16px icon)
- Corner radius: 96px (fully circular)
- Background: White
- Border: `#E2E8F0` (slate-200)
- **Use for**: Floating action buttons, prominent icon-only actions
- **Example**: "Add" button, "Upload" button
- **⚠️ Accessibility**: Must include `aria-label` for screen readers
- **⚠️ Touch target violation**: 40×40px is below 44×44px minimum — needs resize to 44×44px

#### State Variants

**`loading` (async action in progress)**
- Background: Default background at 50% opacity
- Content: Spinner animation (replaces text/icon)
- **Use for**: Async operations (API calls, file uploads, processing)
- **Example**: Form submission, data loading, file upload
- **⚠️ Convention violation**: Should be a boolean prop (`loading={true}`) not a variant value
- **⚠️ Accessibility**: Must announce "loading" to screen readers, disable button interaction

### States

| State | Visual Change | Use Case |
|---|---|---|
| `Default` | Base appearance | Resting state, ready for interaction |
| `hover` | Background color change, subtle elevation | Mouse cursor over button |
| **focus** (missing) | Focus ring, outline | Keyboard navigation focus |
| **active** (missing) | Pressed appearance, darker background | Mouse button down |
| **disabled** (missing) | Reduced opacity, no pointer cursor | Action not available |

**⚠️ Missing states:**
- **Focus state**: Required for keyboard navigation and WCAG 2.2 AA compliance
- **Active state**: Provides tactile feedback for click/tap interaction
- **Disabled state**: Visual indicator when button is not interactive

---

## 5. Token Bindings

### Current Implementation (Hardcoded Values)

**⚠️ CRITICAL VIOLATION**: This component uses **0 Figma variables** — all colors are hardcoded.

**Hardcoded colors found (25 total):**

| Variant | Property | Hardcoded Value | Should Use |
|---|---|---|---|
| `default` | Background | `#0F172A` | `{color/semantic/button-primary-bg}` → `{color/primitive/slate-900}` |
| `default` hover | Background | `#334155` | `{color/semantic/button-primary-bg-hover}` → `{color/primitive/slate-700}` |
| `destructive` | Background | `#EF4444` | `{color/semantic/button-destructive-bg}` → `{color/primitive/red-500}` |
| `destructive` hover | Background | `#DC2626` | `{color/semantic/button-destructive-bg-hover}` → `{color/primitive/red-600}` |
| `outline` | Background | `#FFFFFF` | `{color/semantic/surface}` → `{color/primitive/white}` |
| `outline` | Border | `#E2E8F0` | `{color/semantic/border}` → `{color/primitive/slate-200}` |
| `outline` hover | Background | `#F1F5F9` | `{color/semantic/surface-hover}` → `{color/primitive/slate-100}` |
| `subtle` | Background | `#F1F5F9` | `{color/semantic/button-secondary-bg}` → `{color/primitive/slate-100}` |
| `subtle` hover | Background | `#E2E8F0` | `{color/semantic/button-secondary-bg-hover}` → `{color/primitive/slate-200}` |
| `ghost` hover | Background | `#F1F5F9` | `{color/semantic/surface-hover}` → `{color/primitive/slate-100}` |

### Recommended Token Architecture (3-Layer)

**Layer 1: Primitive tokens (--ds-*)**
```css
--ds-slate-900: #0F172A;
--ds-slate-700: #334155;
--ds-slate-200: #E2E8F0;
--ds-slate-100: #F1F5F9;
--ds-red-500: #EF4444;
--ds-red-600: #DC2626;
--ds-white: #FFFFFF;
```

**Layer 2: Semantic tokens (--*)**
```css
--color-button-primary-bg: var(--ds-slate-900, #0F172A);
--color-button-primary-bg-hover: var(--ds-slate-700, #334155);
--color-button-primary-text: var(--ds-white, #FFFFFF);
--color-button-destructive-bg: var(--ds-red-500, #EF4444);
--color-button-destructive-bg-hover: var(--ds-red-600, #DC2626);
--color-button-destructive-text: var(--ds-white, #FFFFFF);
--color-button-secondary-bg: var(--ds-slate-100, #F1F5F9);
--color-button-secondary-bg-hover: var(--ds-slate-200, #E2E8F0);
--color-button-secondary-text: var(--ds-slate-900, #0F172A);
--color-border: var(--ds-slate-200, #E2E8F0);
--color-surface: var(--ds-white, #FFFFFF);
```

**Layer 3: Component usage (references Layer 2 only)**
```tsx
// Component CSS references semantic tokens, never primitives
<button className="bg-button-primary-bg hover:bg-button-primary-bg-hover text-button-primary-text">
```

### Non-Color Token Bindings

| Property | Value | Token (Recommended) |
|---|---|---|
| Corner radius | 6px | `{radius/md}` or `{spacing/1.5}` |
| Corner radius (circle) | 96px | `{radius/full}` |
| Padding horizontal | 16px | `{spacing/4}` |
| Padding horizontal (icon-only) | 8px | `{spacing/2}` |
| Padding horizontal (circle) | 12px | `{spacing/3}` |
| Padding vertical | 8px | `{spacing/2}` |
| Icon size | 16×16px | `{sizing/icon-sm}` |
| Icon size (large) | 20×20px | `{sizing/icon-md}` |
| Icon-to-text gap | 8px | `{spacing/2}` |

**⚠️ Missing token categories:**
- Typography tokens (font-family, font-size, font-weight, line-height)
- Spacing tokens (padding, gap values)
- Radius tokens (corner radius values)
- Shadow tokens (elevation/depth, if applicable)

---

## 6. Code Examples

### React + Tailwind CSS

```tsx
import { ButtonHTMLAttributes, forwardRef } from 'react'
import { cva, type VariantProps } from 'class-variance-authority'
import { cn } from '@/lib/utils'

const buttonVariants = cva(
  // Base styles
  "inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none",
  {
    variants: {
      variant: {
        default: "bg-button-primary-bg text-button-primary-text hover:bg-button-primary-bg-hover",
        destructive: "bg-button-destructive-bg text-button-destructive-text hover:bg-button-destructive-bg-hover",
        outline: "border border-border bg-surface hover:bg-surface-hover text-secondary",
        secondary: "bg-button-secondary-bg text-button-secondary-text hover:bg-button-secondary-bg-hover",
        ghost: "hover:bg-surface-hover text-secondary",
        link: "underline-offset-4 hover:underline text-secondary",
      },
      size: {
        default: "h-10 py-2 px-4",
        sm: "h-9 px-3 rounded-md",
        lg: "h-11 px-8 rounded-md",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)

export interface ButtonProps
  extends ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean
  loading?: boolean
}

const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, asChild = false, loading = false, children, disabled, ...props }, ref) => {
    return (
      <button
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        disabled={disabled || loading}
        aria-disabled={disabled || loading}
        aria-busy={loading}
        {...props}
      >
        {loading ? (
          <span className="flex items-center gap-2">
            <svg className="animate-spin h-4 w-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle>
              <path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            {children}
          </span>
        ) : (
          children
        )}
      </button>
    )
  }
)
Button.displayName = "Button"

export { Button, buttonVariants }
```

**Usage:**
```tsx
import { Button } from '@/components/ui/button'
import { Download } from 'lucide-react'

// Primary action
<Button>Submit</Button>

// Destructive action with confirmation
<Button variant="destructive" onClick={() => confirm('Delete?')}>
  Delete Account
</Button>

// Secondary action
<Button variant="outline">Cancel</Button>

// With icon (composition pattern)
<Button>
  <Download className="mr-2 h-4 w-4" />
  Download Report
</Button>

// Icon only
<Button size="icon" aria-label="Close">
  <X className="h-4 w-4" />
</Button>

// Loading state
<Button loading>Processing...</Button>
```

### Vue 3 + Tailwind CSS

```vue
<script setup lang="ts">
import { computed } from 'vue'

interface ButtonProps {
  variant?: 'default' | 'destructive' | 'outline' | 'secondary' | 'ghost' | 'link'
  size?: 'default' | 'sm' | 'lg' | 'icon'
  loading?: boolean
  disabled?: boolean
}

const props = withDefaults(defineProps<ButtonProps>(), {
  variant: 'default',
  size: 'default',
  loading: false,
  disabled: false,
})

const buttonClass = computed(() => {
  const base = 'inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none'
  
  const variants = {
    default: 'bg-button-primary-bg text-button-primary-text hover:bg-button-primary-bg-hover',
    destructive: 'bg-button-destructive-bg text-button-destructive-text hover:bg-button-destructive-bg-hover',
    outline: 'border border-border bg-surface hover:bg-surface-hover text-secondary',
    secondary: 'bg-button-secondary-bg text-button-secondary-text hover:bg-button-secondary-bg-hover',
    ghost: 'hover:bg-surface-hover text-secondary',
    link: 'underline-offset-4 hover:underline text-secondary',
  }
  
  const sizes = {
    default: 'h-10 py-2 px-4',
    sm: 'h-9 px-3',
    lg: 'h-11 px-8',
    icon: 'h-10 w-10',
  }
  
  return `${base} ${variants[props.variant]} ${sizes[props.size]}`
})
</script>

<template>
  <button
    :class="buttonClass"
    :disabled="disabled || loading"
    :aria-disabled="disabled || loading"
    :aria-busy="loading"
  >
    <span v-if="loading" class="flex items-center gap-2">
      <svg class="animate-spin h-4 w-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
      </svg>
      <slot />
    </span>
    <slot v-else />
  </button>
</template>
```

**Usage:**
```vue
<script setup>
import Button from '@/components/ui/Button.vue'
import { Download } from 'lucide-vue-next'
</script>

<template>
  <Button>Submit</Button>
  <Button variant="destructive">Delete Account</Button>
  <Button variant="outline">Cancel</Button>
  
  <!-- With icon -->
  <Button>
    <Download class="mr-2 h-4 w-4" />
    Download Report
  </Button>
  
  <!-- Loading state -->
  <Button :loading="isSubmitting">Processing...</Button>
</template>
```

### Vanilla HTML + CSS

```html
<!-- Default button -->
<button class="btn btn--primary">
  Submit
</button>

<!-- Destructive button -->
<button class="btn btn--destructive">
  Delete Account
</button>

<!-- Outline button -->
<button class="btn btn--outline">
  Cancel
</button>

<!-- With icon -->
<button class="btn btn--primary">
  <svg class="btn__icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor">
    <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
    <polyline points="7 10 12 15 17 10"></polyline>
    <line x1="12" y1="15" x2="12" y2="3"></line>
  </svg>
  Download Report
</button>

<!-- Icon only -->
<button class="btn btn--icon" aria-label="Close">
  <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor">
    <line x1="18" y1="6" x2="6" y2="18"></line>
    <line x1="6" y1="6" x2="18" y2="18"></line>
  </svg>
</button>

<!-- Loading state -->
<button class="btn btn--primary" disabled aria-busy="true">
  <span class="btn__spinner" aria-hidden="true"></span>
  Processing...
</button>
```

```css
/* Base button styles */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  line-height: 1.5;
  cursor: pointer;
  transition: background-color 200ms, border-color 200ms, opacity 200ms;
}

.btn:focus-visible {
  outline: 2px solid var(--color-focus-ring);
  outline-offset: 2px;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}

/* Variant: Primary */
.btn--primary {
  background-color: var(--color-button-primary-bg);
  color: var(--color-button-primary-text);
}

.btn--primary:hover:not(:disabled) {
  background-color: var(--color-button-primary-bg-hover);
}

/* Variant: Destructive */
.btn--destructive {
  background-color: var(--color-button-destructive-bg);
  color: var(--color-button-destructive-text);
}

.btn--destructive:hover:not(:disabled) {
  background-color: var(--color-button-destructive-bg-hover);
}

/* Variant: Outline */
.btn--outline {
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  color: var(--color-secondary);
}

.btn--outline:hover:not(:disabled) {
  background-color: var(--color-surface-hover);
}

/* Variant: Secondary */
.btn--secondary {
  background-color: var(--color-button-secondary-bg);
  color: var(--color-button-secondary-text);
}

.btn--secondary:hover:not(:disabled) {
  background-color: var(--color-button-secondary-bg-hover);
}

/* Variant: Ghost */
.btn--ghost {
  background-color: transparent;
  color: var(--color-secondary);
}

.btn--ghost:hover:not(:disabled) {
  background-color: var(--color-surface-hover);
}

/* Variant: Link */
.btn--link {
  background-color: transparent;
  color: var(--color-secondary);
  text-decoration: none;
}

.btn--link:hover:not(:disabled) {
  text-decoration: underline;
  text-underline-offset: 4px;
}

/* Size: Icon only */
.btn--icon {
  width: 40px;
  height: 40px;
  padding: 8px;
}

/* Loading spinner */
.btn__spinner {
  display: inline-block;
  width: 16px;
  height: 16px;
  border: 2px solid currentColor;
  border-right-color: transparent;
  border-radius: 50%;
  animation: btn-spin 600ms linear infinite;
}

@keyframes btn-spin {
  to { transform: rotate(360deg); }
}

/* Icon sizing */
.btn__icon {
  width: 16px;
  height: 16px;
  flex-shrink: 0;
}
```

---

## 7. Cross-References

### Uses (Dependencies)

This component **depends on**:
- **Icon component** (for "with icon", "just icon", "just icon circle" variants)
- **Typography tokens** (font-family, font-size, font-weight, line-height)
- **Color tokens** (background, text, border colors)
- **Spacing tokens** (padding, gap values)
- **Radius tokens** (corner radius)
- **Transition tokens** (duration, easing for hover/focus states)

**⚠️ Missing dependencies** (not found in Figma file):
- No Figma variables exist for any of these dependencies
- All values are hardcoded

### Used By (Dependents)

This component is **used within**:
- **Dialog** — Primary/secondary action buttons in footer
- **Card** — Call-to-action buttons in card footer
- **Form** — Submit/cancel/reset buttons
- **Modal** — Confirmation/dismissal actions
- **AlertDialog** — Destructive action confirmation
- **Popover** — Trigger element (outline/ghost variants)
- **DropdownMenu** — Trigger element (outline/ghost variants)
- **Combobox** — Trigger element
- **Sheet** — Trigger and action buttons
- **Toast** — Action buttons in notifications

**Composition patterns:**
- **ButtonGroup** — Multiple related actions (segmented control)
- **SplitButton** — Button + dropdown combination
- **LoadingButton** — Button with built-in loading state (extends this component)
- **IconButton** — Icon-only button with tooltip (wraps this component)

### Related Components

| Component | Relationship | When to Use |
|---|---|---|
| **Link** | Alternative | Navigation to another page (preserves browser history) |
| **Switch** | Alternative | Toggle on/off state (binary state control) |
| **Radio** | Alternative | Select single option from list |
| **Checkbox** | Alternative | Select multiple options |
| **DropdownMenu** | Composition | Button that opens a menu |
| **Tooltip** | Composition | Provide accessible label for icon-only buttons |
| **Badge** | Composition | Show notification count or status on button |
| **Icon** | Dependency | Visual indicator within button |

---

## 8. Audit Results

### shadcn/ui Compliance: 0%

**Violations: 25 total**

#### Tier 1: Zero-Risk Fixes (1 violation)
- **Variant naming**: "subtle" should be "secondary" (shadcn/ui standard)

#### Tier 2: Low-Risk Fixes (1 violation)
- **Token architecture**: 25 hardcoded colors found (should use Figma variables)
  - All background colors hardcoded (#0F172A, #EF4444, #FFFFFF, etc.)
  - All border colors hardcoded (#E2E8F0)
  - No semantic → primitive token aliasing

#### Tier 3: Requires Manual Review (19 violations)
- **Accessibility — Touch targets**: All 19 variants have height=40px (need 44px minimum for WCAG 2.2 AA)
  - `default`: 93×40px → needs 93×44px
  - `primary`: 93×40px → needs 93×44px
  - `destructive`: 110×40px → needs 110×44px
  - `outline`: 79×40px → needs 79×44px
  - `subtle`: 76×40px → needs 76×44px
  - `ghost`: 73×40px → needs 73×44px
  - `link`: 60×40px → needs 60×44px
  - `with icon`: 165×40px → needs 165×44px
  - `just icon`: 32×32px → needs 44×44px
  - `just icon circle`: 40×40px → needs 44×44px
  - `loading`: 110×40px → needs 110×44px

#### Convention Violations (4 violations)
- **Icon variants**: "just icon", "just icon circle", "with icon" should use composition pattern instead of variant property
- **Loading variant**: "loading" should be a boolean prop (`loading={true}`), not a variant value

### Missing States
- **Focus state**: Required for keyboard navigation (WCAG 2.2 AA)
- **Active state**: Provides tactile feedback for click/tap
- **Disabled state**: Visual indicator when button is not interactive

### Recommendations

**Immediate fixes (Tier 1 + Tier 2):**
1. Rename "subtle" variant to "secondary"
2. Create Figma variable collections for all hardcoded colors
3. Bind all background/border/text colors to semantic variables
4. Create 3-layer token architecture (primitives → semantics → usage)

**Manual review required (Tier 3):**
5. Increase all button heights from 40px to 44px minimum
6. Increase icon-only button sizes to 44×44px minimum

**Architecture improvements:**
7. Remove "with icon", "just icon", "just icon circle" variants — use composition instead
8. Convert "loading" variant to boolean prop
9. Add missing states: focus, active, disabled
10. Create typography tokens for font-family, font-size, font-weight

---

*Generated by design-nexus v3.0.0-enhanced — 75% aligned with industry LLM-friendly standards*  
