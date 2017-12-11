---
title: "TasksAppWithSpringBoot"
date: 2017-12-09T22:14:40-05:00
draft: true
---

Build a spring framework app with spring boot and Angular.

(why it seems that spring framework is a little bit heavy...)

---

Step by step building this app.

Before get started, make sure **IntelliJ IDEA Ultimate version** installed. Community version does not have the Spring Initializer.

## 1.Create the Spring Boot Application

---

1. In the welcome page, click **Create New Project**
2. Select **Spring Initializr**, make sure **Project SDK** is Java 8, then leave other as default, then next.
3. Set up **Group** and **Artifact**.
4. For the section **Core**, check the box `DevTools` and `Lombok`. For the **Web**, check `Web`. For **SQL**, select `JPA` and `H2`. For **Ops**, choose `Actuator` and `Actuator Docs`.
5. Then all should be good to go and hit Finish.