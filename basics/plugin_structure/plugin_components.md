---
title: Plugin Components
---
组件是插件组成的基本单位。有如下三种类型组件：

* **应用级别组件**：随IDE启动而创建和初始化。可以从[Application](upsource:///platform/core-api/src/com/intellij/openapi/application/Application.java)实例中通过`getComponent(Class)`获取。
* **项目级别组件**：会为IDE中不同的[Project](upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java)创建对应的组件实例。可使用`Project`实例的`getComponent(Class)`方法获取。
* **模块级别组件** ：会为项目中不同 [`Module`](upsource:///platform/core-api/src/com/intellij/openapi/module/Module.java)创建对应的组件实例。可通过`Module`实例的`getComponent(Class)`来获取。

在配置文件中，应为每个组件配置接口和实现类。接口类用于从其他组件获取当前组件使用，实现类则用于实例化当前组件。
注意：对于相同级别的两个组件（[应用级别](upsource:///platform/core-api/src/com/intellij/openapi/application/Application.java), [项目级别](upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java) 或 [模块级别](upsource:///platform/core-api/src/com/intellij/openapi/module/Module.java)）不能使用同一个接口类。否则这两个组件会使用同一个接口和实现类。

组件名字为`getComponentName()`返回的字符，其具有唯一性，并用于外部扩展或者内部使用。


## 组件命名规范
推荐使用`插件名.组件名`规范进行组件命名。


## 应用级别组件
应用级别组件实现类推荐实现[ApplicationComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ApplicationComponent.java) 接口。

如果一个应用级别组件没有任何依赖，则其应该有一个无参构造函数，用于组件初始化。如果依赖其他组件，则可以在构造函数的参数中指定需要依赖组件。*IntelliJ平台*会保证组件以正确的顺序进行初始化，来实现组件的依赖关系。

应用级别组件必须在`plugin.xml`中的`<application-components>`部分进行注册（参见：[插件配置](plugin_configuration_file.md)）。

### 快速创建应用级别组件

*IntelliJ平台*提供了简单的创建应用组件的方式，而无需关心底层基础结构。

IntelliJ平台会帮助定义组件实现类并自动在`plugin.xml`中的 `<application-components>`部分进行注册。

**创建并注册应用级别组件:**

1. 在项目中需要创建应用实现类的包上右键点击，并选择*New*（或者使用快捷键：<kbd>Alt</kbd>+<kbd>Insert</kbd>）。
2. 在*New*的子菜单中，点击*Application Component*。
3. 在*New Application Component*对话框中，输入组件名字并点击*OK*。

*IntelliJ平台*会生成一个实现于接口[ApplicationComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ApplicationComponent.java)的类。并在`plugin.xml`中进行注册，接着刷新模块视图，在编辑器中打开该类。

## 项目级别组件

项目级别的组件推荐实现[ProjectComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ProjectComponent.java)接口。

项目级别组件可以通过构造入参获得[项目](upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java)实例引用。也可以通过构造入参依赖其他应用级别或项目级别组件。

注意：项目级别组件必须在`plugin.xml`中的`<project-components>`部分进行配置（参见：[插件配置文件](plugin_configuration_file.md)）。

### 快速创建项目级别组件

*IntelliJ平台*提供了简单的创建项目组件的方式，而无需关心底层基础结构。

IDEA界面会帮助定义组件实现类并自动在`plugin.xml`中的 `<<project-components>>`部分进行注册。

**创建并注册项目级别组件**

1. 在项目中，需要创建组件的包上点击右键，然后在弹出菜单点击*New* (或者使用快捷键 <kbd>Alt</kbd>+<kbd>Insert</kbd>)。
2. 在*New*子菜单，点击*Project Component*。
3. 在*New Project Component* 对话框，输入组件名称，然后点击*OK*。

*IntelliJ平台*会生成一个实现于接口[ProjectComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ProjectComponent.java) 的类。并在`plugin.xml`中进行注册，接着刷新模块视图，在编辑器中打开该类。

## 模块级别组件

模块级别的组件推荐实现[ModuleComponent](upsource:///platform/core-api/src/com/intellij/openapi/module/ModuleComponent.java)接口。

模块级别组件可以通过构造入参获得模块实例引用。也可以通过构造入参依赖其他应用级别、项目级别或模块级别组件。

注意：项目级别组件必须在`plugin.xml`中的`<module-components>`部分进行配置（参见：[插件配置文件](plugin_configuration_file.md)）。

### 快速创建模块级别组件

*IntelliJ平台*提供了简单的创建项目组件的方式，而无需关心底层基础结构。

IDEA界面会帮助定义组件实现类并自动在`plugin.xml`中的 `<module-components>`部分进行注册。

**创建并注册项目级别组件**

1. 在项目中，需要创建组件的包上点击右键，然后在弹出菜单点击*New* (或者使用快捷键 <kbd>Alt</kbd>+<kbd>Insert</kbd>)。
2. 在*New*子菜单，点击*Module Component*。
3. 在*New Module Component* 对话框，输入组件名称，然后点击*OK*。

*IntelliJ平台*会生成一个实现于接口[ModuleComponent](upsource:///platform/core-api/src/com/intellij/openapi/module/ModuleComponent.java) 的类。并在`plugin.xml`中进行注册，接着刷新模块视图，在编辑器中打开该类。

## 保存组件状态
如果组件实现了[JDOMExternalizable](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java) (过时)或[PersistentStateComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/PersistentStateComponent.java)接口，那么其状态都会自动保存和加载。

当组件实现了 [PersistentStateComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/PersistentStateComponent.java) 接口，用[@State](upsource:///platform/projectModel-api/src/com/intellij/openapi/components/State.java) 和 [@Storage](upsource:///platform/projectModel-api/src/com/intellij/openapi/components/Storage.java) 标示的状态会会存储到xml文件中。

当组件实现[JDOMExternalizable](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)接口，组件会将状态存储到如下文件中：

* 项目级别的组件存储到项目文件中（`.ipr`文件）。
    
  然而，如果在`plugin.xml`中将组件的workspace选项置为`ture`，则该组件的配置信息存储到工作区间文件（`.iws`）中。
  
* 模块级别的组件存储组件状态到模块文件（`.iws`）中。

更多的信息和示例，参见[持久化组件状态](/basics/persisting_state_of_components.md)。

## 默认值

组件的默认值（预定义的配置）会存放在`<component_name>.xml`中。将该配置文件放到插件的classpath下的默认包路径文件夹中。当解析配置中的`<component>`标签时会调用`readExternal()`方法。

当组件有默认值时，`readExternal()`方法会调用两次：

* 第一次加载默认值
* 第二次加载存储的配置

## 插件组件生命周期

组件按照如下顺序进行加载：

* 创建 - 调用构造方法
* 初始化 - 如果组件实现[ApplicationComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ApplicationComponent.java)接口，则调用`initComponent`方法
* 配置 - 如果组件实现[JDOMExternalizable](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)接口，则调用其`readExternal`方法；如果组件实现[PersistentStateComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/PersistentStateComponent.java)接口，则调用`loadState`方法，此方式下组件没有默认值。
* 对于模块级别组件，基于接口 [ModuleComponent](upsource:///platform/core-api/src/com/intellij/openapi/module/ModuleComponent.java) 的组件，会调用其`moduleAdded`方法，用于通知模块已经添加到了项目中。
* 对于项目级别组件，基于接口[ProjectComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ProjectComponent.java)的组件，会调用`projectOpened`来触发项目加载完成事件。

组件按照如下顺序进行卸载：

* 保存配置 - 如果组件实现了[JDOMExternalizable](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)接口，则调用组件的`writeExternal`方法；如果实现PersistentStateComponent接口，则调用其`getState`方法。
* 清理 - 调用组件的`disposeComponent`方法

注意：不应该再组件的构造方法中使用`getComponent`来依赖其他组件，否则会得到一个异常断言。如果需要在组件初始化时，依赖其他组件，则可以通过构造函数参数或者在`initComponent`中访问其他组件。

