````md
# ExpressionChangedAfterItHasBeenCheckedError

## What is it?

Angular throws `ExpressionChangedAfterItHasBeenCheckedError` in **development mode** when a template-bound value changes **after Angular has already checked it during the same change detection cycle**.

---

## Common Cause

Changing a bound property inside lifecycle hooks such as:

- `ngAfterViewInit`
- `ngAfterViewChecked`

Example:

```ts
message = 'A';

ngAfterViewChecked() {
  this.message = 'B';
}
```

Template:

```html
{{ message }}
```

---

## Why Does Angular Throw This Error?

Angular expects the view to be stable during a change detection cycle.

Flow:

```text
Check bindings
    ↓
View checked
    ↓
Value changes
    ↓
View becomes inconsistent
```

Angular detects this and throws an error.

---

## Development Mode vs Production Mode

### Development Mode

Angular runs change detection twice:

```text
Pass 1: Check bindings
Pass 2: Verify bindings did not change
```

If a value changes between passes:

```text
ExpressionChangedAfterItHasBeenCheckedError
```

is thrown.

### Production Mode

Only one pass is executed.

```text
No verification pass
No error thrown
```

The bug may still exist but Angular will not warn you.

---

## Why Is This Useful?

It helps detect:

- Unstable UI state
- Incorrect lifecycle usage
- Potential change detection loops
- Hidden bugs that may behave differently in production

---

## Interview Answer

Angular throws `ExpressionChangedAfterItHasBeenCheckedError` when a bound value changes after Angular has already checked it during the same change detection cycle. This typically happens inside lifecycle hooks like `ngAfterViewInit` or `ngAfterViewChecked`. Angular detects this in development mode to ensure the view remains stable and to prevent change detection issues.
````
