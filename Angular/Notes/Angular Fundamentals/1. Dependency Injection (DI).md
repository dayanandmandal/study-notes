# Angular Dependency Injection (DI) - Interview Notes (5+ Years)

## What is Dependency Injection?

Dependency Injection (DI) is a design pattern where Angular creates and supplies dependencies instead of components creating them manually.

Without DI:

```ts
const userService = new UserService();
```

With DI:

```ts
constructor(private userService: UserService) {}
```

Angular is responsible for creating and managing the service lifecycle.

---

# Why DI?

## 1. Centralized Object Creation

Components do not create dependencies.

```ts
constructor(private apiService: ApiService) {}
```

If `ApiService` later requires `HttpClient`, Angular handles it automatically.

**Interview Answer:**

> Angular's injector is responsible for creating dependencies, which keeps component code clean and maintainable.

---

## 2. Loose Coupling

Components depend on contracts instead of concrete implementations.

```ts
constructor(private notificationService: NotificationService) {}
```

The implementation can change without affecting the component.

**Example:**

Today:

```ts
ToastNotificationService;
```

Tomorrow:

```ts
BrowserNotificationService;
```

Component code remains unchanged.

## DI and Loose Coupling Example

Many developers hear "DI reduces coupling" but don't immediately see how.

### Without Abstraction

Component depends on a concrete implementation:

```ts
constructor(
  private notificationService: ToastNotificationService
) {}
```

Later, if the application switches to:

```ts
BrowserNotificationService;
```

the component must be changed:

```ts
constructor(
  private notificationService: BrowserNotificationService
) {}
```

The component is tightly coupled to a specific implementation.

---

### With Abstraction

Create a contract:

```ts
export abstract class NotificationService {
  abstract notify(message: string): void;
}
```

Implementation #1:

```ts
@Injectable()
export class ToastNotificationService extends NotificationService {
  notify(message: string) {
    // show toast
  }
}
```

Component:

```ts
constructor(
  private notificationService: NotificationService
) {}

save() {
  this.notificationService.notify('Saved successfully');
}
```

Provider:

```ts
providers: [
  {
    provide: NotificationService,
    useClass: ToastNotificationService,
  },
];
```

---

### Switching Implementations

New implementation:

```ts
@Injectable()
export class BrowserNotificationService extends NotificationService {
  notify(message: string) {
    new Notification(message);
  }
}
```

Only the provider changes:

```ts
providers: [
  {
    provide: NotificationService,
    useClass: BrowserNotificationService,
  },
];
```

Component code remains unchanged:

```ts
this.notificationService.notify("Saved successfully");
```

---

### Interview Takeaway

DI alone does not automatically reduce coupling.

The real benefit comes when components depend on abstractions (interfaces, abstract classes, InjectionTokens) instead of concrete implementations.

This allows implementations to be swapped through Angular providers without changing component logic.

DI reduces coupling because components depend on contracts rather than creating dependencies directly. In practice, many Angular applications inject concrete services, but Angular's provider system allows implementations to be swapped without changing component logic when abstractions are used.

---

## 3. Easier Testing

Dependencies can be replaced with mocks.

```ts
providers: [{ provide: UserService, useClass: MockUserService }];
```

No real HTTP calls are made during tests.

---

## 4. Better Reusability

Components can work with different implementations.

```ts
constructor(private storageService: StorageService) {}
```

One application may use LocalStorage while another uses SessionStorage.

---

## 5. Shared State

Services can share data between unrelated components.

Example:

```ts
AuthService;
```

Stores:

```ts
currentUser;
```

Navbar, Dashboard and Profile components all use the same data.

---

# Providers

A provider tells Angular how to create a dependency.

```ts
@Injectable({
  providedIn: "root",
})
export class UserService {}
```

Angular registers the service with the Root Injector.

---

## Provider Types

### Root Provider

```ts
@Injectable({
  providedIn: "root",
})
export class UserService {}
```

- Single instance
- Available everywhere
- Tree-shakable: Tree-shakable means unused code can be removed from the final production bundle. Services registered using providedIn: 'root' are tree-shakable because Angular can determine whether they are actually injected anywhere. If a service is never used, it is excluded from the production bundle, helping reduce bundle size.

Most common approach.

---

### Component Provider

```ts
@Component({
  providers: [UserService],
})
export class TodoComponent {}
```

Creates a new service instance for that component subtree.

---

### Custom Provider

```ts
providers: [{ provide: UserService, useClass: MockUserService }];
```

Useful for testing and advanced DI scenarios.

---

# Singleton Services

A singleton service has only one instance across the application.

```ts
@Injectable({
  providedIn: "root",
})
export class AuthService {}
```

All components receive the same instance.

```txt
Navbar
   \
Dashboard -----> AuthService
   /
Profile
```

---

## Common Singleton Services

```txt
AuthService
UserService
ThemeService
ApiService
ConfigService
```

---

## When To Use Singleton Services

Use when data should be shared globally.

Examples:

- Logged-in user
- Theme
- Feature flags
- Configuration
- Application settings

---

# Injection Hierarchy

Angular uses a hierarchy of injectors.

```txt
Root Injector
    │
    ├── AppComponent
    │
    └── TodoComponent
          │
          └── TodoDetailsComponent
```

---

## Dependency Resolution Process

When Angular sees:

```ts
constructor(private userService: UserService) {}
```

It searches:

```txt
1. Current component injector
2. Parent component injector
3. Root injector
```

The first provider found is used.

---

## Rule: Nearest Provider Wins

Example:

```txt
Root Injector
    │
    └── UserService (#1)

TodoComponent Injector
    │
    └── UserService (#2)
```

```ts
@Component({
  providers: [UserService], // this just tell to create new instance and all the child will use this instance instead of root
})
export class TodoComponent {}
```

Result:

```txt
TodoComponent         -> UserService (#2)
TodoDetailsComponent  -> UserService (#2)
NotesComponent        -> UserService (#1)
```

Angular always chooses the closest provider.

---

# Component-Level Providers

```ts
@Component({
  providers: [WizardStateService],
})
export class WizardComponent {}
```

Purpose:

- Isolate state
- Avoid sharing data globally
- Create independent service instances

---

## Real Interview Example

Suppose multiple tabs contain independent forms.

Each form should maintain its own state.

```ts
@Component({
  providers: [FormStateService]
})
```

Each form gets its own service instance.

Without component providers, all forms would share the same state.

---

# DI and Child Routes

For Angular DI, a child component is any component created under a parent component, whether it appears directly in the parent's template or is rendered inside the parent's `<router-outlet>`. Angular resolves dependencies through this component/injector hierarchy.

---

# providedIn Options

## Root (Most Common)

```ts
@Injectable({
  providedIn: 'root'
})
```

One application-wide instance.

---

## Platform

```ts
@Injectable({
  providedIn: 'platform'
})
```

Shared across multiple Angular applications running on the same page.

Rarely used.

## `providedIn: 'platform'`

Most Angular applications use:

```ts
@Injectable({
  providedIn: "root",
})
export class UserService {}
```

This creates one service instance per Angular application.

---

### Why does `platform` exist?

Angular supports running multiple Angular applications on the same page.

Example:

```ts
bootstrapApplication(App1Component);
bootstrapApplication(App2Component);
```

Hierarchy:

```txt
Platform Injector
      │
 ┌────┴────┐
 │         │
App1     App2
```

---

### `providedIn: 'root'`

Each application gets its own service instance.

```ts
@Injectable({
  providedIn: "root",
})
export class UserService {}
```

Result:

```txt
App1 → UserService #1
App2 → UserService #2
```

---

### `providedIn: 'platform'`

All Angular applications share the same service instance.

```ts
@Injectable({
  providedIn: "platform",
})
export class GlobalConfigService {}
```

Result:

```txt
App1 → GlobalConfigService #1
App2 → GlobalConfigService #1
```

---

### Common Use Cases

#### Micro Frontends

```txt
Shell Application
    │
    ├── Customer App
    ├── Orders App
    └── Reports App
```

Multiple Angular applications running together.

#### Angular Elements (Web Components)

```html
<customer-widget></customer-widget> <report-widget></report-widget>
```

Each widget can be a separate Angular application.

#### Enterprise Portals

Large applications may load multiple Angular apps into a single shell.

---

### Angular Library ≠ Angular Application

A library does **not** create a new Angular application.

```txt
Main Angular App
     │
     ├── Shared UI Library
     ├── Auth Library
     └── Core Library
```

Still:

```txt
Platform Injector
      │
Root Injector
      │
One Angular App
```

Libraries are simply code reused inside the same application.

---

### Interview Summary

- `providedIn: 'root'` → one instance per Angular application.
- `providedIn: 'platform'` → one instance shared across Angular applications on the same page.
- Mostly used in micro-frontends and Angular Elements.
- Rarely needed in normal Angular projects.
- Angular libraries do not create separate Angular applications.

---

## Any

```ts
@Injectable({
  providedIn: 'any'
})
```

Creates separate instances for different lazy-loaded modules.

Rarely used in modern Angular applications.

---

# Senior-Level Interview Points

### Why prefer `providedIn: 'root'`?

- Tree-shakable
- Better performance
- Avoids unnecessary module providers
- Recommended by Angular

---

### When would you use component providers?

When service state should be isolated.

Examples:

- Wizard forms
- Tab-specific state
- Independent editors
- Temporary workflows

---

### Difference Between Singleton and Component Provider?

Singleton:

```ts
providedIn: "root";
```

One instance for entire application.

Component Provider:

```ts
providers: [UserService];
```

One instance per component subtree.

---

# Quick Interview Summary

- DI allows Angular to create dependencies.
- Providers tell Angular how to create dependencies.
- `providedIn: 'root'` creates singleton services.
- Angular resolves dependencies through injector hierarchy.
- Angular searches upward until a provider is found.
- Nearest provider wins.
- Component providers create isolated service instances.
- Use singleton services for shared global state.
- Use component providers for isolated local state.
- DI follows the component tree, not the routing tree.
  - A child route gets the parent's service only if:
    1. The child is rendered inside the parent's component tree.
    2. The child does not provide its own instance.
    3. Angular DI is deterministic. Dependencies are resolved by walking up the injector tree from the current component. The first matching provider is used. This "nearest provider wins" rule allows local overrides while still supporting application-wide singleton services through the Root Injector.
       Nearest provider always wins.
