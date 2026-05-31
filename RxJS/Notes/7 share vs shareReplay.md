# share() vs shareReplay()

## Why Do We Need Them?

By default, Observables are cold.

```ts
const user$ = this.http.get('/api/user');

user$.subscribe();
user$.subscribe();
```

Result:

```text
HTTP Request #1
HTTP Request #2
```

Each subscriber creates a new execution.

---

# share()

## Purpose

Convert a Cold Observable into a shared Hot Observable.

```ts
const user$ = this.http.get('/api/user').pipe(
  share()
);
```

Result:

```text
HTTP Request #1
Shared by all subscribers
```

Conceptually:

```text
Source
  ↓
Internal Subject
 ↙   ↘
A     B
```

---

## Important

Late subscribers do NOT receive old values.

Example:

```text
A subscribes
Value emitted
B subscribes
```

Result:

```text
A receives value
B misses value
```

---

## Use Cases

* Sharing HTTP requests
* Sharing WebSocket streams
* Sharing expensive computations

---

# shareReplay()

## Purpose

Share execution AND replay previous values.

```ts
const user$ = this.http.get('/api/user').pipe(
  shareReplay(1)
);
```

Conceptually:

```text
Source
  ↓
Replay Buffer
 ↙   ↘
A     B
```

---

## Example

```text
A subscribes
Value emitted
B subscribes later
```

Result:

```text
A receives value
B immediately receives latest value
```

---

## shareReplay(1)

```ts
shareReplay(1)
```

Stores:

```text
Last 1 emitted value
```

Example:

```text
Source emits:
1
2
3
```

Late subscriber receives:

```text
3
```

---

## shareReplay(2)

```ts
shareReplay(2)
```

Stores:

```text
Last 2 emitted values
```

Late subscriber receives:

```text
2
3
```

---

# Common Angular Use Case

Without shareReplay:

```ts
getUser() {
  return this.http.get('/api/user');
}
```

Component A:

```ts
this.userService.getUser().subscribe();
```

Component B:

```ts
this.userService.getUser().subscribe();
```

Result:

```text
2 HTTP requests
```

---

With shareReplay:

```ts
user$ = this.http.get('/api/user').pipe(
  shareReplay(1)
);
```

Components:

```ts
this.userService.user$.subscribe();
```

Result:

```text
1 HTTP request
Shared response
```

---

# Memory Consideration

share():

```text
No value cache
```

shareReplay():

```text
Caches previous values
```

Because of caching, use the smallest replay buffer needed.

Most Angular applications use:

```ts
shareReplay(1)
```

---

# Quick Comparison

| Feature                         | share() | shareReplay() |
| ------------------------------- | ------- | ------------- |
| Shared Execution                | ✅       | ✅             |
| Uses Subject Internally         | ✅       | ✅             |
| Replays Old Values              | ❌       | ✅             |
| Caches Values                   | ❌       | ✅             |
| Good for HTTP Caching           | ❌       | ✅             |
| Late Subscriber Gets Last Value | ❌       | ✅             |

---

# Interview Answer

## share()

```text
Converts a Cold Observable into a shared Hot Observable so multiple subscribers share a single execution.
```

## shareReplay()

```text
Provides shared execution and replays previous emissions to late subscribers.
```

## Easy Rule

```text
share()
=
Share Execution
```

```text
shareReplay()
=
Share Execution
+
Replay Previous Values
```

# Where Should `shareReplay(1)` Be Used?

* `shareReplay(1)` is commonly used to cache and share expensive streams such as HTTP requests.
* Avoid adding generic caching logic in low-level utility services.
* Different APIs often have different caching requirements.
* Some APIs should always fetch fresh data.
* Some APIs can be cached for the entire session.
* Some APIs may need cache expiration or manual refresh.
* A utility service does not know the business rules of each API.
* Caching decisions belong in feature/domain services where business meaning is understood.
* This keeps caching behavior explicit and easier to maintain.
* It also makes cache invalidation simpler.

Example:

```ts
user$ = this.http.get('/api/user').pipe(
  shareReplay(1)
);
```

Benefits:

* One HTTP request
* Multiple subscribers share the same response
* Prevents duplicate API calls
* Better performance
* Easier to reason about caching behavior

**Rule of thumb:** Use `shareReplay(1)` in feature services, not blindly in generic HTTP utility services.
