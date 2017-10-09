---
title: Plugin Configuration File - plugin.xml
---
下面为一个陈列了所有可以在配置文件使用的元素的示例配置文件。

```xml
<!-- url="" 指定插件主页（会显示在欢迎页和插件配置对话框中） -->
<idea-plugin url="http://www.jetbrains.com/idea">

  <!-- 插件名称 -->
  <name>VssIntegration</name>

  <!-- 插件的唯一标识符。
       插件的不同版本不能有不同的标识符。
       如果未指定标识符，则使用名称为标识符值。 -->
  <id>VssIntegration</id>

  <!-- 插件描述 -->
  <description>Vss integration plugin</description>

  <!-- 插件最新版本的变更描述。
       会显示到插件的配置对话框和web的插件界面。 -->
  <change-notes>Initial release of the plugin.</change-notes>

  <!-- 插件版本 -->
  <version>1.0</version>

  <!-- 插件厂商。
       通过url属性指定厂商的主页，可选。
       通过email属性指定厂商的邮箱，可选。
       通过logo指定插件的图标路径，需要为16*16尺寸。该logo会显示到欢迎界面。 -->
  <vendor url="http://www.jetbrains.com" email="support@jetbrains.com" logo="icons/plugin.png">Foo Inc.</vendor>

  <!-- 依赖的插件的标识符 -->
  <depends>MyFirstPlugin</depends>

  <!-- 非必须的插件依赖。
       如果插件"MySecondPlugin"已经安装，则mysecondplugin.xml（符合plugin.xml格式的配置）就会被加载。 -->
  <depends optional="true" config-file="mysecondplugin.xml">MySecondPlugin</depends>

  <!-- 允许插件将帮助文档（JavaHelp格式）集成到IDEA的帮助系统中。“file”属性指定插件目录中的help子目录下的jar名称。“path”属性指定jar包内的helpset名称。-->
  <helpset file="myhelp.jar" path="/Help.hs" />

  <!-- 可兼容的IDEA最大最小版本 -->
  <idea-version since-build="3000" until-build="3999"/>

  <!-- 需要加载的资源文件，可用于插件描述、交互名称等。-->
  <resource-bundle>messages.MyPluginBundle</resource-bundle>

  <!-- 应用级别组件 -->
  <application-components>
    <component>
      <!-- 组件接口 -->
      <interface-class>com.foo.Component1Interface</interface-class>

      <!-- 组件实现类-->
      <implementation-class>com.foo.impl.Component1Impl</implementation-class>
    </component>
  </application-components>

  <!-- 项目级别组件 -->
  <project-components>
    <component>
      <!-- 未指定接口，则组件和接口的实现类相同 -->
      <interface-class>com.foo.Component2</interface-class>

      <!-- 如果“workspace”属性设为“true”，则组件将状态存储到.iws文件中，而不是.ipr中。注意：只有组件基于JDOMExternalizable接口，<option>配置才生效。否则<option>配置无效。-->
      <option name="workspace" value="true" />

      <!-- "loadForDefaultProject"标签表示项目级别组件会为默认项目加载。 -->
      <loadForDefaultProject>
    </component>
  </project-components>

  <!-- 模块级别组件 -->
  <module-components>
    <component>
      <interface-class>com.foo.Component3</interface-class>
    </component>
  </module-components>

  <!-- 交互 -->
  <actions>
    <action id="VssIntegration.GarbageCollection" class="com.foo.impl.CollectGarbage" text="Collect _Garbage" description="Run garbage collector">
      <keyboard-shortcut first-keystroke="control alt G" second-keystroke="C" keymap="$default"/>
    </action>
  </actions>

  <!-- 扩展点。其他插件可以通过扩展点访问当前插件资源。“beanClass”属性指定可以使用该扩展点的类。
       Extension points are registered by a plugin so that other
       plugins can provide this plugin with certain data. The
       "beanClass" attribute specifies the class the implementations
       of which can be used for the extension point. -->
  <extensionPoints>
    <extensionPoint name="testExtensionPoint" beanClass="com.foo.impl.MyExtensionBean"/>
  </extensionPoints>

  <!-- 扩展可以访问IDEA或者其他插件提供的扩展点。“defaultExtensionNs”属性设置为定义扩展点的插件ID，若使用IDEA核心扩展点，设置为“com.intellij”。“<extensions>”标签类的元素必须为扩展点的名称，“implementation”为访问扩展点的类名称。 -->
  <extensions xmlns="VssIntegration">
    <testExtensionPoint implementation="com.foo.impl.MyExtensionImpl"/>
  </extensions>
</idea-plugin>
```


