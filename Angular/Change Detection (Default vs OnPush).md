Link: `https://blog.angular-university.io/how-does-angular-2-change-detection-really-work/?utm_source=chatgpt.com`

# Angular Change Detection Notes

# What is Change Detection (CD)?

Change Detection is Angular's mechanism to:

```txt
Detect data changes and update the UI
```

Angular continuously checks template bindings and updates DOM when values change.

Example:

```html
{{ username }}
```

Angular reevaluates:

```ts
this.username
```

during CD cycles.

---

# What is a Change Detection Cycle?

A CD cycle means:

```txt
Angular walks through components
→ reevaluates template expressions
→ updates DOM if needed
```

Example:

```html
{{ user.name }}
```

Angular checks:

```ts
previousValue !== currentValue
```

If changed:

```txt
DOM updates
```

---

# What triggers Change Detection?

Angular runs CD after many async/browser events.

## Common Triggers

- click
- input typing
- keypress
- HTTP response
- Promise resolve
- Observable emission
- setTimeout
- setInterval
- route change
- Angular event listener
- async pipe emission

---

# Does Angular continuously run CD on idle screen?

## No

Angular is event-driven.

It does NOT do:

```ts
while(true) {
  detectChanges();
}
```

If absolutely nothing happens:

- no async task
- no event
- no timer
- no observable

then Angular stays idle.

---

# Why CD runs frequently in real apps

Apps usually contain:

```ts
setInterval()
```

or:

```ts
timer()
```

or:

```ts
Observable streams
```

or event listeners.

These repeatedly trigger CD.

---

# What is Zone.js?

Angular traditionally uses:

```txt
zone.js
```

Zone.js patches async browser APIs like:

- setTimeout
- addEventListener
- Promise
- XHR

When async work finishes:

```txt
Zone.js notifies Angular
→ Angular runs CD
```

---

# Default Change Detection Strategy

```ts
@Component({
  changeDetection: ChangeDetectionStrategy.Default
})
```

This is Angular's default behavior.

Angular checks components frequently.

---

# How Default CD works

Angular reevaluates template expressions every CD cycle.

Example:

```html
{{ user.profile.name }}
```

Angular reevaluates:

```ts
user.profile.name
```

every cycle.

---

# Important Clarification

Angular does NOT deep compare entire objects recursively.

It reevaluates expressions and compares final result.

Example:

```html
{{ user.profile.name }}
```

Angular compares:

```ts
oldName !== newName
```

NOT whole object structure.

---

# Nested Object Mutation in Default Strategy

This works:

```ts
this.user.profile.name = 'David';
```

because component is checked again.

UI updates.

---

# OnPush Change Detection Strategy

```ts
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
```

Angular becomes more optimized.

Component is checked only under certain conditions.

---

# When OnPush runs CD

## 1. @Input reference changes

GOOD:

```ts
this.user = {
  ...this.user,
  name: 'John'
};
```

BAD:

```ts
this.user.name = 'John';
```

OnPush checks references.

---

## 2. Event inside component

Example:

```html
<button (click)="save()">
```

Event triggers CD.

---

## 3. Observable emits via async pipe

```html
{{ user$ | async }}
```

Angular updates UI automatically.

---

## 4. Manual trigger

```ts
constructor(
  private cd: ChangeDetectorRef
) {}

this.cd.markForCheck();

this.cd.detectChanges();
```

---

# Why OnPush is faster

Default strategy checks many components frequently.

OnPush skips unnecessary component checks.

Huge optimization for:

- tables
- dashboards
- enterprise apps
- large lists
- charts
- grids

---

# Important OnPush Rule

Mutation often fails.

BAD:

```ts
this.items.push(newItem);
```

Reference remains same.

GOOD:

```ts
this.items = [
  ...this.items,
  newItem
];
```

New reference triggers UI update.

---

# Function Calls in Templates

Example:

```html
{{ getName() }}
```

Angular executes:

```ts
getName()
```

during EVERY CD cycle.

---

# Why Angular repeatedly calls template functions

Angular cannot know:

```txt
Is this function pure?
Does it always return same value?
```

So Angular reevaluates it every cycle.

---

# Bad Example

```html
<div *ngFor="let item of getFilteredItems()">
```

Filtering runs repeatedly.

Can severely hurt performance.

---

# Best Practice

Avoid functions in templates when possible.

BAD:

```html
{{ getFullName() }}
```

GOOD:

```ts
fullName = `${this.user.first} ${this.user.last}`;
```

```html
{{ fullName }}
```

---

# Another Common Mistake

BAD:

```html
[style]="getStyles()"
```

BAD:

```ts
getStyles() {
  return {
    color: 'red'
  };
}
```

New object created every cycle.

---

# Mouse Events and CD

Example:

```html
<div (mousemove)="track()">
```

Every mouse move:

```txt
mousemove
→ Angular event handler
→ CD cycle
```

Can trigger hundreds of CD cycles.

---

# Does plain mouse movement trigger CD?

No.

This alone:

```html
<div>
```

does NOT trigger CD.

Only Angular/Zone-aware events do.

---

# ngDoCheck

Useful for observing CD cycles.

```ts
ngDoCheck() {
  console.log('CD running');
}
```

You will see how often CD executes.

---

# trackBy in ngFor

Without trackBy Angular recreates DOM unnecessarily.

BAD:

```html
<div *ngFor="let item of items">
```

GOOD:

```html
<div *ngFor="let item of items; trackBy: trackById">
```

```ts
trackById(index: number, item: any) {
  return item.id;
}
```

Important for large tables/lists.

---

# runOutsideAngular

Used for performance-heavy events.

Example:

```ts
this.ngZone.runOutsideAngular(() => {

  window.addEventListener('mousemove', () => {

    // no Angular CD

  });

});
```

Useful for:

- drag/drop
- resize
- canvas
- charts
- games
- custom grids

---

# Default vs OnPush Summary

# Default

```txt
Checks components frequently
Handles mutations automatically
Simpler but less optimized
```

# OnPush

```txt
Skips unnecessary checks
Requires immutable updates
Much better performance
```

---

# Mental Model

## Default

```txt
"Check almost everything often"
```

## OnPush

```txt
"Check only when necessary"
```

---

# Important Clarification

CD cycle does NOT mean:

```txt
DOM recreated every time
```

Angular only updates DOM if values changed.

But expressions/functions ARE reevaluated.

---

# Best Practices

## Prefer

- OnPush
- async pipe
- immutable updates
- trackBy
- pure pipes
- computed values
- signals (modern Angular)

## Avoid

- heavy template functions
- unnecessary object creation
- mutation-heavy patterns
- frequent Angular event listeners

---

# Interview Points

## Why template functions are dangerous?

Because Angular calls them every CD cycle.

---

## Why OnPush may not update UI?

Because Angular checks reference changes, not mutations.

---

## Why immutable updates matter?

New reference triggers CD properly.

---

## Difference between Default and OnPush?

Default checks frequently.
OnPush skips unnecessary component checks.

---

# Simple Final Summary

```txt
Change Detection =
Angular checking template bindings again
to decide whether UI should update
```

```txt
Default =
More automatic, more checks
```

```txt
OnPush =
More optimized, fewer checks
```
