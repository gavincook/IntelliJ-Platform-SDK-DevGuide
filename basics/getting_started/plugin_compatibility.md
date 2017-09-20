#插件兼容性
所有基于IntelliJ平台的产品，如IntelliJ IDEA，RubyMine，WebStorm，PhpStorm，PyCharm，AppCode等，都使用的同一套底层API。因此，如果一个插件没有提供Java相关的功能，那么也可以在除了IDEA之外的其他产品中使用。这个特性可以使用在`plugin.xml`中的模块依赖进行配置。
模块依赖使用`<depends>`标签配置，模块名称一般以`com.inteellij.modules.`开头。
比如：

```xml
<idea-plugin>
  ...
  <depends>com.intellij.modules.lang</depends>
  ...
</idea-plugin>
```
如果在`plugin.xml`没有配置任何模块依赖，那么该插件会处理为兼容无模块配置之前插件，且只会被IDEA加载。
一旦`plugin.xml`有一个或多个模块配置，插件仅会在IntelliJ产品具有所有配置模块的特性时加载。

如下几个模块为基于IntelliJ平台产品都具有的特性：
* `com.intellij.modules.platform`
* `com.intellij.modules.lang`
* `com.intellij.modules.vcs`
* `com.intellij.modules.xml`
* `com.intellij.modules.xdebugger`

如下模块只会在特定的产品中提供：

| 模块 | 产品 |
| --- | --- |
| com.intellij.modules.java | IntelliJ  IDEA  |
| com.intellij.modules.ultimate|IntelliJ IDEA Ultimate Edition|
|com.intellij.modules.androidstudio|Android Studio|
|com.intellij.modules.appcode|AppCode|
|com.intellij.modules.cidr.lang|AppCode, CLion|
|com.intellij.modules.cidr.debugger|AppCode, CLion, RubyMotion|
|com.intellij.modules.clion|CLion|
|com.intellij.modules.database|IntelliJ IDEA Ultimate Edition, PhpStorm, RubyMine, PyCharm, DataGrip|
|com.intellij.modules.python	|PyCharm|
|com.intellij.modules.ruby	|RubyMine|

PhpStorm没有任何特有的模块依赖，但任何可以指定模块依赖`com.jetbrains.php`，来兼容PhpStorm。
你可以配置可选的模块依赖。比如插件提供了一些Java特有的功能特性，但又想兼容所有的产品，可以使用如下配置方法：

```xml
<depends optional="true" config-file="my-java-features.xml">
  com.intellij.modules.java
</depends>
```
为了使插件可以适用于所有的产品，需要确保没有使用任何IDEA特有的API。对于IDEA特有API的检查，可以通过基于RubyMine，PyCharm或者其他产品的SDK，然后使用该SDK来进行编译插件。

IntelliJ插件仓库会自动基于上述规则来检测插件是否兼容其他产品，然后为对应的产品发布插件。

IntelliJ平台的API和IDE自带插件会在不同的IDE版本中有所不同。如果一些会引起插件兼容性问题改变会在IntelliJ平台和插件API文档中的[不兼容调整](/reference_guide/api_changes_list.md) 进行罗列。



