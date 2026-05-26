# 🧠 Runtime Effects

## What are runtime effects?

Runtime effects are things that actively run while the application is open.

They exist only in memory during execution and disappear when:

- page refreshes
- app closes
- browser restarts

They are NOT part of persistent application state.

---

# ✅ Common runtime effects

## Timers

```js
setInterval(...)
setTimeout(...)
```

## Event listeners

```js
button.addEventListener("click", ...)
```

## Network / live connections

```js
websocket.connect(...)
```

## Audio / video playback

```js
audio.play();
```

## Animations

```js
requestAnimationFrame(...)
```

---

# ❌ Runtime effects should NOT be stored in state

Bad example:

```js
state.timer.intervalId = setInterval(...)
```

Why this is wrong:

- interval IDs are runtime-only
- cannot be serialized safely
- disappear after refresh
- meaningless when restored from localStorage

---

# ✅ What SHOULD be stored in state

Only plain serializable data:

```js
state.timer = {
  status: "running",
  startTimestamp: new Date(),
  durationLeft: 1200,
};
```

This data can be:

- persisted
- restored
- debugged
- serialized using JSON

---

# 🧠 Runtime vs State

## Persistent state

Stores application data.

Example:

```js
status;
theme;
todos;
startTimestamp;
durationLeft;
```

Usually stored in:

- localStorage
- database
- server
- JSON

---

## Runtime effects

Controls active application behavior.

Example:

```js
setInterval
event listeners
animations
audio playback
```

Usually recreated during:

- app initialization
- component mount
- setup functions

---

# 🔥 Important architecture rule

```txt
Persistent storage restores DATA
Setup functions restore RUNTIME EFFECTS
```

Example:

1. localStorage restores timer status
2. app checks if timer is running
3. app recreates setInterval

---

# ✅ Good architecture flow

## 1. Update state

```js
setTimerStatus(state, TIMER_STATUS.RUNNING);
```

## 2. Setup runtime effect

```js
timerIntervalId = setInterval(...)
```

## 3. Render UI

```js
render();
```

---

# 🚫 Common mistake

Mixing:

- state
- runtime effects
- rendering

inside one function.

Bad:

```js
timerToggle() {
  update state
  manipulate DOM
  create interval
  render UI
}
```

Better:

```js
1. update state
2. setup/cleanup runtime effects
3. render
```

---

# 🧠 Mental model

```txt
📦 State = saved snapshot of app
⚙️ Runtime effects = live running behavior
🎨 Render = visual representation of state
```
