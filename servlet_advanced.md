### 🔹 ServletContext vs ServletConfig

#### 📌 ServletContext

* Represents the **application-wide context** (shared across all servlets).
* Defined in `web.xml` under `<context-param>`.
* Values are available to **all servlets** in the application.
* Retrieved using:

  ```java
  ServletContext ctx = getServletContext();
  ```

---

#### 📌 ServletConfig

* Represents the **configuration of a single servlet**.
* Defined in `web.xml` under `<init-param>` inside a `<servlet>` tag.
* Values are available **only to that servlet**.
* Retrieved using:

  ```java
  ServletConfig cg = getServletConfig();
  ```

---

#### 📌 web.xml Example

```xml
<web-app>
    <!-- Context-wide parameters -->
    <context-param>
        <param-name>name</param-name>
        <param-value>Ekansh</param-value>
    </context-param>
    <context-param>
        <param-name>laptop</param-name>
        <param-value>Dell</param-value>
    </context-param>

    <!-- Servlet-specific config -->
    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>com.ekansh.MyServlet</servlet-class>
        <init-param>
            <param-name>name</param-name>
            <param-value>Ekansh1</param-value>
        </init-param>
    </servlet>

    <servlet-mapping>
        <servlet-name>MyServlet</servlet-name>
        <url-pattern>/my</url-pattern>
    </servlet-mapping>
</web-app>
```

---

#### 📌 MyServlet.java

```java
package com.ekansh;

import jakarta.servlet.*;
import jakarta.servlet.http.*;
import java.io.IOException;

public class MyServlet extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        ServletContext ctx = getServletContext();   // application-wide
        ServletConfig cg = getServletConfig();      // servlet-specific

        String contextName = ctx.getInitParameter("name");     // Ekansh
        String contextLaptop = ctx.getInitParameter("laptop"); // Dell
        String configName = cg.getInitParameter("name");       // Ekansh1

        res.getWriter().println("Context Name: " + contextName);
        res.getWriter().println("Context Laptop: " + contextLaptop);
        res.getWriter().println("Config Name: " + configName);
    }
}
```

---

#### ✅ Key Points

* **ServletContext**

  * Shared by **all servlets** in the app.
  * Good for **application-level configuration** (like DB connection info, company name, global settings).
* **ServletConfig**

  * Unique to a single servlet.
  * Good for **servlet-specific settings** (like servlet name, init params for that servlet).
* If the same parameter name exists in both:

  * `ServletConfig` value overrides **for that servlet**.
  * Other servlets still see the `ServletContext` value.


### 🔹 @WebServlet Annotation

Instead of configuring servlets in `web.xml`, we can use annotations directly in the servlet class.

```java
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;
import java.io.IOException;

@WebServlet("/add")
public class AddServlet extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        int i = Integer.parseInt(req.getParameter("num1"));
        int j = Integer.parseInt(req.getParameter("num2"));
        int k = i + j;

        res.getWriter().println("Result: " + k);
    }
}
```

```java
@WebServlet("/sq")
public class SquareServlet extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        int k = Integer.parseInt(req.getParameter("k"));
        int square = k * k;

        res.getWriter().println("Square is: " + square);
    }
}
```

✅ Now you don’t need to declare them in `web.xml`.

---

### 🔹 What about Config & Context Parameters without XML?

You’re right — if we stop using `web.xml`, **how do we pass init params or context params?**
There are two options:

---

#### 1. ServletConfig with `@WebInitParam`

```java
import jakarta.servlet.annotation.WebInitParam;

@WebServlet(
    urlPatterns = "/my",
    initParams = {
        @WebInitParam(name = "name", value = "Ekansh1"),
        @WebInitParam(name = "role", value = "Developer")
    }
)
public class MyServlet extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        ServletConfig cg = getServletConfig();
        String name = cg.getInitParameter("name");   // Ekansh1
        String role = cg.getInitParameter("role");   // Developer

        res.getWriter().println("Config Name: " + name);
        res.getWriter().println("Role: " + role);
    }
}
```

---

#### 2. ServletContext with `@WebListener` or External Config

For **application-wide (context) parameters**, there are two common ways:

* **Option A: Still use `web.xml` only for `<context-param>`**
  Even if servlets are annotated, you can keep `web.xml` just for global values.

* **Option B: Use a `ServletContextListener`**
  Example:

  ```java
  import jakarta.servlet.*;
  import jakarta.servlet.annotation.*;

  @WebListener
  public class AppConfigListener implements ServletContextListener {
      public void contextInitialized(ServletContextEvent sce) {
          ServletContext ctx = sce.getServletContext();
          ctx.setAttribute("appName", "My Web App");
          ctx.setAttribute("version", "1.0");
      }
  }
  ```

  Then in any servlet:

  ```java
  String appName = getServletContext().getAttribute("appName").toString();
  ```

---

#### ✅ Summary

* Use `@WebServlet` + `@WebInitParam` → replaces `<servlet>` + `<init-param>`.
* Use `@WebListener` or `web.xml <context-param>` → for global values (ServletContext).
* In modern practice:

  * `@WebServlet` for servlet mappings
  * `@WebInitParam` for servlet-specific configs
  * `@WebListener` or frameworks (like Spring) for application-wide configs



## 🔹 Extension Notes

This section is **open for extension**.
You can add:

* [ ] `HelloServlet` example and `web.xml` configuration.
* [ ] Steps for switching Tomcat versions.
* [ ] Guide for integrating JSP.
* [ ] Steps for using annotations (`@WebServlet`) instead of `web.xml`.

---

✅ Following the **Open/Closed Principle**, this document is designed to stay stable. You only **extend** it by adding sections under **Extension Notes**, without modifying the base setup.
