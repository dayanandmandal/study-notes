# Angular Lifecycle Hooks

## What are Lifecycle Hooks?

Lifecycle hooks are methods Angular calls at specific stages of a component's life.

Actual execution order:

```txt
Constructor
    ↓
ngOnChanges
    ↓
ngOnInit
    ↓
ngDoCheck
    ↓
ngAfterContentInit
    ↓
ngAfterContentChecked
    ↓
ngAfterViewInit
    ↓
ngAfterViewChecked
    ↓
ngOnDestroy
```

---

# Frequently Used Hooks (90% of Real Projects)

## 1. Constructor

Used for Dependency Injection only.

```ts
constructor(private userService: UserService) {}
```

### Use Cases

- Inject services
- Initialize simple properties

### Avoid

- API calls
- Accessing `@Input()`
- DOM manipulation

### Interview Tip

Constructor belongs to TypeScript/JavaScript, not Angular.

---

## 2. ngOnInit

Runs once after Angular initializes component inputs.

```ts
ngOnInit() {
  this.loadUsers();
}
```

### Use Cases

- Initial API calls
- Component initialization
- Loading configuration

### Example

```ts
ngOnInit() {
  this.userService.getUsers();
}
```

Most API calls belong here.

---

## 3. ngOnChanges

Runs whenever an `@Input()` changes.

```ts
@Input() userId!: number;

ngOnChanges(changes: SimpleChanges) {
  console.log(changes['userId']);
}
```

### Use Cases

- Reacting to parent data changes
- Reloading data when input values change

### Example

```txt
Parent changes userId
        ↓
Child receives new userId
        ↓
ngOnChanges runs
```

---

## 4. ngAfterViewInit

Runs once after Angular renders the component view.

```ts
@ViewChild('input') input!: ElementRef;

ngAfterViewInit() {
  this.input.nativeElement.focus();
}
```

### Use Cases

- Accessing `@ViewChild`
- DOM manipulation
- Third-party libraries

### Example

```ts
@ViewChild(MatPaginator)
paginator!: MatPaginator;

ngAfterViewInit() {
  this.dataSource.paginator = this.paginator;
}
```

Common with Angular Material tables.

---

## 5. ngOnDestroy

Runs before Angular destroys the component.

```ts
ngOnDestroy() {
  this.subscription.unsubscribe();
}
```

### Use Cases

- Cleanup subscriptions
- Stop timers
- Remove event listeners
- Close WebSockets

### Example

```ts
private destroy$ = new Subject<void>();

ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}
```

---

# Content Projection Hooks

## What is Content Projection?

Parent HTML projected into child component using:

```html
<app-card>
  <h2>Hello</h2>
</app-card>
```

Child component:

```html
<ng-content></ng-content>
```

---

## 6. ngAfterContentInit

Runs once after projected content is initialized.

```ts
ngAfterContentInit() {}
```

### Use Cases

- Reading projected content
- Building reusable UI components

### Example

```html
<app-card>
  <h2>Special Offer</h2>
</app-card>
```

Angular projects `<h2>` into `<ng-content>` first, then executes this hook.

---

## 7. ngAfterContentChecked

Runs whenever projected content is checked.

```ts
ngAfterContentChecked() {}
```

### Use Cases

- Advanced content projection scenarios

### Interview Note

Rarely used in business applications.

---

# View Hooks

## What is Component View?

The component's own template.

Example:

```html
<input #searchBox /> <button>Search</button>
```

Everything inside the component template (here template means html file not ng-template) belongs to its view.

---

## 8. ngAfterViewChecked

Runs whenever Angular checks the component view.

```ts
ngAfterViewChecked() {}
```

### Use Cases

Very rare.

### Avoid

Can run many times and impact performance.

---

# Advanced Hook

## 9. ngDoCheck

Runs during every change detection cycle.

```ts
ngDoCheck() {}
```

### Use Cases

- Custom change detection

### Example

```ts
ngDoCheck() {
  console.log('Change detection running');
}
```

### Avoid

Usually unnecessary.

Can easily cause performance issues.

---

# Content vs View

## Content

Projected from parent using:

```html
<ng-content></ng-content>
```

Example:

```html
<app-card>
  <h2>Hello</h2>
</app-card>
```

Hooks:

```txt
ngAfterContentInit
ngAfterContentChecked
```

---

## View

Component's own template.

Example:

```html
<input #searchBox /> <button>Search</button>
```

Hooks:

```txt
ngAfterViewInit
ngAfterViewChecked
```

---

# Most Common Interview Questions

## Constructor vs ngOnInit

Constructor:

```txt
JavaScript object creation
Dependency Injection
```

ngOnInit:

```txt
Angular lifecycle hook
Inputs available
Initialization logic
API calls
```

---

## When should ngOnChanges be used?

When component behavior depends on changing `@Input()` values.

Example:

```ts
@Input() userId!: number;
```

Reload data when `userId` changes.

---

## When should ngAfterViewInit be used?

When DOM elements or `@ViewChild` references are required.

Examples:

- Angular Material paginator
- Chart.js
- Google Maps
- Input focus

---

## When should ngOnDestroy be used?

For cleanup:

- RxJS subscriptions
- setInterval
- WebSocket connections
- DOM event listeners

---

# Interview Summary

```txt
Constructor        -> Dependency Injection
ngOnInit           -> Initial API calls & setup
ngOnChanges        -> React to Input changes
ngAfterViewInit    -> Access ViewChild / DOM
ngOnDestroy        -> Cleanup

Rare:
ngDoCheck
ngAfterContentInit
ngAfterContentChecked
ngAfterViewChecked
```

For most Angular applications, the hooks you will use regularly are:

Constructor
→ ngOnInit
→ ngOnChanges
→ ngAfterViewInit
→ ngOnDestroy
