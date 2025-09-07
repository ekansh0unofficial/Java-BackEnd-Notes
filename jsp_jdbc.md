## 📘 JSP and JDBC Integration

JSP (JavaServer Pages) can directly interact with databases using JDBC (Java Database Connectivity). Although in real-world applications we prefer to separate business logic (JDBC code) into servlets or DAO classes, learning JDBC inside JSP gives a clear understanding of how database connectivity works in a web environment.

---

### 🔹 Steps for JDBC inside JSP

1. **Import JDBC package**

   ```jsp
   <%@ page import="java.sql.*" %>
   ```

   This makes JDBC classes like `Connection`, `DriverManager`, `Statement`, and `ResultSet` available.

2. **Load and register the driver**

   ```jsp
   Class.forName("com.mysql.cj.jdbc.Driver");
   ```

   This tells the JVM to load the MySQL driver class.

3. **Create a connection**

   ```jsp
   String url = "jdbc:mysql://localhost:3306/placement_db?useSSL=false&serverTimezone=UTC";
   Connection con = DriverManager.getConnection(url, "root", "1234");
   ```

4. **Create a statement**

   ```jsp
   Statement st = con.createStatement();
   ```

5. **Execute query**

   ```jsp
   ResultSet rs = st.executeQuery("SELECT student_id, name, branch FROM students");
   ```

6. **Process result**

   ```jsp
   while (rs.next()) {
       out.println("ID: " + rs.getInt("student_id") + "<br>");
       out.println("Name: " + rs.getString("name") + "<br>");
       out.println("Branch: " + rs.getString("branch") + "<hr>");
   }
   ```

---

### 🔹 Ways to Set Up the JDBC Driver in Tomcat

Since JSPs and servlets run **inside Tomcat’s JVM**, Tomcat must be able to load the JDBC driver JAR file. There are multiple ways to achieve this:

#### ✅ 1. Global Setup (Tomcat `lib/`)

* Place `mysql-connector-java-x.x.xx.jar` into:

  ```
  <TOMCAT_HOME>/lib/
  ```
* Restart Tomcat.
* All webapps deployed on this Tomcat can use the driver.
* Best for: local learning environments or when many apps share the same DB.

#### ✅ 2. Project-Specific Setup (`WEB-INF/lib/`)

* Inside your project, place the driver JAR inside:

  ```
  src/main/webapp/WEB-INF/lib/
  ```
* When the WAR is built, the JAR is bundled automatically.
* Only that project can use the driver.
* Best for: real-world deployable applications (self-contained, no dependency on Tomcat installation).

#### ✅ 3. Maven Dependency (preferred in modern projects)

Add dependency in `pom.xml`:

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.4.0</version>
</dependency>
```

* When you package (`mvn clean package`), Maven automatically copies the driver into `WEB-INF/lib/`.
* Best for: enterprise projects, easy version upgrades, avoids manual JAR handling.

---

### 🔹 Debugging Checklist

* **ClassNotFoundException** → Driver JAR not in Tomcat’s classpath.
* **Access Denied** → Wrong username/password.
* **Unknown Database** → DB name doesn’t exist.
* **Communications Link Failure** → MySQL service not running or wrong port.

---

### 🔹 Example JSP with JDBC

```jsp
<%@ page import="java.sql.*" %>
<html>
<body>
<%
    try {
        Class.forName("com.mysql.cj.jdbc.Driver");
        Connection con = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/placement_db?useSSL=false&serverTimezone=UTC",
            "root", "1234"
        );
        Statement st = con.createStatement();
        ResultSet rs = st.executeQuery("SELECT student_id, name, branch FROM students");

        while (rs.next()) {
            out.println("ID: " + rs.getInt("student_id") + "<br>");
            out.println("Name: " + rs.getString("name") + "<br>");
            out.println("Branch: " + rs.getString("branch") + "<br><hr>");
        }
        con.close();
    } catch (Exception e) {
        out.println("❌ Error: " + e);
    }
%>
</body>
</html>
```

---

✅ With this, you’ve learned not just how to connect JSP ↔ MySQL, but also the **three main ways of setting up JDBC driver** (global, project-specific, and Maven).

## 🔹 Extension Notes

This section is **open for extension**.
You can add:

* [ ] `HelloServlet` example and `web.xml` configuration.
* [ ] Steps for switching Tomcat versions.
* [ ] Guide for integrating JSP.
* [ ] Steps for using annotations (`@WebServlet`) instead of `web.xml`.

---

✅ Following the **Open/Closed Principle**, this document is designed to stay stable. You only **extend** it by adding sections under **Extension Notes**, without modifying the base setup.
