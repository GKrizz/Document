
## **1. Entry Point: MainPresenter.java**

In your `MainPresenter.java`, the flow starts by checking URL parameters:

```java
String r_currentMedIntegration = Window.Location.getParameter("r_currentMedIntegration") == null ? "" : Window.Location.getParameter("r_currentMedIntegration");
```

* This reads a URL query parameter `r_currentMedIntegration`.
* If this parameter is `"true"`, the app decides to render **R_CurrentMedRootPlace** using the **SlottedController**.

Example URL:

```
http://example.com?currentMedIntegration=true&r_currentMedIntegration=true
```

Then it sets up SlottedController:

```java
slottedController.setActivityMapper(mainActivityMapper);
slottedController.setDefaultPlace(new R_CurrentMedRootPlace(bean));
slottedController.setDisplay(view.getMainDisplayPanel());
setIsSlotted(true);
```

**What’s happening here:**

1. `mainActivityMapper` will map a **Place** to an **Activity**.
2. The default place is `R_CurrentMedRootPlace(bean)` (constructed with a `ChartVariablesBean`).
3. The main panel where the content should render is set: `view.getMainDisplayPanel()`.
4. The Slotted framework is activated: `setIsSlotted(true)`.

✅ **Step 1:** URL parameter → Decide to use `R_CurrentMedRootPlace` → SlottedController default place set.

---

## **2. Place Mapping: DesktopMainActivityMapper.java**

When `SlottedController` starts, it asks `DesktopMainActivityMapper` to give the **Activity** corresponding to the **Place**:

```java
else if (place instanceof CurrentMedPlace || ... || place instanceof R_CurrentMedRootPlace) {
    GWT.runAsync(new RunAsyncCallback() {
        public void onSuccess() {
            Activity activity = null;
            if(place instanceof R_CurrentMedRootPlace) {
                activity = r_currentMedicationActivityProvider.get().withPlace((R_CurrentMedRootPlace) place);
            }
            ...
            callback.onSuccess(activity);
        }
    });
}
```

**What’s happening here:**

1. It checks if the place is `R_CurrentMedRootPlace`.
2. If yes, it uses `r_currentMedicationActivityProvider` (dependency injection) to get a **new `R_CurrentMedActivity` instance**.
3. Calls `withPlace(R_CurrentMedRootPlace)` on the activity (currently, it just returns `this` in `R_CurrentMedActivity`).

✅ **Step 2:** `R_CurrentMedRootPlace` → Mapped to `R_CurrentMedActivity` → activity instance returned.

---

## **3. Activity Start: R_CurrentMedActivity.java**

When the activity starts, the `start()` method is called by Slotted framework:

```java
@Override
public void start(AcceptsOneWidget panel) {
    panel.setWidget(ourUiBinder.createAndBindUi(this));

    Scheduler.get().scheduleFixedDelay(() -> {
        if (isReactReady()) {
            try {
                ReactInterop.renderReactAxiosFetch("1");
            } catch (Exception e) {
                Window.alert(">>>>>"+e.getMessage()+"");
            }
            return false;
        }
        return true;
    }, 100);
}
```

**Step by step:**

1. **GWT UIBinder** sets up the activity’s layout:

   ```xml
   <g:HTMLPanel width="100%" height="100%">
     <div id="react-axiosfetch-container" width="100%" height="100%"></div>
   </g:HTMLPanel>
   ```

   * Creates a `<div>` container in the DOM with ID `react-axiosfetch-container`.
   * This is where React will render.

2. **Polling React readiness:**

   ```java
   private native boolean isReactReady() /*-{
       return !!$wnd.reactScriptLoaded;
   }-*/;
   ```

   * It checks if React script has loaded (via a global JS variable `reactScriptLoaded`).

3. **Render React Component:**

   ```java
   ReactInterop.renderReactAxiosFetch("1");
   ```

   * Calls a JS Interop method to render the React component inside `react-axiosfetch-container`.
   * `"1"` is passed as `id`. You could later modify this to pass actual patient/bean info if needed.

✅ **Step 3:** `R_CurrentMedActivity.start()` → GWT DOM setup → Poll React readiness → Call `renderReactAxiosFetch`.

---

## **4. ReactInterop → JS Interop**

`ReactInterop.java` bridges GWT Java code to React JS:

```java
@JsMethod(namespace = JsPackage.GLOBAL)
public static native void renderReactAxiosFetch(String customerId);
```

* This means when Java calls `ReactInterop.renderReactAxiosFetch("1")`, it calls the **global JS function**:

```javascript
window.renderReactAxiosFetch = renderReactAxiosFetch;
```

---

## **5. React Component: renderReactAxiosFetch.js**

```javascript
export function renderReactAxiosFetch(id) {
  let container = document.getElementById("react-axiosfetch-container");
  if (!container) {
    throw new Error("Container not found");
  }

  if (!root) {
    root = ReactDOM.createRoot(container);
  }

  root.render(<ReactAxiosFetchView id={id} />);
}
```

**Step by step:**

1. Find the container in the DOM (`react-axiosfetch-container`).
2. Create a React root (`ReactDOM.createRoot(container)`) if it doesn’t exist.
3. Render `ReactAxiosFetchView` React component inside this container.
4. `id` is passed as a prop to React: `<ReactAxiosFetchView id={id} />`.

✅ **Step 4:** JS container → React root → Render `ReactAxiosFetchView` with `id="1"`.

---

## **6. React Component: ReactAxiosFetchView.jsx**

This component will now **mount** in the container. From here:

* React takes control of rendering inside `#react-axiosfetch-container`.
* Any state, props, API calls, or nested React components are managed by React.
* If you need **dynamic patient data**, you can pass more info from `R_CurrentMedActivity` through `id` or other props.

---

## **7. Unmounting React Component**

When `R_CurrentMedActivity.onStop()` is called (if user navigates away):

```java
@Override 
public void onStop() {
    //ReactInterop.unmountCurrentMedications();
}
```

* Currently commented, but if called:

```java
ReactInterop.unmountReactAxiosFetch();
```

* Calls the JS function to unmount the React component and clean up:

```javascript
export function unmountReactAxiosFetch() {
  if (root) {
    root.unmount();
    root = null;
  }
}
```

✅ **Step 7:** Cleanup when activity stops.

---

## **8. Summary Flow Diagram**

| Step | From                         | To                        | What is passed                           |
| ---- | ---------------------------- | ------------------------- | ---------------------------------------- |
| 1    | URL / MainPresenter          | SlottedController         | URL param `r_currentMedIntegration=true` |
| 2    | SlottedController            | DesktopMainActivityMapper | `R_CurrentMedRootPlace(bean)`            |
| 3    | Mapper                       | R_CurrentMedActivity      | ChartVariablesBean (from place)          |
| 4    | R_CurrentMedActivity.start() | GWT UIBinder              | Sets up `#react-axiosfetch-container`    |
| 5    | Activity → JS                | ReactInterop              | Calls `renderReactAxiosFetch("1")`       |
| 6    | JS → React                   | ReactDOM                  | Mounts `<ReactAxiosFetchView id="1" />`  |
| 7    | Stop / Cleanup               | ReactInterop              | Calls `unmountReactAxiosFetch()`         |

---

## ✅ Key Points to Understand

1. **GWT "Place"** (`R_CurrentMedRootPlace`) is **navigation/state object**.
2. **Activity** (`R_CurrentMedActivity`) is **UI controller** for that place.
3. **UIBinder** builds the DOM shell; React renders inside a container in that shell.
4. **ReactInterop** is the bridge between Java (GWT) and JavaScript (React).
5. **React** manages its own component lifecycle after mounting.
6. **Data passing:** From URL → `Place` → `Activity` → `ReactInterop` → `React props`.

---


# **1️⃣ GWT Place / Route**

**File:** `R_CurrentMedRootPlace.java`

* Defines a **route/place** in GWT for Current Medications.
* Supports **token-based parameters** via `PlaceTokenizer` (e.g., patientId, chartId, encounterId).

**Key methods:**

```java
public static final ChartVariablesBean createFromToken(String token)
```

* Converts URL token like:

```
#CurrentMedPlace:patientId=376638&chartId=376973&encounterId=153968
```

* into a `ChartVariablesBean` containing all relevant patient/chart context.

**Slots:**

* `MappedSlottedPlace` uses `Slot` for parent/child slotting in GWT.
* `getParentSlot()` → `SlottedController.RootSlot`

  * This means this module is loaded at the **root of the main panel**.

✅ **Takeaway:** This is how GWT knows *which module to load* and *which patient/chart context to pass.*

---

# **2️⃣ GWT Activity**

**File:** `R_CurrentMedActivity.java`

* The **Activity** is triggered when the Place is loaded.
* Activity lifecycle methods:

  * `start(AcceptsOneWidget panel)` → Loads the UI via UiBinder and triggers React.
  * `onRefresh()` → Can refresh the module if needed.
  * `onStop()` → Could unmount React (currently commented out).

**UiBinder:** `R_CurrentMedActivity.ui.xml`

```xml
<div id="react-axiosfetch-container"></div>
```

* This `<div>` is the **container for React**.
* GWT renders this panel, React will mount into it.

**React Interop Call:**

```java
Scheduler.get().scheduleFixedDelay(() -> {
    if (isReactReady()) {
        ReactInterop.renderReactAxiosFetch("1");
        return false; // Stop polling
    }
    return true; // Keep checking
}, 100);
```

* Uses `Scheduler.get().scheduleFixedDelay()` → polls every 100ms until React JS is loaded.
* Calls `ReactInterop.renderReactAxiosFetch("1")` → mounts React component into the GWT container.

✅ **Takeaway:** GWT Activity handles lifecycle and **delegates UI rendering to React** using JS Interop.

---

# **3️⃣ JS Interop**

**File:** `ReactInterop.java`

* Exposes **React functions to GWT** via `@JsMethod`.

```java
@JsMethod(namespace = JsPackage.GLOBAL)
public static native void renderReactAxiosFetch(String customerId);

@JsMethod(namespace = JsPackage.GLOBAL)
public static native void unmountReactAxiosFetch();
```

* GWT calls these functions like **any native JS function**, React responds by rendering/unmounting.
* Other functions exist for memory routes and tabs.

✅ **Takeaway:** This is the **bridge** between GWT and React.

---

# **4️⃣ React Entry Point**

**File:** `index.js`

* Imports React rendering functions:

```js
import { renderReactAxiosFetch, unmountReactAxiosFetch } from './application/reactaxiosfetch/renderReactAxiosFetch';
```

* Attaches them to `window` so GWT can call:

```js
window.renderReactAxiosFetch = renderReactAxiosFetch;
window.unmountReactAxiosFetch = unmountReactAxiosFetch;
```

✅ **Takeaway:** This is how React exposes its **modules/functions globally** for GWT interop.

---

# **5️⃣ React Rendering**

**File:** `renderReactAxiosFetch.js`

```js
export function renderReactAxiosFetch(id) {
  let container = document.getElementById("react-axiosfetch-container");
  if (!container) throw new Error("Container not found");

  if (!root) root = ReactDOM.createRoot(container);
  root.render(<ReactAxiosFetchView id={id} />);
}
```

* Uses `ReactDOM.createRoot()` → React 18 rendering API.
* `ReactAxiosFetchView` → Main React **container component** for Current Medications.
* `id` parameter is passed from GWT Activity (`"1"` in your example).

✅ **Takeaway:** This is the **starting point of the React module**, rendered inside the GWT container.

---

# **6️⃣ React Routing Inside the Module**

**File:** `routeReactMemoryRouteTabView.jsx`

* Uses **React Router (MemoryRouter)** to manage **tabs inside Current Medications**.
* Keeps **both tabs in DOM** and uses `display: none` to hide inactive tabs.
* Uses **useState** to manage `activeTab` and `needsRefresh`.
* Passes `contextValue` via `Outlet context` → lets child routes share state.

```js
<MemoryRouter initialEntries={['/']}>
  <Routes>
    <Route element={<Outlet context={contextValue} />}>
      <Route path="/*" element={<RouteMemoryRoute12 userId={userId} mode={mode} />} />
    </Route>
  </Routes>
</MemoryRouter>
```

* Each tab has its **own MemoryRouter** to handle routing locally within React.
* Child routes: `RouteMemoryRoute12` and `RouteMemoryRouteXY`.

✅ **Takeaway:** React handles **module-level navigation** (tabs) inside the GWT container, completely independent of GWT routing.

---

# **7️⃣ MainPresenter & URL Parameters**

**File:** `MainPresenter.java`

* Reads URL parameters like `r_currentMedIntegration=true`
* Sets `SlottedController` with `R_CurrentMedRootPlace` as default place if integration is enabled:

```java
slottedController.setDefaultPlace(new R_CurrentMedRootPlace(bean));
slottedController.setDisplay(view.getMainDisplayPanel());
```

* This is how the EMR system **decides to load Current Medications module automatically** based on URL parameters.

✅ **Takeaway:** URL params → MainPresenter → GWT Slot/Place → Activity → React container.

---

# **8️⃣ Full Flow (Step-by-Step)**

1. User opens EMR URL:

```
glaceemr.html?r_currentMedIntegration=true&patientId=376638...
```

2. **MainPresenter** reads URL parameters → decides `r_currentMedIntegration=true`.

3. Sets **SlottedController**:

```java
slottedController.setDefaultPlace(new R_CurrentMedRootPlace(bean));
```

4. **DesktopMainActivityMapper** maps the Place → `R_CurrentMedActivity`.

5. **R_CurrentMedActivity.start()**

   * UiBinder loads `<div id="react-axiosfetch-container">`.
   * Scheduler polls until React JS is ready.
   * Calls `ReactInterop.renderReactAxiosFetch("1")`.

6. **React index.js / renderReactAxiosFetch.js**

   * Mounts `ReactAxiosFetchView` into the container.
   * Component fetches data via Axios from Spring Boot backend.

7. **React Routing**

   * `RouteReactMemoryRouteTabView` manages **tabs inside the module**.
   * Each tab has its own MemoryRouter + child routes.
   * Shared state via `contextValue`.

8. **Unmounting / Cleanup**

   * `R_CurrentMedActivity.onStop()` could call `ReactInterop.unmountReactAxiosFetch()` if needed.

---

# **9️⃣ Data Flow Summary**

```
Spring Boot Backend
        ↓ (Axios)
React Container (ReactAxiosFetchView)
        ↓ (JS Interop)
GWT Activity (R_CurrentMedActivity)
        ↓ (Slot/Place)
Main GWT App (SlottedController, DesktopMainActivityMapper)
        ↓
MainPresenter (URL parameters, integration flags)
```

* **Routing inside React** is local (MemoryRouter).
* **Routing in EMR** (GWT) is global (Places & Slots).
* React gets **context from GWT** via props / JS Interop parameters.

---

# ✅ **Key Patterns in This Module**

1. **Hybrid Architecture**

   * GWT manages global app flow.
   * React manages specific module UI.

2. **Container-Presenter Pattern in React**

   * `ReactAxiosFetchView` is container, handles state and API.
   * Hooks + child routes manage UI and tabs.

3. **Interop**

   * `ReactInterop.java` exposes JS functions.
   * `index.js` attaches functions to `window`.

4. **MemoryRouter for module-local routing**

   * Allows tabs and subroutes inside React independently of GWT.

---
