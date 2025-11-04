
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

