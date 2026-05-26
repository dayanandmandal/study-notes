# Frontend HTML Attribute Architecture Notes

---

# 1. `class` → Styling Only

Use `class` ONLY for:

- styling
- layout
- visual appearance
- **temporary UI states**

GOOD:

```html
<button class="btn btn-primary"></button>
```

BAD:

```html
<button class="save-note"></button>
```

Why bad?

- JS becomes dependent on styling names
- styles change frequently
- can accidentally break JS

Rule:

> CSS classes should not define application behavior.

---

# 2. `data-role` → Stable Element Identity

Use `data-role` when:

- selecting unique/stable elements
- forms
- modal containers
- sidebar toggle
- save button
- search input

Example:

```html
<button data-role="save-note"></button>
```

JS:

```js
const saveBtn = form.querySelector('[data-role="save-note"]');
```

Mental model:

> "What IS this element?"

Best for:

- singleton UI elements
- direct element selection
- stable app structure

Examples:

```html
<form data-role="notes-form">
  <input data-role="search-input" />
  <button data-role="theme-toggle"></button>
</form>
```

---

# 3. `data-action` → User-triggered Actions

Use `data-action` when:

- user interaction triggers behavior
- event delegation
- repeated action buttons
- dynamic lists

Example:

```html
<button data-action="delete-note"></button>
```

JS:

```js
container.addEventListener("click", (e) => {
  const action = e.target.dataset.action;

  switch (action) {
    case "delete-note":
      deleteNote();
      break;
  }
});
```

Mental model:

> "What ACTION should happen?"

Best for:

- edit buttons
- delete buttons
- archive buttons
- toggle buttons
- delegated event systems

Examples:

```html
<button data-action="edit-note">
  <button data-action="delete-note">
    <button data-action="toggle-todo"></button>
  </button>
</button>
```

---

# 4. `data-id` → Entity Identification

Use `data-id` for:

- identifying records/items
- mapping UI to data

Example:

```html
<div class="note" data-id="42"></div>
```

JS:

```js
const id = note.dataset.id;
```

Mental model:

> "Which item does this belong to?"

Best for:

- notes
- todos
- tasks
- database entities

Examples:

```html
<div data-id="1">
  <li data-id="22"></li>
</div>
```

---

# 5. `data-filter` → Filtering / Categories

Use `data-filter` for:

- filtering
- tabs
- categories
- view modes

Example:

```html
<div data-filter="done"></div>
```

JS:

```js
const filter = tab.dataset.filter;
```

Mental model:

> "Which category/filter is selected?"

Examples:

```html
data-filter="all" data-filter="active" data-filter="done"
```

---

# 6. `data-section` → Routing / Page Sections

Use for:

- navigation
- tabs
- page switching
- section visibility

Example:

```html
<li data-section="notes"></li>
```

Mental model:

> "Which section/page does this belong to?"

Examples:

```html
data-section="dashboard" data-section="todo" data-section="notes"
```

---

# 7. `data-mode` → UI Mode / State

Use for:

- create/edit mode
- open/closed state
- loading states

Examples:

```html
<form data-mode="edit"></form>
```

Mental model:

> "What STATE or MODE is this UI in?"

Examples:

```html
data-mode="create" data-mode="edit" data-state="open" data-state="loading"
```

---

# 8. Direct Selection vs Event Delegation

## Direct Selection → `data-role`

Example:

```js
const saveBtn = form.querySelector('[data-role="save-note"]');

saveBtn.addEventListener("click", saveNote);
```

Best for:

- stable elements
- forms
- singleton UI

---

## Event Delegation → `data-action`

Example:

```js
container.addEventListener("click", (e) => {
  const action = e.target.dataset.action;

  switch (action) {
    case "delete-note":
      deleteNote();
      break;
  }
});
```

Best for:

- repeated dynamic elements
- lists
- scalable interaction systems

---

# 9. Can an Element Have BOTH?

YES.

Example:

```html
<button data-role="save-note" data-action="save-note"></button>
```

But often this is redundant.

Usually:

- stable/singleton UI → `data-role`
- repeated delegated actions → `data-action`

---

# 10. Recommended Architecture

## Styling

```html
class=""
```

## Stable UI elements

```html
data-role=""
```

## User interactions

```html
data-action=""
```

## Entity identification

```html
data-id=""
```

## Filtering/categories

```html
data-filter=""
```

## Navigation/sections

```html
data-section=""
```

## UI state/mode

```html
data-mode="" data-state=""
```

---

# 11. Recommended Mental Model

| Attribute    | Purpose               |
| ------------ | --------------------- |
| class        | Styling               |
| data-role    | Element identity      |
| data-action  | User action           |
| data-id      | Entity identification |
| data-filter  | Filtering/category    |
| data-section | Routing/navigation    |
| data-mode    | UI mode/state         |

---

# 12. Real Example

```html
<button
  class="btn btn-icon btn-ghost"
  data-role="edit-button"
  data-action="edit-note"
  data-id="42"
>
  ✏️
</button>
```

Responsibilities:

- `class` → styling
- `data-role` → stable element identity
- `data-action` → behavior/action
- `data-id` → identifies note
