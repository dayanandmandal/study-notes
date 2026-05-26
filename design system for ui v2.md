# Additional Design System Notes

# 1. Component States

Most apps only style normal and hover states.

Production apps also need:

- focus
- disabled
- loading
- error
- success
- selected
- empty

---

## Disabled State

Used when actions are unavailable.

```css
button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
```

---

## Focus State

Critical for keyboard users.

```css
input:focus-visible,
button:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}
```

---

## Loading State

Avoid duplicate actions.

Example:

```css
.btn-loading {
  opacity: 0.7;
  pointer-events: none;
}
```

---

## Error State

```css
.input-error {
  border-color: var(--color-danger);
}
```

---

## Success State

```css
.input-success {
  border-color: var(--color-success);
}
```

---

# 2. Responsive Design

Your app currently behaves desktop-first.

A real system defines rules for:

- mobile
- tablet
- desktop

---

## Typical Breakpoints

```css
--breakpoint-mobile: 480px;
--breakpoint-tablet: 768px;
--breakpoint-desktop: 1024px;
```

---

## Mobile Layout Rules

Common patterns:

- sidebar collapses
- stacked sections
- reduced spacing
- full-width forms
- smaller typography

---

## Example Media Query

```css
@media (max-width: 768px) {
  .notes-wrap {
    flex-direction: column;
  }
}
```

---

# 3. Layout System

A layout system defines structure consistency.

Without this:
every page becomes different.

---

## Common Layout Tokens

```css
--content-width: 1200px;
--notes-sidebar-width: 400px;
--page-padding: 24px;
```

---

## Content Container

```css
.content {
  width: min(100%, var(--content-width));
  margin-inline: auto;
}
```

---

## Spacing Philosophy

Use spacing to create hierarchy.

Example:

- page spacing > section spacing > card spacing > inner spacing

---

# 4. Motion System

Animations should support usability,
not distract users.

---

## Good Motion Rules

- subtle
- fast
- predictable
- purposeful

---

## Recommended Motion Usage

### Hover Lift

```css
.card:hover {
  transform: translateY(-1px);
}
```

---

### Transitions

```css
transition: var(--transition-fast);
```

---

## Avoid

- large movement
- slow animations
- bouncing everywhere
- excessive scaling

---

# 5. Accessibility

Accessibility is part of professional frontend engineering.

Not optional.

---

# Important Accessibility Areas

## Contrast

Text should remain readable.

Bad:

```css
light gray text on white
```

Good:

```css
dark text on light background
```

---

## Keyboard Navigation

Everything interactive should be keyboard accessible.

Examples:

- buttons
- forms
- navigation
- modals

---

## Focus Visibility

Never remove focus outlines without replacement.

Bad:

```css
outline: none;
```

Good:

```css
:focus-visible {
  outline: 2px solid var(--color-primary);
}
```

---

## Semantic HTML

Use proper elements.

Good:

```html
<button>
  <nav>
    <header>
      <main>
        <section></section>
      </main>
    </header>
  </nav>
</button>
```

Avoid clickable divs when buttons are appropriate.

---

## Touch Targets

Buttons should not be tiny.

Recommended:

```txt
minimum 40px × 40px
```

---

# 6. Icon System

As apps grow, icons need consistency.

Define:

- icon sizes
- spacing
- hover behavior
- button usage

---

## Example

```css
.icon-btn {
  width: 36px;
  height: 36px;
}
```

---

# 7. Z-Index System

Without a z-index system,
overlays become chaotic.

---

## Recommended Tokens

```css
--z-dropdown: 100;
--z-sticky: 200;
--z-modal: 1000;
--z-toast: 1100;
```

---

# 8. Form Validation Design

Validation styling should be standardized.

---

## Error Text

```css
.error-text {
  color: var(--color-danger);
}
```

---

## Helper Text

```css
.helper-text {
  color: var(--color-text-secondary);
}
```

---

# 9. Empty States

Good apps handle empty states gracefully.

Examples:

- no notes
- no todos
- no search results
- no notifications

---

## Good Empty State Includes

- title
- explanation
- suggested action

Example:

```txt
No todos found
Try changing filters or create a new task.
```

---

# 10. CSS Architecture

CSS organization matters as projects grow.

---

# Recommended Structure

```txt
styles/
├── base/
├── layout/
├── components/
├── pages/
├── utilities/
```

---

## Base

Global resets and variables.

---

## Layout

Grid systems and page layouts.

---

## Components

Reusable UI pieces.

Examples:

- buttons
- cards
- inputs
- modals

---

## Pages

Page-specific styling.

---

## Utilities

Helper classes.

Example:

```css
.text-muted
.hidden
.flex-center
```

---

# 11. Reusable Component Inventory

Eventually your app should have reusable components.

Examples:

- Button
- Input
- Select
- Card
- Badge
- Modal
- Tabs
- Dropdown
- Toast

---

# 12. Design Principles

These guide UI decisions.

---

## Good Design Principles

### Consistency Over Creativity

Predictable UI is easier to use.

---

### Spacing Creates Hierarchy

Spacing is one of the most powerful design tools.

---

### Color Should Communicate Meaning

Use color intentionally.

Examples:

- red = danger
- green = success
- purple = primary action

---

### Subtle Beats Flashy

Small polished interactions feel more professional than excessive animations.

---

### Reusability Matters

Avoid styling screen-by-screen.

Think component-first.

---

# 13. Biggest Scaling Mistake

Bad:

```css
.todo-save-btn {
}
.notes-save-btn {
}
.dashboard-save-btn {
}
```

Good:

```css
.btn {
}
.btn-primary {
}
.btn-danger {
}
```

Reusable systems scale much better.

---

# 14. What You Should Focus On Next

Most valuable improvements for your current level:

1. reusable component classes
2. responsive layouts
3. accessibility
4. better spacing consistency
5. component-first CSS architecture
6. validation states
7. semantic HTML structure

---

# 15. Final Mindset Shift

A design system is not:

- colors
- buttons
- spacing tokens

A design system is:

> a framework for making consistent UI decisions.

That mindset is what separates:
"CSS styling"
from
"frontend engineering."

Design systems scale by intent, not by color