# Observer vs Observable vs Subject

## Observable

Observable produces values.

```ts
const obs$ = of(1, 2, 3);
```

Consumers can subscribe:

```ts
obs$.subscribe(console.log);
```

But cannot emit values:

```ts
obs$.next(4); // ❌ Error
```

Observable = Producer

---

## Observer

Observer consumes values.

```ts
const observer = {
  next: value => console.log(value),
  error: err => console.error(err),
  complete: () => console.log('done')
};
```

Observer = Consumer

---

## Subject

A Subject is both:

- Observable
- Observer

It can:

```ts
subject.subscribe(...)
subject.next(...)
```

Subject = Producer + Consumer

---

## Why is Subject an Observer?

Because it implements:

```ts
subject.next(...)
subject.error(...)
subject.complete(...)
```

This allows:

```ts
source$.subscribe(subject);
```

Conceptually:

```text
Source Observable
        ↓
      Subject
     ↙      ↘
Subscriber  Subscriber
```

The Subject receives values from the source and forwards them to multiple subscribers.

---

## Why Can't Observable Have next()?

Imagine:

```ts
const data$ = http.get('/api');
```

If Observables exposed `next()`:

```ts
data$.next('fake data');
```

Anyone could inject values into the stream.

RxJS keeps the producer side private.

Only the Observable implementation can call:

```ts
observer.next(...)
```

Consumers can only subscribe.

---

## Interview Summary

```text
Observable = Producer

Observer = Consumer

Subject = Observable + Observer
```

```text
Observable -> subscribe()

Subject -> subscribe() + next()
```
