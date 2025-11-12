
## **1️⃣ GWT Module Start (`Layout.onModuleLoad`)**

* **Entry point:** `Layout` implements `EntryPoint`, so `onModuleLoad()` is automatically called by GWT.
* Log: `Layout.onModuleLoad: start`
* **Purpose:** initialize your UI system and navigation framework.

---

## **2️⃣ Get the SlottedController**

```java
SlottedController slottedController = AppGinjector.instance.getSlottedController();
```

* **SlottedController** is fetched from the GIN dependency injector.

* **Role:** it manages:

  * Slots (containers for UI)
  * Places (representing pages/screens)
  * Activities (logic and widgets for each place)
  * Navigation & history

* Log: `Layout: got SlottedController: ...`

---

## **3️⃣ Set Default Place**

```java
slottedController.setDefaultPlace(new HomePlace());
```

* `HomePlace` is set as the **default page** for the app.

* **Effect:** triggers SlottedController to:

  * Determine which **Activity** to start (`HomeActivity`)
  * Determine **parent slot** for this place (`RootSlot`)

* Log: `Layout: default place set to HomePlace`

---

## **4️⃣ SlottedController Maps Place → Activity**

* **HomePlace** tells SlottedController:

  * `getParentSlot()` → `RootSlot` (main container)
  * `getActivity()` → `HomeActivity`
* SlottedController then calls `HomeActivity.start()` to create the UI.

---

## **5️⃣ HomeActivity Starts and Builds UI**

* Log: `HomeActivity.start(): BEGIN`

* **Actions:**

  1. UiBinder inflates GWT widgets for this activity.
  2. Header and Main slots are created as **tokens**:

     * Log: `Creating header token... header`
     * Log: `Creating main token... main`

* **Result:** internal data structure in SlottedController now knows:

  * `RootSlot` → contains `header` and `main` slots
  * HomeActivity UI is ready to be added to the DOM

* Log: `HomeActivity.start(): END`

---

## **6️⃣ Add Root Slot to DOM**

```java
RootLayoutPanel.get().add(rootSlot);
slottedController.setDisplay(rootSlot);
```

* The **rootSlot** is attached to the HTML DOM (browser screen).

* **Effect:** HomeActivity widgets (header + main) become visible.

* SlottedController now manages rendering inside **rootSlot** and child slots.

* Log: `Layout: rootSlot added and set as display`

---

## **7️⃣ Inject React Script**

```java
injectReactScript();
```

* Checks if running on **localhost** or production.

* Dynamically injects React JS bundle via `ScriptInjector`.

* Once script loads:

  * `window.reactScriptLoaded = true`
  * React can now render inside a GWT slot (usually the **main** slot)

* Log: `Layout: React script injected and ready (window.reactScriptLoaded=true)`

---

## **📌 Full Flow Summary:**

**Flow chart style:**

1. **Browser loads GWT module → Layout.onModuleLoad**
2. **SlottedController fetched** → manages navigation, slots, and activities
3. **Default place set → HomePlace**
4. **SlottedController maps place → activity** → HomeActivity
5. **HomeActivity.start()**

   * UiBinder inflates widgets
   * Creates child slots: header & main
6. **Root slot added to DOM** → GWT UI visible
7. **React script injected** → React app can mount inside main slot

---

### **📝 Key Connections**

| Component         | Connects to       | Purpose                                               |
| ----------------- | ----------------- | ----------------------------------------------------- |
| Layout            | SlottedController | Initializes navigation & slot system                  |
| SlottedController | RootSlot          | Manages which Place/Activity goes where               |
| HomePlace         | HomeActivity      | Place tells SlottedController which Activity to start |
| HomeActivity      | header/main slots | Creates actual UI widgets inside slots                |
| rootSlot          | DOM               | Makes HomeActivity UI visible                         |
| React script      | main slot         | Mounts React app dynamically inside GWT-managed slot  |

---

So **UI rendering** happens in **two phases**:

1. **GWT Phase:** HomeActivity builds header + main widgets → rootSlot added → visible in DOM.
2. **React Phase:** React JS script loads → React mounts inside a slot (usually main) → dynamic content can render inside GWT layout.

---

## **Step 0: Home Screen**

* **URL:** `index.html`
* **Activity:** `HomeActivity`
* **UI:** Green-bordered wrapper
* **Components:**

  * Static text: “This is a collection of different layout patterns…”
  * Hyperlinks: `headLeftbarButton` and `tabbedParentButton`

**Flow:**

1. User sees the home layout.
2. Click on **“Header with LeftBar menu”** triggers Slotted navigation to `HeaderPlace`.

---

## **Step 1: Load Header with LeftBar menu**

* **URL:** `index.html#header`
* **Activity Hierarchy:**

```
RootSlot
└─ HeaderActivity (blue slot)
   └─ HeaderSlot
      └─ LeftbarActivity (red slot inside leftbar)
         └─ LeftbarSlot
            └─ Content1Activity (default content)
```

* **UI:**

  * Header (blue border)
  * Leftbar (vertical menu, red border for child slot)
  * Content1Activity in red slot
* **Notes:**

  * Default child activity (`Content1Activity`) is loaded in `LeftbarSlot`.
  * Typing in Content1 textbox is preserved if you navigate away and return (because of `@CacheActivities`).

---

## **Step 2: Click a Leftbar Button → React Activity**

* **Button Clicked:** `react-helloreact`
* **URL:** `index.html#header/leftbar/helloreactplacer`
* **Activity Changes:**

  * LeftbarSlot swaps `Content1Activity` → `HelloReactActivityR`.
  * Leftbar itself is unchanged. Header remains.
* **UI:**

  * Leftbar shows menu; selected button highlighted.
  * Content area (red border) shows:

    * Text: “Say Hello to React”
    * Button: `[Call React function]`
    * Container: `<div id="helloreact-container"></div>`

**Flow:**

1. SlottedController swaps activity in `LeftbarSlot`.
2. New activity (`HelloReactActivityR`) attaches its UI to the slot.
3. React placeholder (`helloreact-container`) is ready for React injection.

---

## **Step 3: Click `[Call React function]` Button**

* **Action:** GWT triggers React to render inside `<div id="helloreact-container">`
* **UI Changes:**

  * Gray/dimmed backdrop appears (overlay).
  * Centered modal box appears (title: Hello, message line, buttons).
* **Content:**

  * Initially: “Got Parameter A from GWT”
  * Two buttons:

    1. `[CALL GWT FUNCTION FROM REACT AND GET SYSTEM TIME]`
    2. `[CLOSE]`

**Flow:**

1. React renders the modal dynamically inside the container.
2. Overlay ensures user focus remains on modal.
3. Layout outside modal (Header + Leftbar + Content) remains intact.

---

## **Step 4: Click `[CALL GWT FUNCTION FROM REACT AND GET SYSTEM TIME]`**

* **Action:** React calls **GWT method** via JSNI/JsInterop.
* **GWT Response:** System time in milliseconds is computed and returned.
* **UI Update:**

  * Modal text line updated:

    ```
    GWT sent System Time: 1762867314298 ms
    ```
  * Buttons and modal layout remain unchanged.
* **Flow:**

  1. React component receives value from GWT callback.
  2. React updates DOM inside modal (no full page re-render).
  3. Underlying GWT Slotted layout is untouched (Header, Leftbar, main content remain as is).

---

## **Step 5: Overall Observations / Flow Summary**

1. **Activity & Slot Hierarchy** (initially):

```
RootSlot
└─ HeaderActivity
   └─ HeaderSlot
      └─ LeftbarActivity
         └─ LeftbarSlot
            ├─ Content1Activity (default)
            └─ HelloReactActivityR (after click)
```

2. **Navigation Flow**:

```
HomeActivity → HeaderPlace → LeftbarActivity → Content1Activity → HelloReactActivityR
```

3. **React Integration**:

* React UI lives **inside a GWT activity slot**.
* React modal is rendered in-place in `helloreact-container`.
* React calls back into GWT using JsInterop, updates content without changing Slotted hierarchy.

4. **UI Persistence / Caching**:

* Leftbar buttons highlight selected activity.
* Content activities maintain state (`@CacheActivities`) when swapped.
* Modal overlay does not interfere with underlying layout.

---

✅ **Key Takeaways**

* Slotted maintains **hierarchical slot-activity mapping**.
* **Parent slots are persistent**; children can be swapped dynamically.
* React can be embedded in a child activity slot and interact bidirectionally with GWT.
* Modal overlays are completely **decoupled from the main Slotted hierarchy**, affecting only the React container.

---
