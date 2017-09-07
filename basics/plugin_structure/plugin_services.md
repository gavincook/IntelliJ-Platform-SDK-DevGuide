---
title: Plugin Services
---

The *IntelliJ Platform* provides the concept of _services_.

A _service_ is a plugin component loaded on demand, when your plugin calls the `getService` method of the [ServiceManager](upsource:///platform/core-api/src/com/intellij/openapi/components/ServiceManager.java) class.

The *IntelliJ Platform* ensures that only one instance of a service is loaded even though the service is called several times. A service must have an implementation class which is used for service instantiation. A service may also have an interface class which is used to obtain the service instance and provides API of the service. The interface and implementation classes are specified in the `plugin.xml` file.

The *IntelliJ Platform* offers three types of services: _application level_ services, _project level_ services and _module level_ services.

## How to Declare a Service?

To declare a service, you can use the following extension points in the IDEA core:

* `applicationService`: designed to declare an application level service.
* `projectService`: designed to declare a project level service.
* `moduleService`: designed to declare a module level service.

**To declare a service:**

1. Add the appropriate child element (`<applicationService>`, `<projectService>` or `<moduleService>`) to the `<extensions>` section of the plugin.xml file.
2. For the newly added child element, set the following attributes:
    * `serviceInterface`: specifies the service interface class.
    * `serviceImplementation`: specifies the service implementation class.

If `serviceInterface` isn't specified it's supposed to have the same value as `serviceImplementation`.

To clarify the service declaration procedure, consider the following fragment of the `plugin.xml` file:

```xml
<extensions defaultExtensionNs="com.intellij">
  <!-- Declare the application level service -->
  <applicationService serviceInterface="Mypackage.MyApplicationService" serviceImplementation="Mypackage.MyApplicationServiceImpl" />

  <!-- Declare the project level service -->
  <projectService serviceInterface="Mypackage.MyProjectService" serviceImplementation="Mypackage.MyProjectServiceImpl" />
</extensions>
```

## Retrieving a service

To instantiate your service, in Java code, use the following syntax:

```java
MyApplicationService applicationService = ServiceManager.getService(MyApplicationService.class);

MyProjectService projectService = ServiceManager.getService(project, MyProjectService.class);

MyModuleService moduleService = ModuleServiceManager.getService(module, MyModuleService.class);
```

### Sample Plugin

This section allows you to download and install a sample plugin illustrating how to create and use a plugin service. This plugin has a project component implementing a service that counts the number of currently opened projects in the IDE. If this number exceeds the maximum allowed number of simultaneously opened projects, the plugin returns an error message and closes the most recently opened project.

<!-- TODO Replace with other plugin URL when available-->

**To install and run the sample plugin**

* Download the included sample plugin project located [here](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/max_opened_projects).
* Start *IntelliJ IDEA*, on the starting page, click *Open Project*, and then use the *Open Project* dialog box to open the project *max_opened_projects*.
* On the main menu, choose *Run \| Run* or press <kbd>Shift</kbd>+<kbd>F10</kbd>.
* If necessary, change the [Run/Debug Configurations](http://www.jetbrains.com/idea/help/run-debug-configuration-plugin.html).
