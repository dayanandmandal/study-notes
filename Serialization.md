# 🧠 What does “state must be serializable” mean?

It means your application state should be safe to convert into JSON and store or restore later without losing meaning or breaking the app.

In simple terms:
👉 If you save it today, you should be able to rebuild the same state tomorrow.

---

## 🔄 How serialization works

Serialization = converting your state into a string format (usually JSON):

```js
const saved = JSON.stringify(state);
```

## ❌ What breaks serialization

These should NOT be inside state:

### 1. Functions

```js id="mdfix2"
state.timer.tick = () => {}; // ❌ cannot be saved
```

### 2. DOM elements

```state.timer.element = document.querySelector(".timer") // ❌ cannot be saved or restored

```

### 3. Runtime-only values (like intervals, timers, IDs)

```
state.timer.intervalId = setInterval(() => {}, 1000) // ❌ cannot be restored after refresh
```
