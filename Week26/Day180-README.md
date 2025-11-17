# 🧩 GWT + React — Slotted Layout Flow

This document explains **how the UI loads and works** in your **GWT + Slotted + React** application:

* How **GWT module** starts
* How **SlottedController / Places / Activities / Slots** interact
* How **React** is injected and talks with **GWT**
* Full **URL → Activity → UI** flow

---

## 1️⃣ GWT Module Startup — `Layout.onModuleLoad()`

**Entry point:**

* Class: `Layout` implements `EntryPoint`
* Method: `onModuleLoad()` is automatically called by GWT
* Log: `Layout.onModuleLoad: start`

**Responsibilities:**

* Initialize **navigation** (`SlottedController`)
* Set **default place** (`HomePlace`)
* Attach **root slot** to the DOM
* Inject **React script**

---

## 2️⃣ Get the `SlottedController`

```java
SlottedController slottedController = AppGinjector.instance.getSlottedController();
```

**Source:** via **GIN** injection (`AppGinjector`).

**`SlottedController` manages:**

* **Slots** → containers for activities and widgets
* **Places** → pages/routes (`HomePlace`, `HeaderPlace`, etc.)
* **Activities** → UI + logic (`HomeActivity`, `HeaderActivity`, etc.)
* **Navigation & History** → browser back/forward support

📜 Log: `Layout: got SlottedController: ...`

---

## 3️⃣ Set Default Place — `HomePlace`

```java
slottedController.setDefaultPlace(new HomePlace());
```

* Sets **`HomePlace`** as the **initial screen**.
* `HomePlace` → maps to `HomeActivity`.

When app starts:

* Slotted resolves:

  * **Parent slot:** `RootSlot`
  * **Activity:** `HomeActivity`
* Calls: `HomeActivity.start()`

📜 Log: `Layout: default place set to HomePlace`

---

## 4️⃣ Place → Activity Mapping

For `HomePlace`:

* `getParentSlot()` → `RootSlot`
* `getActivity()` → `HomeActivity`

Then:

```java
HomeActivity.start(AcceptsOneWidget panel, EventBus eventBus)
```

Inside `HomeActivity.start()`:

* Uses **UiBinder** to build **Home screen widgets**.
* Prepares internal slots (like **header/main**) for later use.
* Registers itself inside Slotted’s structures.

📜 Logs:

* `HomeActivity.start(): BEGIN`
* `Creating header token... header`
* `Creating main token... main`
* `HomeActivity.start(): END`

---

## 5️⃣ Root Slot → DOM

```java
RootLayoutPanel.get().add(rootSlot);
slottedController.setDisplay(rootSlot);
```

* `rootSlot` is attached to the **browser DOM**.
* GWT UI is now **visible** (HomeActivity with content).

📜 Log: `Layout: rootSlot added and set as display`

---

## 6️⃣ Inject React Script

```java
injectReactScript();
```

**Behavior:**

* Detects **environment** (localhost vs production).

* Performs **`ScriptInjector`** of React bundle file.

* On load:

  ```js
  window.reactScriptLoaded = true;
  ```

* After this, React can render inside GWT via `<div>` containers.

📜 Log: `Layout: React script injected and ready (window.reactScriptLoaded=true)`

---

## 7️⃣ High-Level Flow (GWT + React)

```text
Browser loads GWT module
    ↓
Layout.onModuleLoad()
    ↓
SlottedController fetched
    ↓
Default place set → HomePlace
    ↓
HomeActivity.start()
    ↓
rootSlot attached to DOM
    ↓
React script injected
```

GWT handles **layout/navigation** → React handles **dynamic modal and UI piece**.

---

## 8️⃣ Activity & Slot Hierarchy (Header + Leftbar + Content)

### Step 0: Home Screen

**URL:** `index.html`
**Activity:** `HomeActivity`
**File:** `HomeActivity.ui.xml`

**UI:**

* Green wrapper (page border)
* Text: “This is a collection of different layout patterns…”
* Links:

  * **Header with LeftBar menu**
  * **Tabbed Parent**

Clicking **Header with LeftBar menu** navigates to `HeaderPlace`.

---

### Step 1: Header with LeftBar

**URL:** `index.html#header`

**Hierarchy:**

```text
RootSlot
└─ HeaderActivity
   └─ HeaderSlot
      └─ LeftbarActivity
         └─ LeftbarSlot
            └─ Content1Activity (default)
```

**Files:**

* `HeaderActivity.ui.xml` → defines header + top layout
* `LeftbarActivity.ui.xml` → left side menu panel with one slot
* `Content1Activity.ui.xml` → default main content in `LeftbarSlot`

**UI:**

* Header (top, blue border)
* Leftbar with buttons
* **Content1Activity** in main content area:

  * TextBox (state cached via `@CacheActivities`)

---

### Step 2: Click Leftbar Button → React Place

**Clicked:** `react-helloreact`
**URL:** `index.html#header/leftbar/helloreactplacer`

**Hierarchy after click:**

```text
RootSlot
└─ HeaderActivity
   └─ HeaderSlot
      └─ LeftbarActivity
         └─ LeftbarSlot
            └─ HelloReactActivityR
```

**Files:**

* `HelloReactPlaceR.java` → parent slot = `LeftbarSlot`
* `HelloReactActivityR.java` → GWT activity
* `HelloReactActivityR.ui.xml`:

  * Label: “Say Hello to React”
  * GWT button: `[Call React function]`
  * `<div id="helloreact-container"></div>` → React mount point

**Flow:**

* Slotted swaps `Content1Activity` → `HelloReactActivityR`.
* Header + Leftbar **stay the same**.

---

### Step 3: Click `[Call React function]` (GWT → React)

**Action:**

```java
ReactInterop.renderHelloReact(" Got Parameter A from  GWT");
```

**What happens:**

1. `ReactInterop.renderHelloReact()` is a JS interop function.
2. It finds `helloreact-container` div.
3. Creates React root and renders `<HelloReactView />`.
4. React shows a **modal** with:

   * Text: `Got Parameter A from GWT`
   * Buttons:

     * `[CALL GWT FUNCTION FROM REACT AND GET SYSTEM TIME]`
     * `[CLOSE]`

**UI:**

* Screen behind gets **dimmed**.
* Centered modal appears.
* Header & Leftbar remain visible underneath.

---

### Step 4: React Calls Back into GWT

**Button clicked in React:**

> `CALL GWT FUNCTION FROM REACT AND GET SYSTEM TIME`

**React code (conceptual):**

```js
const timeMillis = window.GwtInterop.getSystemTimeMillis();
setMessage(`GWT sent System Time: ${timeMillis} ms`);
```

**Flow:**

1. React calls `window.GwtInterop.getSystemTimeMillis()` → bridged to a GWT method.
2. GWT returns **system time in ms**.
3. React updates its state → line updates to:

   ```text
   GWT sent System Time: 1762867314298 ms
   ```

No GWT re-render, only React DOM update inside `helloreact-container`.

---

### Step 5: Close Modal

* `[CLOSE]` button in React:

  * Calls `onClose()`
  * Unmounts React root from container
  * Resets modal state

* Underlying **GWT layout remains intact**:

  * HeaderActivity
  * LeftbarActivity
  * HelloReactActivityR

---

## 9️⃣ Overall Navigation & Interaction Summary

### Activity & Slot Tree (Conceptual)

```text
RootSlot
└─ HomeActivity (initial)
   └ (navigate to header)
RootSlot
└─ HeaderActivity
   └ HeaderSlot
      └ LeftbarActivity
         └ LeftbarSlot
            ├─ Content1Activity (default)
            └─ HelloReactActivityR (after user clicks)
```

### Navigation Flow

```text
HomeActivity
   → HeaderPlace
   → LeftbarActivity (default: Content1Activity)
   → HelloReactPlaceR (react-helloreact)
   → React modal inside HelloReactActivityR
```

---

## 🔑 Key Concepts / Takeaways

| Concept                                   | Role                                                             |
| ----------------------------------------- | ---------------------------------------------------------------- |
| **Layout.onModuleLoad**                   | Starts GWT app, wires SlottedController, root slot, React script |
| **SlottedController**                     | Owns **slots, places, activities, navigation, history**          |
| **HomePlace / HomeActivity**              | Initial screen with links                                        |
| **Header / Leftbar / Content Activities** | Define structured layout via nested slots                        |
| **HelloReactActivityR**                   | GWT wrapper for React content, provides `<div>` for mounting     |
| **ReactInterop**                          | Bridge from GWT → React (`renderHelloReact`)                     |
| **GwtInterop**                            | Bridge from React → GWT (`getSystemTimeMillis`)                  |
| **@CacheActivities**                      | Caches GWT activities so user input isn't lost on navigation     |

---

## 🧠 Mental Model

* **GWT = Layout + Navigation + Slots**
* **Slotted = Hierarchical activity/slot management**
* **React = Dynamic UI inside specific GWT slots**
* **Interop = Glue that lets each side call into the other**

GWT builds the **house structure** (rooms and doors).
React is a **fancy device** you plug into one of the rooms.
JSInterop is the **cable** connecting them.

---
