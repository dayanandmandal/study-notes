# Absolute Overlay Approach

Used when switching between multiple UI panels without causing layout shift.

## Idea

Keep both components inside the same parent and overlay them using:

- `position: absolute`
- `opacity`
- `visibility`

instead of:

- removing from DOM
- using `display: none`

This prevents content from jumping when panel heights differ.

---

## Example Use Cases

- Form ↔ Bulk actions
- Tabs
- Modals
- Dropdowns
- Loading states

---

## Structure

```html
<div class="toolbar">
  <form class="todo-form"></form>

  <div class="bulk-actions"></div>
</div>
```
