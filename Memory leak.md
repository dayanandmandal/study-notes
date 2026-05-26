# Memory Leaks in JavaScript & Angular Applications

## What is a Memory Leak?

A memory leak occurs when memory that is no longer needed is still being retained by the application, preventing JavaScript's Garbage Collector (GC) from freeing it.

This causes memory usage to continuously grow over time.

---

# Why Memory Leaks Are Dangerous

Memory leaks can cause:

- Slow UI rendering
- Laggy scrolling
- High RAM usage
- Increased CPU usage
- Browser tab crashes
- Application freezes
- Poor user experience

In Single Page Applications (SPA) like Angular apps, leaks are more dangerous because users may keep the app open for hours without refreshing the page.

---

# How JavaScript Memory Management Works

JavaScript automatically manages memory using a Garbage Collector (GC).

The Garbage Collector removes objects only when they become unreachable.

Example:

```js
let user = { name: "John" };

user = null;
```

Now the old object has no active references, so GC can remove it from memory.

---

# Important Interview Concept

## Memory Leak ≠ High Memory Usage

Using a lot of memory temporarily is normal.

A memory leak means:

> Memory keeps increasing unnecessarily and never gets released.

---

# Common Causes of Memory Leaks

## 1. Unremoved Event Listeners

### Problem

```js
button.addEventListener("click", handleClick);
```

If the DOM element is removed but the listener still exists, memory cannot be freed.

### Prevention

```js
button.removeEventListener("click", handleClick);
```

---

# 2. Uncleaned Timers

### Problem

```js
setInterval(() => {
  console.log("Running...");
}, 1000);
```

If not cleared, the callback continues running forever.

### Prevention

```js
const intervalId = setInterval(...);

clearInterval(intervalId);
```

---

# 3. RxJS Subscription Leaks (Very Important for Angular)

## Problem

```ts
this.userService.data$.subscribe(data => {
  console.log(data);
});
```

If the component gets destroyed but subscription remains active:
- memory leak occurs
- duplicate API calls/events may happen

---

## Prevention

### Option 1: unsubscribe()

```ts
subscription.unsubscribe();
```

---

### Option 2: takeUntil() (Recommended)

```ts
private destroy$ = new Subject<void>();

this.userService.data$
  .pipe(takeUntil(this.destroy$))
  .subscribe();

ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}
```

---

### Option 3: async pipe

Angular automatically unsubscribes.

```html
<div>{{ user$ | async }}</div>
```

---

# Senior Interview Question

## Does every Observable require unsubscribe?

### No.

Examples that usually complete automatically:
- HttpClient requests
- firstValueFrom()
- finite observables

Examples requiring cleanup:
- interval()
- fromEvent()
- Subject streams
- websocket streams
- store subscriptions

---

# 4. Detached DOM Nodes

## Problem

DOM removed visually but still referenced in JS.

```js
const element = document.querySelector(".card");
```

If stored globally or retained in closures:
- browser cannot clean memory

---

## Prevention

Remove references when unused.

```js
element = null;
```

---

# 5. Closures Retaining Large Objects

## Problem

```js
function outer() {
  const hugeData = new Array(1000000);

  return function inner() {
    console.log("Hello");
  };
}
```

`inner()` still references `hugeData`.

---

## Prevention

Avoid unnecessarily capturing large objects inside closures.

---

# 6. Global Variables & Infinite Caches

## Problem

```js
window.cache = {};
```

If data keeps getting added forever:
- memory continuously grows

---

## Prevention

- limit cache size
- cleanup stale entries
- use WeakMap where appropriate

---

# 7. Memory Leaks from Third-Party Libraries

Examples:
- chart libraries
- editors
- map libraries
- websocket clients

Some libraries require manual destroy methods.

Example:

```js
chart.destroy();
editor.dispose();
```

---

# Angular-Specific Memory Leak Scenarios

## Common Angular Leak Sources

- RxJS subscriptions
- setInterval / setTimeout
- fromEvent()
- Route reuse
- Shared services storing stale data
- CDK overlays/dialogs not destroyed
- Large NgRx stores
- WebSocket streams
- Manual DOM manipulation

---

# Memory Leak Example in Angular

## Bad Example

```ts
ngOnInit() {
  interval(1000).subscribe(() => {
    console.log("Running");
  });
}
```

Every navigation creates another interval.

---

## Correct Example

```ts
private destroy$ = new Subject<void>();

ngOnInit() {
  interval(1000)
    .pipe(takeUntil(this.destroy$))
    .subscribe();
}

ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}
```

---

# How to Detect Memory Leaks

# 1. Chrome DevTools → Memory Tab

## Steps

1. Open DevTools (`F12`)
2. Go to `Memory` tab
3. Take Heap Snapshot
4. Perform repeated actions/navigation
5. Take another snapshot
6. Compare retained objects

---

# 2. Performance Monitor

Chrome:

```text
More Tools → Performance Monitor
```

Watch:
- JS Heap Size
- DOM Nodes
- Event Listeners

If continuously increasing:
- possible leak

---

# 3. Observe Symptoms

Signs:
- increasing RAM usage
- app slows after prolonged usage
- duplicate API calls
- old components still reacting
- UI freezes

---

# Important Interview Questions

# Q1. Does page refresh clear memory leaks?

## Answer

Yes, usually.

Refreshing the page destroys:
- JS execution context
- DOM
- event listeners
- timers
- memory heap

This resets the application state completely.

---

# Q2. Why are memory leaks more dangerous in Angular SPAs?

Because SPA navigation does not reload the page.

Leaked objects remain alive across route changes.

---

# Q3. What is Garbage Collection?

Garbage Collection is the automatic process of reclaiming memory occupied by unreachable objects.

---

# Q4. What are "reachable objects"?

Objects still accessible through references:
- variables
- closures
- event listeners
- timers
- global objects
- DOM references

---

# Q5. Difference Between Shallow Memory & Retained Memory

## Shallow Memory
Memory occupied by the object itself.

## Retained Memory
Total memory kept alive because of that object.

Very important in heap snapshot analysis.

---

# Q6. What are WeakMap and WeakSet?

Weak references that allow garbage collection when object becomes unreachable.

Useful for:
- temporary metadata
- caches
- object tracking

---

# Q7. How does async pipe help prevent leaks?

Angular automatically subscribes and unsubscribes when component gets destroyed.

---

# Q8. Can circular references cause leaks?

Modern JS garbage collectors handle most circular references correctly.

Leaks happen when circular references remain reachable from roots like:
- window
- active listeners
- timers
- closures

---

# Senior-Level Best Practices

- Prefer async pipe where possible
- Use takeUntil for long-lived streams
- Avoid unnecessary global state
- Cleanup intervals/listeners in ngOnDestroy
- Destroy third-party library instances
- Profile memory in DevTools for complex apps
- Avoid storing large DOM references
- Keep services stateless when possible

---

# One-Line Interview Definition

> A memory leak occurs when unused objects remain referenced in memory, preventing garbage collection and causing increasing memory usage over time.F