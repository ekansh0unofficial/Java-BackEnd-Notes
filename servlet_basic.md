
## 🔹 Understanding Servlets

### What is a Servlet?

A **Servlet** is a Java class that runs on a server and handles requests from clients (e.g., browsers).

* They act as **middleware**, sitting between the client and backend resources.
* They process incoming requests (like form data, query parameters, API calls), perform logic (such as database access, calculations), and return a **dynamic response** (HTML, JSON, XML, etc.).
* Use case: Whenever we need **dynamic content** instead of static HTML.

---

### Servlet Mapping

* Servlets are mapped to specific URL patterns so the server knows **which servlet handles which request**.
* This mapping can be defined in:

  * `web.xml` (traditional way).
  * `@WebServlet` annotation (modern way).

Example (`web.xml`):

```xml
<servlet>
    <servlet-name>abc</servlet-name>
    <servlet-class>com.ekansh.AddServlet</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>abc</servlet-name>
    <url-pattern>/add</url-pattern>
</servlet-mapping>
```

---

### Servlet Lifecycle Methods

A servlet extends `HttpServlet` and typically overrides:

* `service(req, res)` → handles all requests.
* `doGet(req, res)` → handles HTTP GET.
* `doPost(req, res)` → handles HTTP POST.

> ✅ Best practice: use `doGet` and `doPost` depending on the request type.

---

### Example: Adding Two Numbers

**index.html**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Add Numbers</title>
</head>
<body>
    <form action="add" method="get">
        Number 1: <input type="text" name="num1"><br>
        Number 2: <input type="text" name="num2"><br>
        <input type="submit" value="Add">
    </form>
</body>
</html>
```

**AddServlet.java**

```java
package com.ekansh;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.*;
import javax.servlet.http.*;

public class AddServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        int num1 = Integer.parseInt(req.getParameter("num1"));
        int num2 = Integer.parseInt(req.getParameter("num2"));
        int sum = num1 + num2;

        PrintWriter out = res.getWriter();
        out.println("<h1>Result: " + sum + "</h1>");
    }
}
```

---

### Flow

1. Client submits form → `/add` request sent.
2. Tomcat checks `web.xml` → maps `/add` → `AddServlet`.
3. `doGet()` is executed → adds numbers → sends result back to browser.

---

### 🔹 Request Methods: GET vs POST

**index.html**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Servlet Demo</title>
</head>
<body>
<h1>Hello World</h1>

<h3>Using GET</h3>
<form action="add" method="get">
    Enter 1st Number : <input type="text" name="num1"><br>
    Enter 2nd Number : <input type="text" name="num2"><br>
    <input type="submit" value="Submit (GET)">
</form>

<h3>Using POST</h3>
<form action="add" method="post">
    Enter 1st Number : <input type="text" name="num1"><br>
    Enter 2nd Number : <input type="text" name="num2"><br>
    <input type="submit" value="Submit (POST)">
</form>
</body>
</html>
```

**AddServlet.java**

```java
package com.ekansh;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

public class AddServlet extends HttpServlet {
    public void doGet(HttpServletRequest req , HttpServletResponse res) throws IOException, ServletException {
        int i = Integer.parseInt(req.getParameter("num1"));
        int j = Integer.parseInt(req.getParameter("num2"));
        int k = i+j;
        PrintWriter out = res.getWriter();
        out.print("Result :" + k );
    }
    public void doPost(HttpServletRequest req , HttpServletResponse res) throws IOException{
        int i = Integer.parseInt(req.getParameter("num1"));
        int j = Integer.parseInt(req.getParameter("num2"));
        int k = i+j;
        PrintWriter out = res.getWriter();
        out.print("Result :" + k );
    }
}
```

#### Key Points:

* **GET**

  * Parameters are visible in the **URL** (query string).
  * Example: `/add?num1=10&num2=20`
  * Best for read-only or idempotent requests.

* **POST**

  * Parameters are sent in the **request body**, not visible in the URL.
  * Better for sensitive data and larger payloads.
  * Commonly used in form submissions that modify server state.

✅ Observation: Switching from `method="get"` to `method="post"` in an HTML `<form>` changes how data is transmitted and displayed in the browser.

### 🔹 RequestDispatcher and Request Attributes

Sometimes, one servlet needs to forward a request to another servlet.  
For this, we use the **RequestDispatcher** interface.  

This is useful when you want to pass control (and data) from one servlet to another **without involving the client/browser again**.

---

#### 📌 Key Concepts

* **RequestDispatcher** is obtained from the `HttpServletRequest` object:
  ```java
  RequestDispatcher rd = req.getRequestDispatcher("sq");
  rd.forward(req, res);
* `forward(req, res)` → hands over the same request and response to the target servlet.
* The client/browser is **not aware** of this forwarding → URL in the browser does **not change**.
* Data can be shared between servlets using **request attributes**:

  ```java
  req.setAttribute("key_name", value);
  ```

---

#### 📝 Example

**AddServlet.java**

```java
package com.ekansh;

import jakarta.servlet.RequestDispatcher;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

public class AddServlet extends HttpServlet {
    public void doGet(HttpServletRequest req , HttpServletResponse res) throws IOException, ServletException {
        int i = Integer.parseInt(req.getParameter("num1"));
        int j = Integer.parseInt(req.getParameter("num2"));
        int k = i + j;

        // set attribute to share data with the next servlet
        req.setAttribute("value", k);

        // forward request to SquareServlet
        RequestDispatcher rd = req.getRequestDispatcher("sq");
        rd.forward(req, res);
    }

}
```

**SquareServlet.java**

```java
package com.ekansh;

import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

public class SquareServlet extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        // retrieve attribute passed from AddServlet
        int k = (int) req.getAttribute("value");
        int square = k * k;

        res.getWriter().println("Square is: " + square);
    }
}
```

---

#### ✅ Summary

* Use `RequestDispatcher` for **server-side forwarding**.
* Unlike `sendRedirect()`, the browser URL does **not** change.
* Data can be shared between servlets using `req.setAttribute()` and `req.getAttribute()`.
* Great for chaining logic across multiple servlets (e.g., `AddServlet → SquareServlet`).
---

### 🔹 sendRedirect() vs RequestDispatcher

* **sendRedirect()**

  * Response is sent back to the **client’s browser** with a new URL.
  * Browser makes a **new request** to that URL.
  * The URL in the browser **changes**.
  * Any data stored as a request attribute is **lost**.
  * Useful when redirecting to another domain, external site, or ensuring a new request cycle.

* **RequestDispatcher**

  * Request is forwarded **internally** on the server.
  * Browser does **not** know about the forwarding.
  * URL remains the **same**.
  * Request attributes are preserved.
  * Useful for chaining servlets on the server side.

---

### 🔹 URL Rewriting (Data Sharing with sendRedirect)

Since `sendRedirect()` does not preserve request attributes, we can pass data using **URL rewriting**.

**AddServlet.java**

```java
public void doGet(HttpServletRequest req , HttpServletResponse res) throws IOException, ServletException {
    int i = Integer.parseInt(req.getParameter("num1"));
    int j = Integer.parseInt(req.getParameter("num2"));
    int k = i + j;

    // Example of URL rewriting to send data along with redirect
    res.sendRedirect("sq?k=" + k);
}
```

**SquareServlet.java**

```java
public void doGet(HttpServletRequest req , HttpServletResponse res) throws IOException {
    int k = Integer.parseInt(req.getParameter("k"));
    int square = k * k;
    res.getWriter().println("Square is: " + square);
}
```

✅ With **URL rewriting**, data is sent as part of the **query string**.

### 🔹 Cookies in Servlets

#### 📌 What is a Cookie?

* A **cookie** is a small piece of data stored on the **client’s browser**.
* It is sent by the server in the response, and the browser automatically sends it back in subsequent requests.
* Useful for storing **small, client-side information** (preferences, tokens, session IDs).

---

#### 📌 Working with Cookies

* **Create a Cookie**

```java
Cookie cookie = new Cookie("sum", k + "");
res.addCookie(cookie); // add cookie to response
```

* **Read Cookies**

```java
Cookie[] cookies = req.getCookies(); // returns an array of cookies
for (Cookie c : cookies) {
    if (c.getName().equals("sum")) {
        int sum = Integer.parseInt(c.getValue());
    }
}
```

---

#### 📝 Example

**AddServlet.java**

```java
package com.ekansh;

import jakarta.servlet.http.*;
import java.io.IOException;

public class AddServlet extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        int i = Integer.parseInt(req.getParameter("num1"));
        int j = Integer.parseInt(req.getParameter("num2"));
        int k = i + j;

        // create cookie and add to response
        Cookie cookie = new Cookie("sum", k + "");
        res.addCookie(cookie);

        res.sendRedirect("sq");
    }
}
```

**SquareServlet.java**

```java
package com.ekansh;

import jakarta.servlet.http.*;
import java.io.IOException;

public class SquareServlet extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        int sum = 0;

        // read cookies
        Cookie[] cookies = req.getCookies();
        for (Cookie c : cookies) {
            if (c.getName().equals("sum")) {
                sum = Integer.parseInt(c.getValue());
            }
        }

        int square = sum * sum;
        res.getWriter().println("Square is: " + square);
    }
}
```

---

#### ❓ Why does `getCookies()` return a list (array), not a map?

* A request can contain **multiple cookies with the same name** (though uncommon).
* Example: Two different paths or domains might both set a `sessionId` cookie.
* To preserve **all values**, `getCookies()` returns an **array** instead of a map (maps would overwrite duplicates).

---

#### ❓ Why use Cookies if we already have Sessions?

* **Sessions** internally use **cookies** to track users!

  * By default, a session ID is stored in a cookie named `JSESSIONID`.
* **Cookies vs Sessions**:

  * Cookies → stored on **client-side**, good for **lightweight, non-sensitive data** (preferences, themes).
  * Sessions → stored on **server-side**, better for **sensitive or larger data** (login info, cart).
* If cookies are disabled in the browser, sessions can fall back to **URL rewriting**.

---

#### ✅ Key Points

* Cookies are **client-side storage**, sessions are **server-side storage**.
* Cookies persist even **after the browser is closed** (if given an expiry).
* Sessions last until the user logs out, session times out, or is invalidated.
* **Good practice**:

  * Use **sessions** for sensitive info.
  * Use **cookies** for simple client preferences (language, theme, remember-me options).


---

### 🔹 Comparison: Request Attributes vs Session vs Cookies vs URL Rewriting

| Feature              | Request Attribute                                           | Session                                        | Cookie                                                  | URL Rewriting                                               |
| -------------------- | ----------------------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------- | ----------------------------------------------------------- |
| **Scope / Lifetime** | Only for a **single request** (lost after forward/redirect) | Lasts until session timeout or invalidate      | Depends on expiry (can persist across browser restarts) | Only for the redirected request (data in query string)      |
| **Storage Location** | **Server-side** (inside request object)                     | **Server-side** (session object)               | **Client-side** (browser)                               | **Client-side** (URL itself)                                |
| **Data Sharing**     | Between servlets in **one request only**                    | Across multiple servlets and requests          | Across multiple requests (as long as cookie exists)     | Passes data between requests via URL                        |
| **Data Size**        | Small (in-memory, request scoped)                           | Larger (server memory)                         | Very small (usually < 4KB per cookie)                   | Very small (limited by URL length)                          |
| **Visibility**       | Not visible to client                                       | Not visible to client                          | Visible in browser (can be modified by user)            | Visible in browser URL                                      |
| **Persistence**      | Ends after request completes                                | Ends after session timeout/invalidate          | Can persist for days/weeks (until expiry)               | Ends after request                                          |
| **Use Cases**        | Passing values between chained servlets                     | Login/authentication, shopping cart, user data | Remember-me login, theme, language settings             | Redirecting with query params, fallback if cookies disabled |

---

✅ **Quick Tip:**

* Use **Request Attributes** → for immediate forwarding between servlets.
* Use **Sessions** → for user-specific data across pages (secure, server-controlled).
* Use **Cookies** → for lightweight, client-side preferences.
* Use **URL Rewriting** → fallback when cookies are disabled.



## 🔹 Extension Notes

This section is **open for extension**.
You can add:

* [ ] `HelloServlet` example and `web.xml` configuration.
* [ ] Steps for switching Tomcat versions.
* [ ] Guide for integrating JSP.
* [ ] Steps for using annotations (`@WebServlet`) instead of `web.xml`.

---

✅ Following the **Open/Closed Principle**, this document is designed to stay stable. You only **extend** it by adding sections under **Extension Notes**, without modifying the base setup.
