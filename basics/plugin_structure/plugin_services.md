---
title: Plugin Services
---

*IntelliJ平台*提供了服务的概念。

当调用[ServiceManager](upsource:///platform/core-api/src/com/intellij/openapi/components/ServiceManager.java)的`getService`时，服务会按需加载到插件中。

*IntelliJ平台*会保证多次服务加载得到的服务都是单例对象。服务必须有一个实现类用于初始化。可以通过服务接口来对外提供API和获取服务实例。服务的实现类和接口都在`plugin.xml`进行配置。

*IntelliJ平台*提供了三种类型的服务：应用级别服务、项目级别服务以及模块级别服务。

## 如何声明一个服务？

可以使用如下IDEA核心扩展点来声明服务：

* `applicationService`：用于声明应用级别服务。
* `projectService`：用于声明项目级别服务。
* `moduleService`：用于声明模块级别服务。

**声明服务:**

1. 在`plugin.xml`的`<extensions>`部分添加对应的子标签（`<applicationService>`、`<projectService>` 或 `<moduleService>`）
2. 为新增的子标签设置如下属性：
    * `serviceInterface`：指定服务的接口。
    * `serviceImplementation`：指定服务的实现类。

如果没有指定`serviceInterface`，则会假定其和`serviceImplementation`具有一样的值。

对于服务声明，参见如下示例，

```xml
<extensions defaultExtensionNs="com.intellij">
  <!-- Declare the application level service -->
  <applicationService serviceInterface="Mypackage.MyApplicationService" serviceImplementation="Mypackage.MyApplicationServiceImpl" />

  <!-- Declare the project level service -->
  <projectService serviceInterface="Mypackage.MyProjectService" serviceImplementation="Mypackage.MyProjectServiceImpl" />
</extensions>
```

## 获取服务实例

在Java代码中，使用如下方式进行服务实例化：

```java
MyApplicationService applicationService = ServiceManager.getService(MyApplicationService.class);

MyProjectService projectService = ServiceManager.getService(project, MyProjectService.class);

MyModuleService moduleService = ModuleServiceManager.getService(module, MyModuleService.class);
```

### 示例插件

本章节通过下载并安装示例插件来说明如何创建和使用插件服务。该插件有一个用于计数当前IDE中打开的项目个数的项目级别服务。如果打开的项目个数达到插件允许的最大个数，则插件会返回一个异常信息并关闭最近打开最多的项目。

**安装运行示例插件**

* 下载[示例插件项目](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/max_opened_projects).
* 启动*IDEA*，在起始页点击*Open Project*，然后在弹出对话框中选择下载的项目*max_opened_projects*。
* 在IDEA主菜单，选择*Run \| Run* 或 使用快捷键 <kbd>Shift</kbd>+<kbd>F10</kbd>。
* 如果有必要，可以变更[运行/调试配置](http://www.jetbrains.com/idea/help/run-debug-configuration-plugin.html)。

