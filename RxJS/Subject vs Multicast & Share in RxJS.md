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