# Subject vs Multicast / Share in RxJS

## Unicast Observable

Normal observables are unicast.

Each subscription creates a new execution.

```ts
const obs$ = new Observable(() => {
  console.log('executed');
});

obs$.subscribe();
obs$.subscribe();
```

Output:

```txt
executed
executed
```

---

# Subject

Subject is both:
- Observable
- Observer

It allows multicasting.

```ts
const subject = new Subject();

subject.subscribe(v => console.log('A', v));
subject.subscribe(v => console.log('B', v));

subject.next(1);
```

Output:

```txt
A 1
B 1
```

One producer → many consumers.

---

# Why share / multicast Exists

Subject alone does NOT automatically share source execution.

Problem:

```ts
const data$ = http.get('/api');

data$.subscribe();
data$.subscribe();
```

This creates:
- 2 subscriptions
- 2 HTTP calls

---

# share()

```ts
const shared$ = data$.pipe(share());

shared$.subscribe();
shared$.subscribe();
```

Now:
- single source execution
- shared among subscribers

---

# Internally share() Uses Subject

Conceptually:

```ts
const subject = new Subject();

source.subscribe(subject);

return subject;
```

---

# Difference

| Thing | Purpose |
|---|---|
| Subject | Multicasting primitive |
| share / multicast | Convert unicast observable into shared execution |

---

# Important

Subject:
- distributes values

share():
- manages shared subscription lifecycle
- prevents duplicate execution
- internally uses Subject



# Subject vs share()

## Subject

A Subject is both:

- Observable
- Observer

It allows multicasting.

```ts
const subject = new Subject();

subject.subscribe(v => console.log('A', v));
subject.subscribe(v => console.log('B', v));

subject.next(1);
```

Output:

```text
A 1
B 1
```

One producer → many consumers.

---

## Problem with Normal Observables

```ts
const data$ = http.get('/api');

data$.subscribe();
data$.subscribe();
```

Creates:

```text
HTTP Request #1
HTTP Request #2
```

Observables are unicast by default.

---

## Using Subject for Sharing

```ts
const subject = new Subject();

http.get('/api').subscribe(subject);

subject.subscribe(...);
subject.subscribe(...);
```

Flow:

```text
HTTP Request #1
       ↓
    Subject
    ↙    ↘
   A      B
```

Only one HTTP request.

---

## share()

```ts
const data$ = http.get('/api').pipe(
  share()
);
```

`share()` internally uses a Subject to multicast a source Observable.

Benefits:

- One source execution
- Multiple subscribers
- No manual Subject wiring

---

## Interview Answer

### Subject

A multicast Observable that is both an Observer and an Observable.

### share()

Converts a cold/unicast Observable into a shared/multicast Observable by internally using a Subject.
