# Button Component Specification

**Component Type**: ATOM  
**Design System**: Ant Design  
**Figma Source**: https://www.figma.com/design/4y9pYMXKPVqcqRDEBKNPTZ/Ant-Design-Open-Source--Community-  
**Generated**: 2026-04-24 by design-nexus v3.0.0-enhanced

---

## Section 1: Overview

### When to Use

Use the Button component when:

1. **Primary Actions** — Triggering the main action on a page (form submission, confirmation, proceeding to next step)
2. **Navigation** — Moving between pages, steps, or views
3. **Dangerous Operations** — Deleting data, closing without saving (use `danger` variant)
4. **Links vs Actions** — Use `link` variant for navigation that behaves like a hyperlink
5. **Icon-Only Actions** — Use `shape="circle"` for icon-only buttons (settings, close, menu)
6. **Ghost Buttons** — Use `ghost` variant for subtle actions on colored backgrounds

### When NOT to Use

Avoid using Button when:

1. **Text-Heavy Content** — Use a hyperlink (`<a>`) for inline text navigation
2. **Toggle State** — Use Switch or Checkbox for binary on/off controls
3. **Multiple Selection** — Use Checkbox Group for selecting multiple options
4. **Single Selection from List** — Use Radio Group or Select dropdown
5. **Complex Forms** — Use dedicated form components (Input, Select, DatePicker)

### Alternatives

| Use Case | Alternative Component | Why |
|---|---|---|
| Navigation within text | Link | Better semantic HTML, better for SEO |
| Binary toggle (on/off) | Switch | Clear visual indication of state |
| Select one from many | Radio Group, Select | Reduces cognitive load |
| Select multiple | Checkbox Group | Better UX for multi-select |
| Upload files | Upload | Specialized for file handling |

### Related Components

Buttons are commonly used with:

- **Modal/Dialog** — Primary/secondary actions in footer
- **Form** — Submit/reset actions
- **Card** — Call-to-action buttons
- **Dropdown** — Button as trigger (split buttons)
- **Tooltip** — Explain icon-only buttons
- **Popconfirm** — Dangerous actions requiring confirmation
- **Badge** — Show notification count on button
- **Space/ButtonGroup** — Grouping related actions

---

## Section 2: Design Decision

### WHY This Component Exists

The Button component solves 5 core problems:

1. **Action Hierarchy** — Visually distinguish primary vs secondary vs tertiary actions
2. **Interactive Affordance** — Clearly signal what's clickable/tappable
3. **State Communication** — Show hover, active, disabled, loading states
4. **Semantic Consistency** — Standardize "danger" actions (destructive operations)
5. **Accessibility** — Ensure keyboard navigation, focus states, touch targets

### Design System Role

The Button is the **fundamental interactive primitive** in Ant Design. It:

- Establishes the visual language for all clickable elements
- Defines the interaction patterns (hover → active → disabled)
- Sets the token bindings (colors, spacing, typography) that cascade to other components
- Serves as the composition building block for complex components (Modal, Popconfirm, Dropdown)

### Historical Context

Ant Design's Button component is based on:

- **Ant Design 4.x** specifications (Chinese design philosophy)
- **Material Design** influence (ripple effects, ghost variants)
- **Enterprise UI conventions** (dashed borders for secondary, text variant for tertiary)

**Key Deviation from shadcn/ui**:
- Uses `Type` property instead of `variant`
- Separates `Danger` and `Ghost` into boolean properties instead of variant values
- Includes `dashed` variant (non-standard in Western design systems)
- Uses `medium` instead of `default` for size naming

---

## Section 3: Anatomy

### Constituent Parts

The Button component consists of 4 parts:

1. **Container** — Background, border, padding, corner radius
2. **Text** — Button label (Typography component)
3. **Icon** — Optional leading/trailing icon (Icon component)
4. **Loading Spinner** — Replaces icon when `state="animating"`

### Visual Hierarchy

```
┌─────────────────────────────────────┐
│  Container                          │
│  ┌───────────────────────────────┐  │
│  │ [Icon] Text [Loading Spinner]│  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

**Spacing Relationships**:
- Horizontal padding: 16px (large), 12px (medium), 8px (small)
- Icon-to-text gap: 8px
- Vertical padding: Auto (based on line-height + height)

**Typography Hierarchy**:
- Large: 16px / 1.5715 line-height / 500 weight
- Medium: 14px / 1.5715 line-height / 400 weight
- Small: 14px / 1.5715 line-height / 400 weight

**Color Relationships**:
- Primary: Blue-6 background, White text
- Secondary: White background, Black text, Gray-5 border
- Dashed: White background, Black text, Gray-5 dashed border
- Text: Transparent background, Black text
- Link: Transparent background, Blue-6 text
- Danger: Red-6 background, White text (when combined with primary)

### ASCII Diagram

```
Size: Large (40px height)
┌────────────────────────────────────────────────┐
│ 16px │ [Icon 16×16] 8px Text 16px │ 16px       │
│      │                           │            │
└────────────────────────────────────────────────┘
    padding        content         padding

Size: Medium (32px height) — DEFAULT
┌────────────────────────────────────────────────┐
│ 12px │ [Icon 14×14] 8px Text 14px │ 12px       │
└────────────────────────────────────────────────┘

Size: Small (24px height)
┌────────────────────────────────────────────────┐
│ 8px │ [Icon 14×14] 8px Text 14px │ 8px         │
└────────────────────────────────────────────────┘

Shape: Circle (Icon-only, Square dimensions)
┌──────────────┐
│      40      │
│   [Icon]     │  Large: 40×40px
│      40      │
└──────────────┘

Shape: Square (Icon-only, Square dimensions)
┌──────────────┐
│   [Icon]     │  Large: 40×40px
└──────────────┘  Medium: 32×32px, Small: 24×24px
```

---

## Section 4: Props/API

### Component Properties

| Property | Type | Default | Values | Description |
|---|---|---|---|---|
| `Type` | VARIANT | `primary` | `primary`, `secondary`, `dashed`, `link`, `text` | Button visual style |
| `Size` | VARIANT | `medium` | `large`, `medium`, `small` | Button dimensions |
| `Shape` | VARIANT | `standard` | `standard`, `circle`, `square` | Button shape (circle/square for icon-only) |
| `State` | VARIANT | `normal` | `normal`, `hover or press`, `disabled`, `active`, `animating` | Interaction state |
| `Danger` | VARIANT | `false` | `false`, `true` | Applies danger/destructive styling |
| `Ghost` | VARIANT | `false` | `true`, `false` | Transparent background on colored surfaces |
| `Icon` | BOOLEAN | `false` | `true`, `false` | Show icon (leading position) |
| `Title` | TEXT | `"Button Title"` | Any string | Button text label |
| `One Char Title` | TEXT | `"A"` | Single character | Single-character label (for circle buttons) |

### Variant Definitions

#### Type Variants

| Variant | Use Case | Visual Treatment |
|---|---|---|
| `primary` | Main action on the page | Blue-6 background, white text, solid fill |
| `secondary` | Secondary actions, cancel | White background, black text, Gray-5 border |
| `dashed` | Form items, placeholders | White background, black text, dashed Gray-5 border |
| `link` | Navigation, subtle actions | Transparent background, blue-6 text, no border |
| `text` | Tertiary actions, minimal emphasis | Transparent background, black text, no border |

**⚠️ Convention Violation**: `Type` property should be renamed to `variant` (shadcn/ui standard)  
**⚠️ Convention Violation**: `dashed` variant is non-standard (not in shadcn/ui)  
**⚠️ Convention Violation**: `text` variant overlaps with shadcn `ghost` purpose

#### Size Variants

| Variant | Height | Padding (H) | Font Size | Use Case |
|---|---|---|---|---|
| `large` | 40px | 16px | 16px | Page-level primary actions, hero CTAs |
| `medium` | ~32px | 12px | 14px | Default size, form actions |
| `small` | 24px | 8px | 14px | Compact UIs, table row actions, toolbars |

**⚠️ Convention Violation**: `medium` should be `default` (shadcn/ui standard)  
**⚠️ Accessibility Violation**: All sizes have height < 44px minimum touch target (WCAG 2.2 AA)

#### Shape Variants

| Variant | Use Case | Dimensions |
|---|---|---|
| `standard` | Default rectangle | Width: auto (based on text), Height: size-dependent |
| `circle` | Icon-only, rounded | Square dimensions + border-radius: 50% |
| `square` | Icon-only, sharp corners | Square dimensions + border-radius: 2px |

#### State Variants

| Variant | Visual Treatment | When to Use |
|---|---|---|
| `normal` | Default appearance | Idle state |
| `hover or press` | Lighter background, darker border | Mouse hover, touch press |
| `disabled` | Reduced opacity, gray colors | Action unavailable |
| `active` | Darker background | Currently selected/active option |
| `animating` | Loading spinner replaces icon | Async operation in progress |

**⚠️ Missing State**: `focus` state not defined (required for WCAG 2.2 AA keyboard navigation)

#### Danger Variants

| Variant | Use Case | Visual Treatment |
|---|---|---|
| `false` | Normal actions | Standard Type colors |
| `true` | Destructive actions (delete, remove, cancel) | Red-6 background, white text (primary), red text (other types) |

**⚠️ Convention Violation**: `Danger` should be a variant value of `Type` (e.g., `variant="destructive"`), not a separate property

#### Ghost Variants

| Variant | Use Case | Visual Treatment |
|---|---|---|
| `false` | Standard buttons | Normal background |
| `true` | Buttons on colored backgrounds | Transparent background, colored text/border |

**⚠️ Convention Violation**: `Ghost` should be a variant value of `Type`, not a separate boolean property

---

## Section 5: States

### State Definitions

| State | Description | Visual Changes | Accessibility |
|---|---|---|---|
| **Default (normal)** | Idle state, not interacted with | Base colors, no effects | No `aria-*` needed |
| **Hover (hover or press)** | Mouse hover or touch press | Lighter background (primary), darker border (secondary) | `aria-haspopup` if triggers dropdown |
| **Active** | Currently selected/pressed | Darker background, pressed appearance | `aria-pressed="true"` for toggle buttons |
| **Disabled** | Action unavailable | Gray background, reduced opacity, no hover | `disabled` attribute, `aria-disabled="true"` |
| **Loading (animating)** | Async operation in progress | Spinner replaces icon, button remains clickable | `aria-busy="true"`, `aria-label` updates |

### Missing States

The following states are **required for WCAG 2.2 AA** but not defined in Figma:

| Missing State | Why Required | Recommendation |
|---|---|---|
| **Focus** | Keyboard navigation | Add visible focus ring (2px blue outline, 2px offset) |

### State Transitions

```
     ┌─────────┐
     │ Normal  │
     └────┬────┘
          │
    ┌─────┴──────┐
    │            │
    v            v
┌────────┐  ┌────────────┐
│ Hover  │  │  Disabled  │
└───┬────┘  └────────────┘
    │
    v
┌────────┐
│ Active │
└───┬────┘
    │
    v
┌────────────┐
│  Loading   │
└────────────┘
```

---

## Section 6: Token Bindings

### Current Implementation Audit

**Status**: ❌ 100% hardcoded (0 Figma variables defined)

The Ant Design file contains **0 Figma variables**. All color, spacing, and typography values are hardcoded in component properties.

**Sample of hardcoded values** (from 20 of 205 variants checked):

| Variant | Property | Hardcoded Value | Recommended Token |
|---|---|---|---|
| Type=primary, Size=large | Background | #1890FF | `{color-button-primary-bg}` |
| Type=primary, Size=large | Text | #FFFFFF | `{color-button-primary-text}` |
| Type=secondary, Size=medium | Background | #FFFFFF | `{color-button-secondary-bg}` |
| Type=secondary, Size=medium | Border | #D9D9D9 | `{color-border}` |
| Type=dashed, Size=large | Border | #D9D9D9 (dashed) | `{color-border}` |
| Type=link, Size=medium | Text | #1890FF | `{color-button-link-text}` |
| Type=text, Size=small | Text | #000000 | `{color-text-primary}` |
| Danger=true, Type=primary | Background | #F5222D | `{color-button-danger-bg}` |
| State=disabled | Background | #F5F5F5 | `{color-button-disabled-bg}` |
| State=disabled | Text | #BFBFBF | `{color-button-disabled-text}` |

**Total unique hardcoded colors found**: ~15 (Blue-6, Red-6, White, Black, Gray-3, Gray-5, Gray-6, Blue-5, Blue-7, Red-5, etc.)

### Recommended 3-Layer Architecture

**LAYER 1: Upstream Tokens (--ds-*)**

Define primitive color scale in Figma variables:

| Token | Value | Description |
|---|---|---|
| `{ds/blue-6}` | #1890FF | Ant Design primary brand color |
| `{ds/blue-5}` | #40A9FF | Hover state |
| `{ds/blue-7}` | #096DD9 | Active state |
| `{ds/red-6}` | #F5222D | Danger/error brand color |
| `{ds/red-5}` | #FF4D4F | Danger hover |
| `{ds/gray-1}` | #FFFFFF | White |
| `{ds/gray-3}` | #F5F5F5 | Background |
| `{ds/gray-5}` | #D9D9D9 | Border default |
| `{ds/gray-6}` | #BFBFBF | Disabled text |
| `{ds/gray-13}` | #000000 | Black |

**LAYER 2: Semantic Tokens (--*)**

Map semantic meaning to primitives:

| Token | Primitive Reference | Fallback | Usage |
|---|---|---|---|
| `{color-button-primary-bg}` | `{ds/blue-6}` | `#1890FF` | Primary button background |
| `{color-button-primary-bg-hover}` | `{ds/blue-5}` | `#40A9FF` | Primary hover |
| `{color-button-primary-text}` | `{ds/gray-1}` | `#FFFFFF` | Primary text |
| `{color-button-danger-bg}` | `{ds/red-6}` | `#F5222D` | Danger button background |
| `{color-button-disabled-bg}` | `{ds/gray-3}` | `#F5F5F5` | Disabled background |
| `{color-border}` | `{ds/gray-5}` | `#D9D9D9` | Default border |

**LAYER 3: Component Usage**

Components reference semantic tokens ONLY (never primitives):

```jsx
// GOOD — references semantic token
<button style={{ 
  background: 'var(--color-button-primary-bg)',
  color: 'var(--color-button-primary-text)'
}}>

// BAD — hardcoded value
<button style={{ background: '#1890FF' }}>

// BAD — references primitive directly
<button style={{ background: 'var(--ds-blue-6)' }}>
```

### Non-Color Token Bindings

| Property | Current Value | Recommended Token |
|---|---|---|
| Horizontal padding (large) | 16px | `{spacing-md}` |
| Horizontal padding (medium) | 12px | `{spacing-3}` |
| Horizontal padding (small) | 8px | `{spacing-sm}` |
| Icon-to-text gap | 8px | `{spacing-sm}` |
| Border radius (standard) | 2px | `{radius-sm}` |
| Border radius (circle) | 50% | `{radius-full}` |
| Font size (large) | 16px | `{text-base}` |
| Font size (medium/small) | 14px | `{text-sm}` |

---

## Section 7: Code Examples

### React + Ant Design (Official)

```tsx
import { Button } from 'antd'
import { DownloadOutlined, DeleteOutlined, PlusOutlined } from '@ant-design/icons'

// Primary button (default)
<Button type="primary">Submit</Button>

// Secondary button (default in Ant Design = white background + border)
<Button>Cancel</Button>

// Destructive action (danger)
<Button type="primary" danger>
  Delete Account
</Button>

// Large size
<Button type="primary" size="large">
  Continue →
</Button>

// Small size
<Button size="small">Learn More</Button>

// Icon + Text
<Button type="primary" icon={<DownloadOutlined />}>
  Download Report
</Button>

// Icon-only circle button
<Button type="primary" shape="circle" icon={<DeleteOutlined />} />

// Loading state
<Button type="primary" loading>
  Submitting...
</Button>

// Dashed border
<Button type="dashed">+ Add Item</Button>

// Link variant
<Button type="link">Read Documentation</Button>

// Text variant
<Button type="text">Cancel</Button>

// Ghost variant on colored background
<Button type="primary" ghost>
  Transparent Primary
</Button>
```

### React + Tailwind CSS (shadcn-compatible adaptation)

```tsx
import React from 'react'
import { cva, type VariantProps } from 'class-variance-authority'
import { cn } from '@/lib/utils'

const buttonVariants = cva(
  // Base styles
  'inline-flex items-center justify-center gap-2 rounded-sm font-normal transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-blue-500 focus-visible:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none',
  {
    variants: {
      // ⚠️ Ant Design uses "Type" — mapped to "variant" for shadcn compliance
      variant: {
        primary: 'bg-blue-600 text-white hover:bg-blue-500 active:bg-blue-700',
        secondary: 'bg-white text-black border-2 border-gray-300 hover:border-gray-500',
        dashed: 'bg-white text-black border-2 border-dashed border-gray-300 hover:border-gray-500',
        text: 'bg-transparent text-black hover:bg-gray-100',
        link: 'bg-transparent text-blue-600 hover:text-blue-500 underline-offset-4 hover:underline',
        // ⚠️ Ant Design separates "danger" as boolean — combined here for shadcn compliance
        destructive: 'bg-red-600 text-white hover:bg-red-500 active:bg-red-700',
      },
      // ⚠️ Ant Design uses "medium" — mapped to "default" for shadcn compliance
      size: {
        small: 'h-6 px-2 text-sm',   // 24px height (violates 44px touch target)
        default: 'h-8 px-3 text-sm',  // ~32px height (violates 44px touch target)
        large: 'h-10 px-4 text-base', // 40px height (violates 44px touch target)
      },
      shape: {
        standard: '',
        circle: 'rounded-full aspect-square p-0',
        square: 'aspect-square p-0',
      },
      // ⚠️ Ant Design has "ghost" as boolean — included as variant for shadcn compliance
      ghost: {
        false: '',
        true: 'bg-transparent border-2',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'default',
      shape: 'standard',
      ghost: 'false',
    },
  }
)

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  loading?: boolean
}

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, shape, ghost, loading, children, ...props }, ref) => {
    return (
      <button
        ref={ref}
        className={cn(buttonVariants({ variant, size, shape, ghost }), className)}
        disabled={loading || props.disabled}
        aria-busy={loading}
        {...props}
      >
        {loading && <span className="animate-spin">⏳</span>}
        {children}
      </button>
    )
  }
)
Button.displayName = 'Button'
```

**Usage Examples**:

```tsx
// Primary button (default)
<Button>Submit</Button>

// Secondary button
<Button variant="secondary">Cancel</Button>

// Destructive action
<Button variant="destructive">Delete Account</Button>

// Large size
<Button size="large">Continue →</Button>

// Small size
<Button size="small">Learn More</Button>

// Icon + Text
<Button>
  <Icon name="download" />
  Download Report
</Button>

// Icon-only circle button
<Button shape="circle" size="large" aria-label="Settings">
  <Icon name="settings" />
</Button>

// Loading state
<Button loading>Submitting...</Button>

// Dashed border (Ant Design specific)
<Button variant="dashed">+ Add Item</Button>

// Link variant
<Button variant="link">Read Documentation</Button>

// Ghost variant on colored background
<Button ghost variant="primary">Transparent Primary</Button>
```

### Vue 3 + Ant Design

```vue
<script setup lang="ts">
import { Button } from 'ant-design-vue'
import { DownloadOutlined, DeleteOutlined } from '@ant-design/icons-vue'
</script>

<template>
  <!-- Primary button -->
  <a-button type="primary">Submit</a-button>
  
  <!-- Secondary (default) -->
  <a-button>Cancel</a-button>
  
  <!-- Destructive -->
  <a-button type="primary" danger>Delete</a-button>
  
  <!-- Large size -->
  <a-button type="primary" size="large">Continue</a-button>
  
  <!-- Icon + Text -->
  <a-button type="primary">
    <template #icon><DownloadOutlined /></template>
    Download
  </a-button>
  
  <!-- Loading -->
  <a-button type="primary" :loading="isLoading">Processing</a-button>
  
  <!-- Dashed -->
  <a-button type="dashed">+ Add</a-button>
  
  <!-- Ghost -->
  <a-button type="primary" ghost>Transparent</a-button>
</template>
```

### Vanilla HTML + CSS (Inferred Tokens)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ant Design Button</title>
  <link rel="stylesheet" href="../foundations/tokens.css">
  <style>
    /* Base button styles */
    .ant-btn {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      font-family: var(--font-family);
      font-weight: var(--font-normal);
      line-height: var(--leading-base);
      border-radius: var(--radius-sm);
      border: none;
      cursor: pointer;
      transition: background-color 0.2s, border-color 0.2s, color 0.2s;
    }
    
    .ant-btn:focus-visible {
      outline: 2px solid var(--color-border-focus);
      outline-offset: 2px;
    }
    
    .ant-btn:disabled {
      background-color: var(--color-button-disabled-bg);
      color: var(--color-button-disabled-text);
      border-color: var(--color-button-disabled-border);
      cursor: not-allowed;
    }
    
    /* Variant: Primary */
    .ant-btn--primary {
      background-color: var(--color-button-primary-bg);
      color: var(--color-button-primary-text);
      border: 1px solid var(--color-button-primary-border);
    }
    .ant-btn--primary:hover:not(:disabled) {
      background-color: var(--color-button-primary-bg-hover);
    }
    .ant-btn--primary:active:not(:disabled) {
      background-color: var(--color-button-primary-bg-active);
    }
    
    /* Variant: Secondary */
    .ant-btn--secondary {
      background-color: var(--color-button-secondary-bg);
      color: var(--color-button-secondary-text);
      border: 1px solid var(--color-button-secondary-border);
    }
    .ant-btn--secondary:hover:not(:disabled) {
      background-color: var(--color-button-secondary-bg-hover);
      border-color: var(--color-border-hover);
    }
    
    /* Variant: Dashed */
    .ant-btn--dashed {
      background-color: var(--color-button-dashed-bg);
      color: var(--color-button-dashed-text);
      border: 1px dashed var(--color-button-dashed-border);
    }
    .ant-btn--dashed:hover:not(:disabled) {
      background-color: var(--color-button-dashed-bg-hover);
      border-color: var(--color-border-hover);
    }
    
    /* Variant: Text */
    .ant-btn--text {
      background-color: var(--color-button-text-bg);
      color: var(--color-button-text-text);
    }
    .ant-btn--text:hover:not(:disabled) {
      background-color: var(--color-button-text-bg-hover);
    }
    
    /* Variant: Link */
    .ant-btn--link {
      background-color: transparent;
      color: var(--color-button-link-text);
      text-decoration: underline;
    }
    .ant-btn--link:hover:not(:disabled) {
      color: var(--color-button-link-text-hover);
    }
    
    /* Variant: Destructive (Danger) */
    .ant-btn--destructive {
      background-color: var(--color-button-danger-bg);
      color: var(--color-button-danger-text);
    }
    .ant-btn--destructive:hover:not(:disabled) {
      background-color: var(--color-button-danger-bg-hover);
    }
    
    /* Size: Small */
    .ant-btn--small {
      height: 24px;
      padding: 0 8px;
      font-size: var(--text-sm);
    }
    
    /* Size: Default (medium) */
    .ant-btn--default {
      height: 32px;
      padding: 0 12px;
      font-size: var(--text-sm);
    }
    
    /* Size: Large */
    .ant-btn--large {
      height: 40px;
      padding: 0 16px;
      font-size: var(--text-base);
    }
    
    /* Shape: Circle */
    .ant-btn--circle {
      border-radius: var(--radius-full);
      aspect-ratio: 1 / 1;
      padding: 0;
    }
    
    /* Shape: Square */
    .ant-btn--square {
      aspect-ratio: 1 / 1;
      padding: 0;
    }
    
    /* Ghost modifier */
    .ant-btn--ghost {
      background-color: transparent;
      border: 1px solid currentColor;
    }
    
    /* Loading state */
    .ant-btn--loading {
      pointer-events: none;
    }
  </style>
</head>
<body>
  <!-- Primary button (default size) -->
  <button class="ant-btn ant-btn--primary ant-btn--default">Submit</button>
  
  <!-- Secondary button -->
  <button class="ant-btn ant-btn--secondary ant-btn--default">Cancel</button>
  
  <!-- Destructive button -->
  <button class="ant-btn ant-btn--destructive ant-btn--default">Delete</button>
  
  <!-- Large size -->
  <button class="ant-btn ant-btn--primary ant-btn--large">Continue →</button>
  
  <!-- Small size -->
  <button class="ant-btn ant-btn--secondary ant-btn--small">Learn More</button>
  
  <!-- Dashed variant (Ant Design specific) -->
  <button class="ant-btn ant-btn--dashed ant-btn--default">+ Add Item</button>
  
  <!-- Text variant -->
  <button class="ant-btn ant-btn--text ant-btn--default">Cancel</button>
  
  <!-- Link variant -->
  <button class="ant-btn ant-btn--link ant-btn--default">Read Documentation</button>
  
  <!-- Icon-only circle button -->
  <button class="ant-btn ant-btn--primary ant-btn--large ant-btn--circle" aria-label="Settings">
    ⚙
  </button>
  
  <!-- Ghost variant on colored background -->
  <button class="ant-btn ant-btn--primary ant-btn--default ant-btn--ghost">Transparent</button>
  
  <!-- Loading state -->
  <button class="ant-btn ant-btn--primary ant-btn--default ant-btn--loading">
    <span class="animate-spin">⏳</span> Processing...
  </button>
  
  <!-- Disabled state -->
  <button class="ant-btn ant-btn--primary ant-btn--default" disabled>Submit</button>
</body>
</html>
```

---

## Section 8: Cross-References

### Uses (Dependencies)

The Button component **uses** these foundation tokens/components:

| Dependency | Purpose | Binding Type |
|---|---|---|
| **Color Tokens** | Background, text, border colors | Variable (recommended) |
| **Typography Tokens** | Font family, size, weight, line-height | Variable (recommended) |
| **Spacing Tokens** | Padding, icon gap | Variable (recommended) |
| **Radius Tokens** | Corner radius | Variable (recommended) |
| **Icon Component** | Leading/trailing icons | Component instance |
| **Loading Spinner** | Async operation indicator | Component instance |

### Used By (Dependents)

The Button component is **used by** these complex components:

| Component | Usage | Composition Pattern |
|---|---|---|
| **Modal/Dialog** | Primary/secondary actions in footer | `<Modal.Footer><Button /></Modal.Footer>` |
| **Form** | Submit/reset buttons | `<Form.Item><Button htmlType="submit" /></Form.Item>` |
| **Card** | Call-to-action buttons | `<Card.Actions><Button /></Card.Actions>` |
| **Dropdown** | Trigger button for menu | `<Dropdown overlay={menu}><Button /></Dropdown>` |
| **Popconfirm** | Confirm/cancel actions | `<Popconfirm onConfirm={...}><Button /></Popconfirm>` |
| **Table** | Row action buttons | `<Table.Column render={() => <Button size="small" />} />` |
| **Space/ButtonGroup** | Grouped button layouts | `<Space><Button /><Button /></Space>` |
| **Upload** | Upload trigger | `<Upload><Button icon={<Upload />} /></Upload>` |
| **Pagination** | Page navigation | Internal (not exposed) |
| **Steps** | Next/previous navigation | `<Steps.Action><Button /></Steps.Action>` |

### Related Components

Components that serve similar or complementary purposes:

| Component | Relationship | When to Choose |
|---|---|---|
| **Link** | Alternative for navigation | Use for inline text navigation, better SEO |
| **Switch** | Alternative for toggle | Use for binary on/off state (not actions) |
| **Radio/Checkbox** | Alternative for selection | Use for selecting options, not triggering actions |
| **Select** | Alternative for dropdown | Use for choosing from list, not triggering actions |
| **Dropdown.Button** | Composition pattern | Split button with primary action + dropdown menu |
| **IconButton** | Specialized button | Icon-only button (use `shape="circle"` instead) |

---

## Compliance Report

### shadcn/ui Convention Violations

**Score**: 20% compliant (8 violations found)

#### Tier 1 Violations (Zero-Risk Fixes)

1. **Property "Type" should be "variant"**  
   **Severity**: TIER_1  
   **Impact**: API naming inconsistency  
   **Fix**: Rename component property `Type` → `variant` in Figma  
   **Effort**: 1 minute (automated)

2. **Size value "medium" should be "default"**  
   **Severity**: TIER_1  
   **Impact**: API naming inconsistency  
   **Fix**: Rename variant value `medium` → `default`  
   **Effort**: 1 minute (automated)

#### Tier 2 Violations (Low-Risk Fixes)

3. **100% hardcoded colors (0 Figma variables)**  
   **Severity**: TIER_2  
   **Impact**: No token architecture, maintenance burden  
   **Fix**: Migrate to Figma variables with 3-layer architecture  
   **Effort**: 2-4 hours (manual)

#### Tier 3 Violations (Manual Review Required)

4. **All variants have height < 44px**  
   **Severity**: TIER_3 (Accessibility)  
   **Impact**: WCAG 2.2 AA violation (touch target size)  
   **Fix**: Increase heights: small=44px, default=44px, large=48px  
   **Effort**: 30-60 minutes (manual resize)

#### Convention Violations

5. **"Ghost" should be a variant value, not a boolean property**  
   **Severity**: CONVENTION  
   **Impact**: API surface explosion (2× variant count)  
   **Fix**: Merge into `variant` property with value `ghost-primary`, `ghost-secondary`, etc.  
   **Effort**: 2-3 hours (manual restructure)

6. **"Danger" should be "destructive" variant value**  
   **Severity**: CONVENTION  
   **Impact**: API naming inconsistency, separate property instead of variant  
   **Fix**: Merge into `variant` property with value `destructive`  
   **Effort**: 2-3 hours (manual restructure)

7. **"dashed" is non-standard variant**  
   **Severity**: CONVENTION  
   **Impact**: Not recognized in Western design systems (shadcn, Material, Chakra)  
   **Fix**: Consider removing or documenting as Ant Design-specific  
   **Effort**: 1 hour (documentation)

8. **"text" variant overlaps with "ghost"**  
   **Severity**: CONVENTION  
   **Impact**: Confusing distinction between `text` and `ghost` variants  
   **Fix**: Clarify use cases or merge into single variant  
   **Effort**: 1-2 hours (documentation + potential refactor)

### Missing States

| State | Impact | Recommendation |
|---|---|---|
| **Focus** | WCAG 2.2 AA violation | Add visible focus ring (2px blue outline, 2px offset) |

---

## Accessibility Checklist

- [ ] **Touch targets**: All button sizes meet 44×44px minimum (currently FAILING)
- [ ] **Color contrast**: Text meets 4.5:1 ratio (PASSING for most variants)
- [ ] **Keyboard navigation**: Focus state visible (MISSING — requires manual add)
- [ ] **Screen reader labels**: `aria-label` on icon-only buttons (developer responsibility)
- [x] **Disabled state**: Visually distinct (PASSING)
- [ ] **Loading state**: `aria-busy` attribute (developer responsibility)
- [x] **Interactive affordance**: Clear hover/active states (PASSING)

---

*Generated by design-nexus v3.0.0-enhanced — 75% aligned with industry LLM-friendly standards*  
