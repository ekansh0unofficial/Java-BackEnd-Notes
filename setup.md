# Java Servlets Project Setup with IntelliJ Community Edition

This document explains how to set up and manage Java Servlets projects in **IntelliJ IDEA Community Edition**.
Since CE doesn’t ship with built-in Tomcat support, we rely on **external Apache Tomcat** and the **Cargo Maven plugin** for deployment.

---

## 📑 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [First Project Setup (One-Time)](#-first-project-setup-one-time)

   * [Install Apache Tomcat](#install-apache-tomcat)
   * [IntelliJ Project Setup](#intellij-project-setup)
   * [Configure `pom.xml`](#configure-pomxml)
   * [Project Structure](#project-structure)
   * [Running the Project](#running-the-project)
3. [Subsequent Projects](#-subsequent-projects)
4. [Using IntelliJ’s Bundled Maven](#-using-intellij’s-bundled-maven)
5. [Extension Notes](#-extension-notes)

---

## 🔹 Prerequisites

* **JDK 8 or 11** (Servlet API works best here).
* **IntelliJ IDEA Community Edition**.
* **Apache Tomcat** (downloaded separately).
* Internet access for Maven dependencies.

---

## 🔹 First Project Setup (One-Time)

### Install Apache Tomcat

1. Download Tomcat: [Official Downloads](https://tomcat.apache.org/download-10.cgi).
2. Extract to a directory, e.g., `C:\apache-tomcat-10`.
3. Configure user in `conf/tomcat-users.xml`:

   ```xml
   <tomcat-users>
      <role rolename="manager-gui"/>
      <role rolename="manager-script"/>
      <user username="admin" password="admin123" roles="manager-gui,manager-script"/>
   </tomcat-users>
   ```

---

### IntelliJ Project Setup

1. Open IntelliJ → **New Project** → Select **Maven**.
2. Provide:

   * `GroupId`: `com.example`
   * `ArtifactId`: `servlet-demo`
3. Choose JDK.

---

### Configure `pom.xml`

Add dependencies and Cargo plugin:

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>servlet-demo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <dependencies>
    <!-- Servlet API (Tomcat provides it at runtime) -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.cargo</groupId>
        <artifactId>cargo-maven2-plugin</artifactId>
        <version>1.9.13</version>
        <configuration>
          <container>
            <containerId>tomcat10x</containerId>
            <type>remote</type>
          </container>
          <configuration>
            <type>runtime</type>
            <properties>
              <cargo.remote.username>admin</cargo.remote.username>
              <cargo.remote.password>admin123</cargo.remote.password>
              <cargo.remote.uri>http://localhost:8080/manager/text</cargo.remote.uri>
            </properties>
          </configuration>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

---

### Project Structure

Manually create folders:

```
servlet-demo/
 ├── src/
 │   └── main/
 │       ├── java/            <-- Servlets
 │       └── webapp/
 │            ├── WEB-INF/
 │            │    └── web.xml
 │            └── index.html
 └── pom.xml
```

---

### Running the Project

1. Start Tomcat manually:

   * Windows: `bin/startup.bat`
   * Linux/Mac: `bin/startup.sh`
2. Deploy project:

   ```sh
   mvn clean package cargo:redeploy
   ```
3. Access at: `http://localhost:8080/servlet-demo/yourServlet`

---

## 🔹 Subsequent Projects

For new projects:

1. Create new **Maven project**.
2. Copy the **Cargo plugin** block from the first project’s `pom.xml`.
3. Update:

   * `artifactId`
   * Project name
4. Reuse the same **Tomcat installation**.
5. Deploy with:

   ```sh
   mvn clean package cargo:redeploy
   ```

---

## 🔹 Using IntelliJ’s Bundled Maven

* IntelliJ CE ships with Maven by default (bundled inside IDE).
* Verify via: **Settings → Build Tools → Maven → Use Bundled (Maven 3)**.
* No external Maven installation required.

---
