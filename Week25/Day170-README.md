
## **1️⃣ GWT Side: Activity & Place Architecture with MVP**

GWT often uses the **Activity and Place** pattern for navigation and MVP for organizing UI logic.

Here’s what your notes describe:

### **Files & Roles**

| File                         | Role                       | Explanation                                                                                                                                                         |
| ---------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PrescriptionPlaceR.java`    | Place                      | Represents a “location” in the app, similar to a URL route. It tells the app *where* the user is.                                                                   |
| `PrescriptionActivityR.java` | Activity                   | Controller in MVP. Handles logic for this place. On `start()`, it will call the React render function. Think of it as a bridge between GWT and React for this view. |
| `PrescriptionViewR.ui.xml`   | View (UI Binder)           | Defines the HTML structure of the view. It has an empty `<div>` with `id="prescripio"` where React will render the component.                                       |
| `PrescriptionViewR.java`     | View (Presenter interface) | Binds the UI (from `.ui.xml`) and defines methods that the Activity can call.                                                                                       |

### **How Communication Happens**

* **GWT → React**
  A Java class `ReactInterlop.java` handles calling React functions from GWT. For example:

  ```java
  ReactInterlop.renderPrescription();
  ```

* **React → GWT**
  React functions are exposed globally (on `window`) so GWT can call them. Example:

  ```javascript
  window.renderPrescription = renderPrescription;
  ```

This is classic **interop** between GWT (Java) and React (JS).

---

## **2️⃣ React Side: Container-Presenter + Custom Hooks**

React code is structured using modern best practices:

### **Files & Roles**

| File                                               | Role                | Explanation                                                                 |
| -------------------------------------------------- | ------------------- | --------------------------------------------------------------------------- |
| `renderPrescription.jsx`                           | Entry point for GWT | Finds the GWT `<div id="prescripio">` and mounts the React component there. |
| `routePrescription.jsx`                            | Router              | Handles routes for React components.                                        |
| `PrescriptionView.jsx`                             | Container           | Manages state, API calls, and passes data to Presentational components.     |
| `usePrescription.js`                               | Custom Hook         | Encapsulates state logic (fetching, updating prescriptions).                |
| `editPrescription.jsx` & `useEditPrescription.jsx` | Editing logic       | Component + hook for editing prescription data.                             |
| `drugSearchView.jsx` & `useDrugSearch.jsx`         | Drug search logic   | Component + hook for drug search functionality.                             |

### **Key React Concepts Used**

* **Container-Presenter Pattern**: Containers handle logic and state; Presenters handle rendering UI.
* **Custom Hooks**: Encapsulate reusable logic (`useState`, `useEffect`, API calls with `axios`).
* **React Router**: Uses `useNavigate`, `useLocation`, `useOutletContext` for routing and passing context.

---

## **3️⃣ How GWT and React are Integrated**

1. User navigates in GWT (via `Place` → `Activity`).
2. `PrescriptionActivityR.start()` calls `ReactInterlop.renderPrescription()`.
3. `renderPrescription.jsx` mounts the React component into the `<div id="prescripio">`.
4. React handles all UI, state, API calls, and routing within that div.

So essentially, **GWT acts as a shell and routing layer**, and **React handles dynamic UI inside a container div**.

---

## **4️⃣ Build & Run Instructions**

1. Switch to **Java 17**.
2. Go to React directory and install dependencies:

```bash
cd /home/software/git/gwt-react_Master/src/main/react
npm install
npm start  # Runs React dev server
```

3. Go to GWT project root:

```bash
cd /home/software/git/gwt-react_Master
mvn clean
mvn install
mvn gwt:devmode  # Runs GWT in development mode
```

This sets up both GWT and React, with React embedded inside GWT views.

---

## **✅ Summary**

* **GWT (MVP + Activity/Place)**: Handles routing and overall app structure. Minimal UI logic.
* **React (Container-Presenter + Hooks)**: Handles dynamic UI inside specific GWT `<div>` containers.
* **Interop**: `ReactInterlop` allows GWT to trigger React rendering.
* **Architecture Goals**:

  * Separate concerns cleanly (routing vs UI logic)
  * Reuse React components in a GWT environment
  * Modern React patterns (hooks, container/presenter, axios, router)

---


## 🧩 Overall Concept

They are building a **GWT (Google Web Toolkit)** application that integrates **React** for rendering modern UI parts.

* **GWT** side: Written in Java, compiled into JavaScript.
* **React** side: Written in JavaScript/TypeScript, runs as part of the same web app.
* **Communication:** Done via **JSInterop** (Java ↔ JavaScript bridge).

Essentially, the GWT side loads a page and includes an empty `<div>` container.
React is then mounted into that container dynamically at runtime.

---

## 🏗️ GWT Architecture (MVP + Activities & Places)

### 1. **PrescriptionPlaceR.java (1)**

* Represents a “place” (i.e., a logical screen or route) in GWT.
* Tied to a specific URL or token.
* When navigated to, GWT uses an **ActivityMapper** to launch the corresponding `PrescriptionActivityR`.

### 2. **PrescriptionActivityR.java (2)**

* The *Activity* associated with the *Place*.
* This is where the logic starts.
* In `start()`, it initializes the `PrescriptionViewR`.
* It also calls the JSInterop function (to render the React component).

Example idea:

```java
public class PrescriptionActivityR extends AbstractActivity {
    @Override
    public void start(AcceptsOneWidget panel, EventBus eventBus) {
        panel.setWidget(prescriptionView.asWidget());
        ReactInterop.renderPrescription(); // Calls React render function
    }
}
```

### 3. **PrescriptionViewR.ui.xml (3)**

* Defines the UI layout for the view in XML.
* It contains a **placeholder div** for React:

```xml
<g:HTMLPanel>
  <div id="prescription"></div>
</g:HTMLPanel>
```

### 4. **PrescriptionViewR.java (4)**

* The Java class backing the XML UI.
* It might provide some helper methods or logic.
* But most importantly, it exposes the div container (`id="prescription"`) where React will mount.

---

## 🔁 GWT ↔ React Communication (JSInterop)

### GWT side:

**ReactInterop.java**

* A GWT Java class annotated with `@JsType` or `@JsMethod` to interact with JS.
* Calls React render function defined on the JS side.

Example:

```java
@JsType(isNative = true, namespace = JsPackage.GLOBAL)
public class ReactInterop {
    public static native void renderPrescription();
}
```

### React side:

**index.js**

```js
import { renderPrescription } from './renderPrescription';
window.renderPrescription = renderPrescription;
```

This makes the function available globally so GWT can call it.

---

## ⚛️ React Architecture (Container–Presenter + Hooks)

On the React side, they’re following a **Container–Presenter pattern**, using **custom hooks** for logic and **functional components** for presentation.

### React File Overview

| File                           | Purpose                                                                           |
| ------------------------------ | --------------------------------------------------------------------------------- |
| **renderPrescription.jsx (1)** | Entry point for rendering. Mounts React app into GWT’s `<div id="prescription">`. |
| **routePrescription.jsx (2)**  | Handles React Router setup for routes inside prescription feature.                |
| **PrescriptionView.jsx (3)**   | Main container component for the prescription screen.                             |
| **usePrescription.js (4)**     | Custom hook for managing state and API calls for prescription.                    |
| **editPrescription.jsx**       | Component for editing prescriptions.                                              |
| **useEditPrescription.jsx**    | Hook with logic for editing functionality.                                        |
| **drugSearchView.jsx**         | UI for drug searching.                                                            |
| **useDrugSearch.jsx**          | Logic hook for drug search operations.                                            |

### React Stack Used

* **Container–Presenter pattern:**

  * Container = handles data fetching and state logic (via hooks)
  * Presenter = purely visual / UI part
* **Custom Hooks:**
  For encapsulating business logic.
* **React Router:**
  Uses `useNavigate`, `useLocation`, `useOutletContext` for routing.
* **Axios:**
  For API calls.
* **React 18+ hooks-based architecture.**

---

## 🔧 Build and Run Instructions

This project uses both **Maven (for GWT/Java)** and **npm (for React/JS)**.

### Steps:

1. **Set Java to 17**
   GWT supports up to Java 17 (needed for compatibility).

2. **React setup:**

   ```bash
   cd /home/software/git/gwt-react_Master/src/main/react
   npm install
   npm start
   ```

   * Installs all React dependencies.
   * Starts the React dev server (for standalone dev).

3. **GWT setup:**

   ```bash
   cd /home/software/git/gwt-react_Master
   mvn clean
   mvn install
   mvn gwt:devmode
   ```

   * Compiles the Java side.
   * Runs GWT in development mode.
   * GWT will load the HTML that contains the React mount point.

---

## 🧠 In Summary

They are describing a **hybrid application** that integrates:

| Layer         | Framework | Pattern                            | Purpose                             |
| ------------- | --------- | ---------------------------------- | ----------------------------------- |
| Frontend Java | GWT       | MVP (Activity + Place)             | Routing, lifecycle, interop trigger |
| Frontend JS   | React     | Container–Presenter + Custom Hooks | Actual UI rendering                 |
| Bridge        | JSInterop | Java ↔ JS communication            | Connects GWT and React              |

**Flow:**

1. User navigates to “Prescription” place → triggers `PrescriptionActivityR`.
2. Activity calls `ReactInterop.renderPrescription()`.
3. React code mounts inside `<div id="prescription">`.
4. React app handles its own routing, state, and API calls.

---


### 🧭 **GWT–React Architecture & Control Flow**

```
┌────────────────────────────────────────────┐
│                GWT SIDE (Java)              │
│--------------------------------------------│
│                                            │
│ 1️⃣ PrescriptionPlaceR.java                 │
│     • Defines the route (URL token).        │
│     • When navigated, launches Activity.    │
│                                            │
│              │                              │
│              ▼                              │
│ 2️⃣ PrescriptionActivityR.java              │
│     • Called by GWT PlaceController.        │
│     • start() method sets up view.          │
│     • Calls JSInterop → renderPrescription()│
│                                            │
│              │                              │
│              ▼                              │
│ 3️⃣ PrescriptionViewR.ui.xml                │
│     • Defines layout in XML.                │
│     • Contains empty div:                   │
│          <div id="prescription"></div>      │
│                                            │
│ 4️⃣ PrescriptionViewR.java                  │
│     • Binds the XML UI.                     │
│     • Provides access to that div.          │
│                                            │
│              │                              │
│              ▼                              │
│ 5️⃣ ReactInterop.java (JSInterop bridge)    │
│     • @JsMethod calls JS function:          │
│          window.renderPrescription()        │
└──────────────┬──────────────────────────────┘
               │
               ▼
┌────────────────────────────────────────────┐
│                REACT SIDE (JSX)             │
│--------------------------------------------│
│                                            │
│ 6️⃣ index.js                                │
│     • Exposes global function:              │
│         window.renderPrescription = ...     │
│     • Called by GWT JSInterop.              │
│                                            │
│              │                              │
│              ▼                              │
│ 7️⃣ renderPrescription.jsx                  │
│     • Mounts React app into:                │
│          document.getElementById('prescription') │
│     • Loads <RoutePrescription />           │
│                                            │
│              │                              │
│              ▼                              │
│ 8️⃣ RoutePrescription.jsx                   │
│     • Uses React Router for nested routes.  │
│     • Displays <PrescriptionView />         │
│                                            │
│              │                              │
│              ▼                              │
│ 9️⃣ PrescriptionView.jsx                    │
│     • Container component.                  │
│     • Uses hooks: usePrescription(), etc.   │
│     • Renders subcomponents like:           │
│         <EditPrescription />                │
│         <DrugSearchView />                  │
│                                            │
│ 🔟 Custom Hooks (Logic layer)               │
│     • usePrescription.js                    │
│     • useEditPrescription.js                │
│     • useDrugSearch.js                      │
│     (Manage API, state, business logic)     │
│                                            │
└────────────────────────────────────────────┘
```

---

### ⚙️ **Flow Summary**

1. **User navigates** to the Prescription page in GWT.
2. `PrescriptionPlaceR` triggers → `PrescriptionActivityR`.
3. `PrescriptionActivityR.start()` calls `ReactInterop.renderPrescription()`.
4. GWT page already has `<div id="prescription"></div>`.
5. JSInterop calls global JS function `window.renderPrescription()`.
6. React mounts the React UI into that div.
7. React takes over — routing, hooks, and rendering handled entirely on the React side.

---

### 💡 **Why This Design?**

✅ Reuses existing GWT app structure (Activities & Places).
✅ Allows modern UI (React) integration incrementally.
✅ Maintains clear separation:

* **GWT = navigation, shell, legacy logic.**
* **React = UI, dynamic interaction, modern frontend stack.**

---

## 🧠 WHAT THIS IS ABOUT

This project is a **hybrid web application** where:

* **GWT (Google Web Toolkit)** handles **routing and screen management** using its **MVP (Model–View–Presenter)** architecture.
* **React** handles **modern, dynamic UI rendering** using **Container–Presenter pattern** and **custom hooks**.

Basically:

> GWT is used for navigation + lifecycle,
> React is used for rendering the actual user interface.

They communicate via **JSInterop** (Java calling JavaScript).

---

## ⚙️ HIGH-LEVEL FLOW

Let’s go through the **runtime flow** — what happens when the user opens the “Prescription” screen:

### 1️⃣ User navigates to Prescription page

* GWT navigation triggers a **Place** → `PrescriptionPlaceR.java`.
* The “Place” is like a **URL token or route handler** in GWT.

### 2️⃣ Place → Activity

* The **ActivityMapper** finds which **Activity** corresponds to that Place.
* It launches `PrescriptionActivityR.java`.

### 3️⃣ Activity starts

* The Activity’s `start()` method is called.
* It attaches the **View** (`PrescriptionViewR`) to the screen.
* It then calls a JSInterop method:

  ```java
  ReactInterop.renderPrescription();
  ```

  This triggers React to start rendering.

### 4️⃣ View loads

* The view (`PrescriptionViewR.ui.xml`) is a GWT XML layout file.
* It contains a placeholder:

  ```xml
  <div id="prescription"></div>
  ```

  → This is the “container” where React will render its components.

### 5️⃣ JSInterop bridge

* The call from GWT to JS (via `ReactInterop.java`) runs:

  ```js
  window.renderPrescription();
  ```
* This global JS function (defined in React’s `index.js`) mounts the React app.

### 6️⃣ React mounts into GWT container

* `renderPrescription.jsx` finds the `<div id="prescription">` and renders the React UI there.

### 7️⃣ React handles everything else

* Once mounted, React runs independently:

  * `RoutePrescription.jsx` sets up internal React routes.
  * `PrescriptionView.jsx` is the main container.
  * Hooks like `usePrescription.js` handle data fetching & state.
  * Subcomponents like `EditPrescription.jsx`, `DrugSearchView.jsx` render different screens.

So the control flow is:

```
GWT Place  →  GWT Activity  →  GWT View (div placeholder)
       ↓
   JSInterop bridge
       ↓
 React render → React Router → React components & hooks
```

---

## 🔄 GWT vs React — File-by-File Comparison

Here’s a **mapping** so you can understand which file in React serves a similar role to which file in GWT:

| GWT Component                  | React Equivalent                         | Description / Purpose                                                                                            |
| ------------------------------ | ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **PrescriptionPlaceR.java**    | **routePrescription.jsx**                | Defines routing or navigation entry point. GWT’s *Place* ≈ React’s *Route*.                                      |
| **PrescriptionActivityR.java** | **renderPrescription.jsx**               | GWT Activity controls what to start and what to display. It’s like the entry that calls React’s render function. |
| **PrescriptionViewR.ui.xml**   | **PrescriptionView.jsx**                 | The actual screen layout (UI). In GWT it’s XML-based; in React it’s JSX-based.                                   |
| **PrescriptionViewR.java**     | **usePrescription.js** + UI state logic  | Connects the UI with business logic — in React this role is done by the Container + custom hook.                 |
| **ReactInterop.java**          | **index.js** (window.renderPrescription) | The communication bridge. GWT calls JS function exposed globally in React.                                       |

So conceptually:

```
GWT "Place"         = React "Route"
GWT "Activity"      = React "Entry Render function"
GWT "View" (XML)    = React "Component JSX"
GWT "Presenter"     = React "Hook + Container logic"
GWT "JSInterop"     = React "index.js global functions"
```

---

## 🔁 Putting It All Together

Let’s visualize the **flow end-to-end** in plain steps:

```
[User clicks Prescription link]
        ↓
GWT Place: PrescriptionPlaceR
        ↓
GWT Activity: PrescriptionActivityR
        ↓
GWT View: PrescriptionViewR.ui.xml
  (contains <div id="prescription">)
        ↓
JSInterop: ReactInterop.renderPrescription()
        ↓
React side:
   → window.renderPrescription()
   → renderPrescription.jsx
   → routePrescription.jsx
   → PrescriptionView.jsx (Container)
       ↳ usePrescription.js (logic)
       ↳ EditPrescription.jsx
       ↳ DrugSearchView.jsx
```

---

## 🎯 Summary

| Layer                           | Technology             | Role                                |
| ------------------------------- | ---------------------- | ----------------------------------- |
| **Navigation / Page lifecycle** | GWT (Place + Activity) | Controls which screen is active     |
| **Placeholder View**            | GWT (ui.xml)           | Div container where React mounts    |
| **Interop Bridge**              | JSInterop              | Allows GWT Java to call React JS    |
| **UI Rendering + Logic**        | React (JSX + Hooks)    | Handles dynamic rendering and state |
| **Routing inside React**        | React Router           | Manages subpages like Edit / Search |

---




