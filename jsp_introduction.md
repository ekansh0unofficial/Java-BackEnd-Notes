# JSP Notes

## 1. Setup in IntelliJ Community Edition

* **Problem**: Community Edition does not provide direct JSP/Servlet support.
* **Workaround**:

  * Go to `Settings → Editor → File Types`.
  * Map `.jsp` files to `HTML`.
  * This gives **syntax highlighting**, but **JSP-specific features (tags, EL, debugging, deployment)** won’t work.

## 🔹 2. Use File Templates (For new JSP files)

You can make IntelliJ **insert boilerplate automatically when creating a new JSP file**.

**Steps:**

1. Go to `Settings → Editor → File and Code Templates`.
2. Under the **Files tab**, click ➕ → Name it `JSP File`.
3. Paste boilerplate code:

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>${NAME}</title>
   </head>
   <body>
       <h2>Welcome to JSP!</h2>
   </body>
   </html>
   ```

   * `${NAME}` will be replaced with the filename.
4. Next time you **Right-click → New → JSP File**, IntelliJ will use this template.

---

### 🔹 Introduction to JSP (JavaServer Pages)

#### 📌 What is JSP?

* JSP is a technology that allows you to **mix HTML and Java code** in the same file.
* Think of it as a **shortcut to servlets** — under the hood, a JSP file is compiled into a servlet by the server (e.g., Tomcat).
* Great for building **beautiful, practical webpages** with embedded Java logic.


#### 📌 Why JSP over Servlets?

* In a pure servlet (`AddServlet.java`):

  * You must use `PrintWriter` to write every line of HTML.
  * Code quickly becomes messy for bigger webpages.
* In JSP:

  * You write **HTML directly** and sprinkle Java wherever needed.
  * JSP engine automatically provides `request`, `response`, and `out` objects — no need to declare them.



#### 📌 Example: `add.jsp`

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<html>
<head>
    <title>Add Page JSP</title>
</head>
<body bgcolor="cyan">
    <h2>Add Two Numbers</h2>
    <form action="add.jsp" method="get">
        Number 1: <input type="text" name="num1"><br>
        Number 2: <input type="text" name="num2"><br>
        <input type="submit" value="Add">
    </form>

    <% 
        // JSP scriptlet: execute Java code here
        String n1 = request.getParameter("num1");
        String n2 = request.getParameter("num2");

        if (n1 != null && n2 != null) {
            int i = Integer.parseInt(n1);
            int j = Integer.parseInt(n2);
            int k = i + j;
            out.println("<h3>Result: " + k + "</h3>");
        }
    %>
</body>
</html>
```

---

#### 📌 Key Observations

* **No PrintWriter needed** → use `out.println()` directly in scriptlets.
* **`request` and `response`** objects are **already available** — no need to declare them.
* JSP combines **form + logic + display** in one page.

---

#### ✅ Summary

* **Servlets** → best for heavy backend logic (processing, DB, APIs).
* **JSP** → best for presenting data in a webpage (with Java mixed in).
* Together they follow **MVC pattern**:

  * Servlet → Controller (logic)
  * JSP → View (presentation)

## 📘 JSP Components

JSP (JavaServer Pages) extends Servlets by allowing **Java code + HTML** in the same file.
Every JSP is compiled into a **Servlet** by the server (e.g., Tomcat).

JSP provides the following main components:

---

### 🔹 1. Scriptlets

* Syntax:

  ```jsp
  <% Java code %>
  ```
* Java code inside a JSP scriptlet is placed in the servlet’s **service method**.
* Example:

  ```jsp
  <% 
     int a = 5, b = 10; 
     int sum = a + b; 
     out.println("Sum: " + sum); 
  %>
  ```

---

### 🔹 2. Declarations

* Syntax:

  ```jsp
  <%! Java code %>
  ```
* Declares **methods or variables** at the class level (outside service method).
* Example:

  ```jsp
  <%! 
     int cube(int n) { 
         return n * n * n; 
     } 
  %>
  Result: <%= cube(3) %>
  ```

---

### 🔹 3. Expressions

* Syntax:

  ```jsp
  <%= expression %>
  ```
* Shortcut for `out.println(expression);`
* Example:

  ```jsp
  <%= "Hello, JSP!" %>
  <%= 5 + 10 %>
  ```

---

### 🔹 4. Directives

Instructions to the JSP engine, processed **at translation time**.
Syntax:

```jsp
<%@ directive attribute="value" %>
```

**Types of directives:**

1. **Page Directive** – page-level settings

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
   <%@ page import="java.util.Date" %>
   ```

   Attributes include: `language`, `contentType`, `pageEncoding`, `import`, `errorPage`, `isErrorPage`.

2. **Include Directive** – inserts another file at translation time (static include).

   ```jsp
   <%@ include file="header.jsp" %>
   ```

3. **Taglib Directive** – imports custom tag libraries (e.g., JSTL).

   ```jsp
   <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
   ```

---

## 🔹 JSP Implicit Objects in Detail

JSP gives **9 pre-defined objects** you can directly use inside a page without declaring them.

| Object        | Type                  | Scope       | Description                                                          |
| ------------- | --------------------- | ----------- | -------------------------------------------------------------------- |
| `request`     | `HttpServletRequest`  | Request     | Represents the client request (form data, params, headers, cookies). |
| `response`    | `HttpServletResponse` | Request     | Used to control response (headers, cookies, redirection).            |
| `out`         | `JspWriter`           | Page        | Used to send output to the client (like `PrintWriter`).              |
| `session`     | `HttpSession`         | Session     | Stores user data across multiple requests.                           |
| `application` | `ServletContext`      | Application | Shared memory across the entire web app (all users, all sessions).   |
| `config`      | `ServletConfig`       | Page        | Stores servlet-specific initialization parameters.                   |
| `pageContext` | `PageContext`         | Page        | Provides access to all other implicit objects + extra utilities.     |
| `page`        | `Object` (this)       | Page        | Reference to the current JSP page as a servlet object.               |
| `exception`   | `Throwable`           | Page        | Used in error handling JSPs to display caught exceptions.            |

---

### 1. `request` (You already know)

* Represents **client request** to the server.
* Scope: only valid for that single HTTP request.
* Example:

  ```jsp
  Name: <%= request.getParameter("username") %>
  ```

---

### 2. `response` (You already know)

* Represents the **server’s response** to client.
* Used for redirection, setting headers, sending cookies.
* Example:

  ```jsp
  <% response.sendRedirect("login.jsp"); %>
  ```

---

### 3. `out` (You already know, but JSP-specific)

* Object of `JspWriter` (not `PrintWriter`).
* Handles **buffered output**.
* Example:

  ```jsp
  <% out.println("Hello from JSP!"); %>
  ```

---

### 4. `session` (You already know)

* Represents `HttpSession`.
* Stores data across requests from the **same user**.
* Example:

  ```jsp
  <% session.setAttribute("user", "Ekansh"); %>
  Welcome <%= session.getAttribute("user") %>
  ```

---

### 5. `application` (ServletContext)

* Represents **ServletContext** (shared memory for entire application).
* Unlike `session` (per user), `application` is **shared across all users**.
* Use it for app-wide data like configuration, counters.
* Example:

  ```jsp
  <% 
     int count = (application.getAttribute("visits") == null) ? 1 :
                 (Integer)application.getAttribute("visits") + 1;
     application.setAttribute("visits", count);
  %>
  Total Visits: <%= application.getAttribute("visits") %>
  ```

---

### 6. `config` (ServletConfig)

* Contains **initialization parameters** for a servlet (from `web.xml`).
* Scope: only for that servlet/JSP.
* Example (`web.xml`):

  ```xml
  <servlet>
      <servlet-name>MyServlet</servlet-name>
      <servlet-class>com.test.MyServlet</servlet-class>
      <init-param>
          <param-name>dbName</param-name>
          <param-value>myDB</param-value>
      </init-param>
  </servlet>
  ```

  In JSP:

  ```jsp
  Database Name: <%= config.getInitParameter("dbName") %>
  ```

---

### 7. `pageContext`

* A **wrapper/helper object** for all implicit objects.
* Provides convenience methods to access attributes in **different scopes** (`page`, `request`, `session`, `application`).
* Example:

  ```jsp
  <% pageContext.setAttribute("city", "Delhi", PageContext.SESSION_SCOPE); %>
  City: <%= pageContext.getAttribute("city", PageContext.SESSION_SCOPE) %>
  ```

---

### 8. `page`

* A reference to the **current JSP’s servlet object** (like `this` in Java).
* Rarely used directly in JSP.
* Example:

  ```jsp
  This page object: <%= page.getClass().getName() %>
  ```

---

### 9. `exception`

* Represents the `Throwable` object if the JSP is an **error page**.
* Only available when you declare:

  ```jsp
  <%@ page isErrorPage="true" %>
  ```
* Example:

  ```jsp
  <h3>Error: <%= exception.getMessage() %></h3>
  ```

---

## 🔹 How They Differ from Session/Cookies

* **Cookies** → stored on client browser; sent with each request.
* **Session (`HttpSession`)** → stored on server, per user, can internally use cookies (JSESSIONID) to track users.
* **Application (`ServletContext`)** → stored on server, shared among all users.
* **Config (`ServletConfig`)** → fixed servlet-specific parameters (from deployment descriptor).
* **PageContext** → a manager object that lets you navigate across **all scopes** (page → request → session → application).

---

### ✅ Summary

* **Scriptlet**: Embed raw Java inside JSP (`<% %>`).
* **Declaration**: Define methods/variables at class level (`<%! %>`).
* **Expression**: Print values quickly (`<%= %>`).
* **Directive**: Control JSP behavior (`<%@ %>`).
* **Implicit Objects**: Predefined objects like `request`, `response`, `session`.

JSP makes building **dynamic webpages** easier compared to Servlets alone, by separating **logic (Servlets)** and **presentation (JSP)**.

## 🔹 JSP Lifecycle (JSP → Servlet → Response)

When you access a `.jsp` file, the server (e.g., Tomcat) does the following:

1. **Translation**

   * The JSP file is translated into a **Java Servlet source file** (`.java`).
   * Example: `index.jsp` → `index_jsp.java`.

2. **Compilation**

   * The generated servlet `.java` file is compiled into a `.class` file.
   * Example: `index_jsp.java` → `index_jsp.class`.

3. **Loading & Initialization**

   * The servlet class is loaded into memory.
   * `jspInit()` method is called once (like `init()` in servlets).

4. **Execution**

   * For every request, `_jspService()` method is called.
   * Your HTML + Java code runs and produces dynamic output.

5. **Destroy**

   * When the server shuts down or the JSP is unloaded, `jspDestroy()` is called (like `destroy()` in servlets).

---

### 🔹 Example: Simple JSP

**hello.jsp**

```jsp
<html>
<body>
    <h2>Hello JSP!</h2>
    <%
        out.println("Today is: " + new java.util.Date());
    %>
</body>
</html>
```

---

### 🔹 How it Looks After Translation

The server converts it to a servlet (simplified view):

```java
public final class hello_jsp extends HttpJspBase {
    public void _jspService(HttpServletRequest request, HttpServletResponse response)
            throws IOException, ServletException {
        JspWriter out = response.getWriter();
        out.write("<html><body>");
        out.write("<h2>Hello JSP!</h2>");
        out.write("Today is: " + new java.util.Date());
        out.write("</body></html>");
    }
}
```

---

### ✅ Key Observations

* JSP is just a **shortcut for writing servlets**.
* **Your HTML** becomes `out.write("...")` statements.
* **Your scriptlets** (`<% ... %>`) become Java code inside `_jspService()`.
* This explains why JSP supports **implicit objects** like `request`, `response`, and `out`.

## 🔹 JSP Directives

Directives in JSP provide **instructions to the JSP container** at **translation time** (when JSP is converted to a servlet).
Syntax:

```jsp
<%@ directive attribute="value" %>
```

There are **three types of directives**:

---

### 1. **Page Directive**

Controls settings for the entire JSP page.

**Common Attributes:**

* `language` → Programming language (default: Java).
* `contentType` → MIME type of response.
* `pageEncoding` → Character encoding (UTF-8 is recommended).
* `import` → Import Java packages/classes.
* `errorPage` → Page to forward to if an exception occurs.
* `isErrorPage` → Marks JSP as an error handler page.

**Example:**

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
         pageEncoding="UTF-8" import="java.util.Date" errorPage="error.jsp" %>

<html>
<body>
   <h2>Page Directive Example</h2>
   Current Time: <%= new Date() %>
</body>
</html>
```

**error.jsp**

```jsp
<%@ page isErrorPage="true" %>
<html><body>
   <h3>Error Occurred: <%= exception %></h3>
</body></html>
```

---

### 2. **Include Directive**

Includes another file into the JSP **at translation time** (static include).
Useful for reusing **header, footer, or menus**.

**Example:**

```jsp
<%@ include file="header.jsp" %>
<html>
<body>
   <h2>Main Content</h2>
   <p>This is the body of the page.</p>
</body>
</html>
<%@ include file="footer.jsp" %>
```

**header.jsp**

```jsp
<h1>Welcome to My Website</h1>
<hr>
```

**footer.jsp**

```jsp
<hr>
<p>Copyright © 2025</p>
```

---

### 3. **Taglib Directive**

Declares and uses **custom tag libraries** (like JSTL).
Makes JSP pages cleaner by reducing scriptlets.

**Example (Using JSTL Core Library):**

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<html>
<body>
   <h2>Taglib Directive Example</h2>

   <c:set var="name" value="Ekansh" />
   <c:if test="${name == 'Ekansh'}">
       <p>Hello, ${name}! Welcome back.</p>
   </c:if>
</body>
</html>
```

---

### ✅ Summary

* **Page Directive** → Configures JSP behavior (imports, encoding, error handling).
* **Include Directive** → Inserts static files (good for reusing headers/footers).
* **Taglib Directive** → Enables JSTL/custom tags, reducing Java code in JSP.

Directives help **structure JSP projects cleanly** and separate concerns between **logic and presentation**.

## 🔹 Exception Handling in JSP

Just like in Servlets, exceptions can occur in JSP (e.g., division by zero, null values, database errors). JSP provides a **built-in way** to handle them gracefully by using **error pages**.

---

### 1. Declaring an Error Page

* In the JSP where an error might occur, you specify:

  ```jsp
  <%@ page errorPage="error.jsp" %>
  ```
* If an exception occurs, the request is **forwarded** to `error.jsp`.

---

### 2. Creating the Error Page

* The error handling JSP must declare:

  ```jsp
  <%@ page isErrorPage="true" %>
  ```
* This makes the implicit object `exception` available.

---

### 🔹 Example

**home.jsp**

```jsp
<%@ page contentType="text/html; charset=UTF-8" errorPage="error.jsp" %>
<html>
<head><title>Home</title></head>
<body>
    <h2>Testing Exception Handling</h2>

    <%
        int k = 10 / 0;   // This will throw ArithmeticException
    %>

    <p>Result is: <%= k %></p>
</body>
</html>
```

---

**error.jsp**

```jsp
<%@ page isErrorPage="true" contentType="text/html; charset=UTF-8" %>
<html>
<head><title>Error Page</title></head>
<body style="color:red;">
    <h2>Oops! Something went wrong.</h2>
    <p>Error Details: <%= exception %></p>
</body>
</html>
```

---

### 3. How It Works

* `home.jsp` executes → exception occurs.
* Instead of crashing with **HTTP 500**, control goes to `error.jsp`.
* The implicit object `exception` (of type `Throwable`) contains error details.

---

### 4. Global Error Handling (Alternative via web.xml)

You can also define error pages for the **entire application** in `web.xml`:

```xml
<error-page>
   <exception-type>java.lang.ArithmeticException</exception-type>
   <location>/error.jsp</location>
</error-page>

<error-page>
   <error-code>404</error-code>
   <location>/notfound.jsp</location>
</error-page>
```

* First rule → handles specific exception types.
* Second rule → handles specific HTTP error codes.

---
## 🔹 Extension Notes

This section is **open for extension**.
You can add:

* [ ] `HelloServlet` example and `web.xml` configuration.
* [ ] Steps for switching Tomcat versions.
* [ ] Guide for integrating JSP.
* [ ] Steps for using annotations (`@WebServlet`) instead of `web.xml`.

---

✅ Following the **Open/Closed Principle**, this document is designed to stay stable. You only **extend** it by adding sections under **Extension Notes**, without modifying the base setup.
