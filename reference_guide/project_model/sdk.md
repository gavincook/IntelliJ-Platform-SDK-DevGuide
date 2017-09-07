---
title: SDK
---

Every project uses a Software Development Kit (SDK). For Java projects, the SDK is referred to as the JDK (Java Development Kit). The SDK determines which API library is used to build the project. If your project is multi-module, the project SDK by default is common for all modules within the project. Optionally, you can configure individual SDKs for each module. For more information about SDKs, see [SDK](https://www.jetbrains.com/help/idea/working-with-sdks.html) in the IntelliJ IDEA Web Help.

## Getting project SDK information

Main information about the project SDK can be accessed via [ProjectRootManager.java](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ProjectRootManager.java) like the following example shows

```java
Sdk projectSdk = ProjectRootManager.getInstance(project).getProjectSdk();
```

## Getting and setting project SDK attributes

* To get the project level SDK

  ```java
  Sdk projectSDK = ProjectRootManager.getInstance(project).getProjectSdk();
  ```

* To get the project level SDK name:
 
  ```java 
  String projectSDKName = ProjectRootManager.getInstance(project).getProjectSdkName();
  ```

* To set the project level SDK:
 
  ```java
  ProjectRootManager.getInstance(project).setProjectSdk(Sdk jdk);
  ```

* To set the project level SDK name:
 
  ```java
  ProjectRootManager.getInstance(project).setProjectSdkName(String name);
  ```
  
See the following [code sample](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/project_model/src/com/intellij/tutorials/project/model/ProjectSdkAction.java) to get more familiar with SDK manipulation tool set.
