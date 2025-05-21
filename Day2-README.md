#Day 2 


## 🔹 **Session Tracking**

Session tracking refers to techniques used to **maintain the state of a user (client)** across multiple HTTP requests in a web application.

### 🔸 **Types of Session Tracking**:

1. **Cookies** – Stores session ID in the client browser.
2. **URL Rewriting** – Appends session ID to URL as a query parameter.
3. **Hidden Form Fields** – Stores session data in forms sent to client.
4. **HttpSession** – Server-side session management using `HttpSession` object.
5. **SSL Sessions** – Uses secure socket layer session ID.

---

## 🔹 **RequestDispatcher**

This is part of the **Servlet API** used for **request forwarding and including resources**.

* **Type**: Server-side forwarding
* It is used to:

  * Forward a request from one servlet to another (`forward()`).
  * Include content from another resource (`include()`).

---

## 🔹 **sendRedirect**

This is a method from the `HttpServletResponse` interface.

* **Type**: **Client-side redirect**
* It sends a response back to the browser with a new URL.
* Browser initiates a new request to that URL.

---

## 🔹 **Session Management**

Refers to maintaining **user session** across requests.

### 🔸 **Types of Session Management**:

1. **Client-side**:

   * Cookies
   * URL rewriting
   * Hidden form fields

2. **Server-side**:

   * `HttpSession` object
   * Session tracking with SSL

---

## 🔹 **URL Rewriting**

This is a **session tracking** technique where a session ID is appended to the URL.

* **Example**: `http://example.com/home.jsp;jsessionid=12345`
* **Used when cookies are disabled** on the client side.

---

## 🔹 **HttpSession**

This is a **Java Servlet API** interface that allows storing data on the server per user session.

* Methods: `getSession()`, `setAttribute()`, `getAttribute()`, `invalidate()`, etc.
* Data is stored **on the server**.

---

## 🔹 **Cookies**

Cookies are small pieces of data stored on the **client's browser** and sent with every request to the server.

* Used for:

  * Session tracking
  * Personalization
  * Tracking user behavior

---

## 📌 Summary Table:

| Concept            | Type                         | Category                      |
| ------------------ | ---------------------------- | ----------------------------- |
| Session Tracking   | Client-side / Server-side    | Web Session Management        |
| RequestDispatcher  | Server-side forwarding       | Request Dispatching (Servlet) |
| sendRedirect       | Client-side redirect         | Response Control              |
| Session Management | Client-side & Server-side    | Web Session Handling          |
| URL Rewriting      | Client-side session tracking | Session Tracking Technique    |
| HttpSession        | Server-side session tracking | Java Servlet API              |
| Cookies            | Client-side session tracking | HTTP / Session Tracking       |

---


---

# ✅ **Java Servlet**

---

## 🌐 1. **What is Session Tracking?**

**Session Tracking** is a way to **maintain user data** across multiple requests (HTTP is stateless).
For example: logging in once and staying logged in on all pages.

### 🔹 Types of Session Tracking:

| Method               | Storage            | Code Sample                        |
| -------------------- | ------------------ | ---------------------------------- |
| 1. Cookies           | Client (browser)   | ✅ Below                            |
| 2. URL Rewriting     | Client (URL)       | ✅ Below                            |
| 3. Hidden Form Field | Client (HTML form) | ✅ Below                            |
| 4. HttpSession       | Server             | ✅ Below                            |
| 5. SSL Session       | Server (via HTTPS) | Not commonly used in code manually |

---

### ✅ **1.1 Cookies**

Stores data in the browser, sent with every request.

```java
// In Servlet - Setting Cookie
Cookie c = new Cookie("username", "john123");
c.setMaxAge(3600);  // 1 hour
response.addCookie(c);

// Reading Cookie
Cookie[] cookies = request.getCookies();
for (Cookie cookie : cookies) {
    if (cookie.getName().equals("username")) {
        out.println("Welcome " + cookie.getValue());
    }
}
```

---

### ✅ **1.2 URL Rewriting**

Adds session data to the URL as a parameter.

```java
// Rewriting the URL
String url = response.encodeURL("dashboard.jsp");
out.println("<a href='" + url + "'>Dashboard</a>");
```

---

### ✅ **1.3 Hidden Form Fields**

Stores data in a hidden field in HTML form.

```html
<form action="NextServlet" method="post">
    <input type="hidden" name="username" value="john123">
    <input type="submit" value="Submit">
</form>
```

```java
// NextServlet.java
String user = request.getParameter("username");
out.println("Welcome " + user);
```

---

### ✅ **1.4 HttpSession**

Server-side storage of session information.

```java
// Creating session
HttpSession session = request.getSession();
session.setAttribute("user", "john123");

// Retrieving session data
String user = (String) session.getAttribute("user");
out.println("Hello, " + user);

// Logout
session.invalidate();
```

---

## 🚀 2. **RequestDispatcher (Forward or Include another Servlet)**

Used to forward request/response from one servlet to another **within server**.

### ✅ Forwarding Example:

```java
// Servlet 1
request.setAttribute("user", "john");
RequestDispatcher rd = request.getRequestDispatcher("SecondServlet");
rd.forward(request, response);
```

```java
// SecondServlet.java
String name = (String) request.getAttribute("user");
out.println("Welcome, " + name);
```

---

## 🔁 3. **sendRedirect() (Redirect to another page)**

Sends a **new request** to the browser. URL changes.

```java
response.sendRedirect("WelcomeServlet");
```

⚠️ **New request → request attributes are lost.**

---

## 🧠 4. **Session Management**

Used to manage user data across multiple HTTP requests.

### ✅ Client-side:

* **Cookies**
* **URL Rewriting**
* **Hidden Form Fields**

### ✅ Server-side:

* **HttpSession**
* **SSL Session**

---

## 🔗 5. **URL Rewriting (When cookies are disabled)**

```java
String url = response.encodeRedirectURL("profile.jsp?user=john123");
response.sendRedirect(url);
```

---

## 💼 6. **HttpSession (Best for login/session)**

```java
HttpSession session = request.getSession();
session.setAttribute("username", "john");

String user = (String) session.getAttribute("username");
out.println("Welcome " + user);
```

---

## 🍪 7. **Cookies (Client-side storage)**

```java
// Store
Cookie cookie = new Cookie("user", "john");
response.addCookie(cookie);

// Read
Cookie[] cookies = request.getCookies();
for (Cookie c : cookies) {
    if (c.getName().equals("user")) {
        out.println("Welcome " + c.getValue());
    }
}
```

---

## 🧾 Summary Table (Quick Revision)

| Concept               | Description                               | Where Data is Stored  |
| --------------------- | ----------------------------------------- | --------------------- |
| **Cookies**           | Store small info in client browser        | Client                |
| **URL Rewriting**     | Add info in URL (e.g., `?user=...`)       | Client                |
| **Hidden Fields**     | Pass data in hidden HTML form field       | Client                |
| **HttpSession**       | Stores info in server per user session    | Server                |
| **RequestDispatcher** | Internal forwarding to another resource   | Server                |
| **sendRedirect**      | Browser makes new request to another page | Client-side (new req) |

---

## 📘 Bonus: Servlet Lifecycle

| Method      | Description                             |
| ----------- | --------------------------------------- |
| `init()`    | Called once when servlet is created     |
| `service()` | Called on every request                 |
| `doGet()`   | Handles GET requests                    |
| `doPost()`  | Handles POST requests                   |
| `destroy()` | Called once before servlet is destroyed |

---

## 🧩 Quick Scenario: Login + Session Tracking

### 🔹 LoginServlet.java

```java
String user = request.getParameter("username");
String pass = request.getParameter("password");

if (user.equals("admin") && pass.equals("1234")) {
    HttpSession session = request.getSession();
    session.setAttribute("user", user);
    response.sendRedirect("dashboard.jsp");
} else {
    response.sendRedirect("login.jsp?error=1");
}
```

### 🔹 dashboard.jsp

```jsp
<%
String user = (String) session.getAttribute("user");
if (user == null) {
    response.sendRedirect("login.jsp");
} else {
    out.println("Welcome, " + user);
}
%>
```

---

Certainly! Here's a comprehensive guide covering all the topics you've listed, complete with definitions and sample code snippets. This will serve as a quick reference for your studies in Servlets, JSP, JDBC, Maven, and related concepts.

---

## 📘 Complete Notes: Servlet, JSP, JDBC, Maven (with Examples)

---

### 1. ✅ Creating a Web Project in Eclipse

* **Steps**:

  1. Open Eclipse IDE.
  2. Navigate to **File > New > Dynamic Web Project**.
  3. Enter the **Project Name**.
  4. Select the **Target Runtime** (e.g., Apache Tomcat).
  5. Choose the **Dynamic Web Module version**.
  6. Click **Finish**.

---

### 2. ✅ Creating a Servlet and Configuring `web.xml`

#### ➤ Create Servlet:

```java
@WebServlet("/AddServlet")
public class AddServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        PrintWriter out = res.getWriter();
        out.println("Hello from AddServlet");
    }
}
```

#### ➤ `web.xml` Configuration:

```xml
<servlet>
  <servlet-name>AddServlet</servlet-name>
  <servlet-class>AddServlet</servlet-class>
</servlet>
<servlet-mapping>
  <servlet-name>AddServlet</servlet-name>
  <url-pattern>/add</url-pattern>
</servlet-mapping>
```

---

### 3. ✅ GET and POST Methods

* **Objects**:

  * `HttpServletRequest req` - Used to read data from the client request (form data, headers, attributes).
  * `HttpServletResponse res` - Used to send response back to the client (HTML, text, JSON, etc.).

* **Methods**:

  * `getParameter(String name)` - Fetches the value of a request parameter (e.g., from a form input).
  * `getWriter()`               - Returns a `PrintWriter` object.
  * `service()`                 - Master method invoked on every request (overrides automatically call `doGet()` or `doPost()`).
  * `doGet()`                   - Handles HTTP GET requests.
  * `doPost()`                  - Handles HTTP POST requests.

#### ➤ Example:

```java
protected void doPost(HttpServletRequest req, HttpServletResponse res) throws IOException {
    String name = req.getParameter("name");
    PrintWriter out = res.getWriter();
    out.println("Hello, " + name);
}
```

---

### 4. ✅ Method Dispatcher (Calling a Servlet from Another Servlet)

#### ➤ RequestDispatcher:

```java
RequestDispatcher rd = req.getRequestDispatcher("/SecondServlet");
rd.forward(req, res);
```

* **Passing Values**:

  * `req.setAttribute("key", value);`
  * `req.getAttribute("key");`

---

### 5. ✅ `HttpServletRequest` & `HttpServletResponse`

* **Methods**:

  * `service()`: Handles all types of requests.
  * `destroy()`: Called before the servlet is removed from service.

---

### 6. ✅ RequestDispatcher vs. SendRedirect

* **RequestDispatcher**:

  * Server-side forward.
  * Same request object.

* **SendRedirect**:

  * Client-side redirect.
  * New request and response objects.

```java
res.sendRedirect("page.jsp");
```

---

### 7. ✅ HttpSession vs. Cookie

* **HttpSession**:

  * Server-side storage.
  * Stores user data across multiple requests.

* **Cookie**:

  * Client-side storage.
  * Stores small pieces of data on the user's browser.

---

### 8. ✅ `ServletConfig` & `ServletContext`

#### ➤ `web.xml` Configuration:

```xml
<context-param>
  <param-name>name</param-name>
  <param-value>Gobal</param-value>
</context-param>

<servlet>
  <servlet-name>MyServlet</servlet-name>
  <servlet-class>com.krish.MyServlet</servlet-class>
  <init-param>
    <param-name>name</param-name>
    <param-value>Krishnan</param-value>
  </init-param>
</servlet>
```

---

### 9. ✅ Servlet Annotation Configuration

* Use `@WebServlet("/path")` to define servlets.
* Two configuration methods:

  * XML (`web.xml`)
  * Annotations (`@WebServlet`)

---

### 10. ✅ Why JSP?

* Simplifies the creation of dynamic web content.
* Allows embedding Java code in HTML.

#### ➤ Example:

```jsp
<% out.print("Welcome to JSP"); %>
```

---

### 11. ✅ How JSP is Translated into Servlets

* JSP files are compiled into servlet classes by the server (e.g., Tomcat).
* The JSP engine converts JSP into a servlet, which is then compiled and executed.

---

### 12. ✅ JSP to Servlet Conversion in NetBeans

* Right-click on the JSP file.
* Select **Compile JSP** to view the generated servlet code.

---

### 13. ✅ JSP Tags

* **Scriptlet**: `<% code %>`
* **Expression**: `<%= value %>`
* **Declaration**: `<%! int x = 5; %>`
* **Directive**: `<%@ page language="java" %>`

---

Great! Let's properly organize and explain **JSP Directive Tags** with **syntax, purpose, and real examples** for each of the three types:

---

## ✅ **14. JSP Directives**

JSP **Directives** provide **global information** about an entire JSP page. They affect how the JSP page is translated into a servlet.

There are **three types** of directives:

---

### 🔹 1. **Page Directive**

Defines attributes that apply to the entire JSP page, such as language, content type, import statements, error page, etc.

#### ✅ Syntax:

```jsp
<%@ page attribute="value" %>
```

#### ✅ Common Attributes:

| Attribute     | Description                                  |
| ------------- | -------------------------------------------- |
| `language`    | Language used (usually `java`)               |
| `contentType` | MIME type (e.g., `text/html`, charset)       |
| `import`      | Java packages to import                      |
| `errorPage`   | Page to redirect if an exception occurs      |
| `isErrorPage` | Set to `true` if the page handles exceptions |

#### ✅ Example:

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" %>
<%@ page import="java.sql.*, java.util.*" %>
<%@ page errorPage="error.jsp" %>
```

---

### 🔹 2. **Include Directive**

Includes a static resource **at translation time** (i.e., when the JSP is converted to a servlet). The included file's code becomes part of the main JSP page.

#### ✅ Syntax:

```jsp
<%@ include file="filename.jsp" %>
```

#### ✅ Example:

```jsp
<%@ include file="header.jsp" %>
<%@ include file="footer.jsp" %>
```

> 🔸 Use this when you want to reuse code like headers, footers, navbars, etc.

---

### 🔹 3. **Taglib Directive**

Declares a custom tag library (e.g., JSTL). This enables the use of special tags in the JSP page.

#### ✅ Syntax:

```jsp
<%@ taglib uri="tagURI" prefix="prefixName" %>
```

#### ✅ Example (JSTL Core):

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```

This enables JSTL `<c:forEach>`, `<c:if>`, and other tags.

---

### ✅ Summary Table

| Directive   | Purpose                                  | Example Syntax                        |
| ----------- | ---------------------------------------- | ------------------------------------- |
| **page**    | Set page-level settings                  | `<%@ page contentType="text/html" %>` |
| **include** | Include static files at translation time | `<%@ include file="header.jsp" %>`    |
| **taglib**  | Import tag libraries like JSTL           | `<%@ taglib uri="..." prefix="c" %>`  |



---

### 15. ✅ Implicit Objects in JSP

| Object        | Description           |
| ------------- | --------------------- |
| `request`     | `HttpServletRequest`  |
| `response`    | `HttpServletResponse` |
| `session`     | `HttpSession`         |
| `application` | `ServletContext`      |
| `out`         | `JspWriter`           |
| `config`      | `ServletConfig`       |
| `pageContext` | `PageContext`         |

---

### 16. ✅ Exception Handling in JSP

* **In main JSP**:

```jsp
<%@ page errorPage="error.jsp" %>
```

* **In `error.jsp`**:

```jsp
<%@ page isErrorPage="true" %>
```

---

### 17. ✅ JDBC in JSP

#### ➤ Example:

```jsp
<%@ page import="java.sql.*" %>
<%
Class.forName("com.mysql.cj.jdbc.Driver");
Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/student", "root", "pass");
Statement stmt = con.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM students");
while(rs.next()) {
    out.println(rs.getString(1));
}
rs.close(); stmt.close(); con.close();
%>
```

---

### 18. ✅ MVC Using Servlet & JSP

* **Model**: Java classes and JDBC for data handling.
* **View**: JSP files for presentation.
* **Controller**: Servlets to handle requests and responses.

---

### 19. ✅ JSTL Tutorial and EL (Expression Language)

* **JSTL SQL Tags**:

```jsp
<sql:setDataSource ... />
<sql:query ... />
```

* **JSTL Functions**:

```jsp
<c:out value="${expression}" />
<c:forEach var="item" items="${list}">
    ${item}
</c:forEach>
```

---

### 20. ✅ Filters in Servlet

* Used for preprocessing and postprocessing requests.

#### ➤ Example:

```java
@WebFilter("/secure/*")
public class AuthFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        // Authentication logic
        chain.doFilter(request, response);
    }
}
```

---

### 21. ✅ Login Using Servlet & JSP

* **Session Management**:

```java
HttpSession session = request.getSession();
session.setAttribute("user", username);
```

* **Preventing Back Button After Logout**:

```jsp
<%
response.setHeader("Cache-Control","no-cache,no-store,must-revalidate");
response.setHeader("Pragma","no-cache");
response.setDateHeader ("Expires", 0);
%>
```

---

### 22. ✅ Servlet | JSP | JDBC | Maven Example

#### ➤ `pom.xml` Dependency:

```xml
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.33</version>
</dependency>
```

#### ➤ Servlet:

```java
@WebServlet("/Login")
public class LoginServlet extends HttpServlet {
  protected void doPost(HttpServletRequest req, HttpServletResponse res) throws IOException {
    String user = req.getParameter("username");
    String pass = req.getParameter("password");
    // JDBC logic to validate user
  }
}
```

#### ➤ JSP:

```jsp
<form action="Login" method="post">
  Username: <input name="username">
  Password: <input name="password" type="password">
  <input type="submit">
</form>
```

---


