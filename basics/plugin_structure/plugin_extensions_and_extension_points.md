---
title: Plugin Extensions and Extension Points
---

*IntelliJ平台*提供了一些列的扩展和扩展点，使得插件可以和其他插件或者IDE本身进行交互。

## 扩展点

如果你想允许其他插件集成当前插件的功能特性，那么在当前插件中，必须声明一个或多个*扩展点*。每个扩展点定义了一个类或一个接口使得可以访问该扩展点。

## 扩展

如果你想使得你的插件继承其他插件的功能特性或者*IntelliJ平台*特性，你需要定义一个或多个 _扩展_

## 如何声明扩展和扩展点

可以通过在`plugin.xml`中，使用`<extensions>`和`<extensionPoints>`来分别定义扩展和扩展点。

**声明扩展点**

在`<extensionPoints>`部分，使用子元素`<extensionPoints>`来定义扩展点，使用`name`，`beanClass`，`interface`分别指定扩展点的名字，扩展点的类、扩展点的接口。

下面为一个定义扩展点的示例：

```xml
<extensionPoints>
  <extensionPoint name="MyExtensionPoint1" beanClass="MyPlugin.MyBeanClass1">
  <extensionPoint name="MyExtensionPoint2" interface="MyPlugin.MyInterface">
</extensionPoints>
```

* `interface`属性指定扩展点必须实现的接口。
* `beanClass`属性指定的类，其有一个或多个属性会进行注解[@Attribute](upsource:///xml/dom-openapi/src/com/intellij/util/xml/Attribute.java)标识。

使用了扩展点的插件会解析`plugin.xml`中的上述配置。

如下为上述例子中的`MyBeanClass1`：

```java
public class MyBeanClass1 extends AbstractExtensionPointBean {
  @Attribute("key")
  public String key;

  @Attribute("implementationClass")
  public String implementationClass;

  public String getKey() {
    return key;
  }

  public String getClass() {
    return implementationClass;
  }
}
```

定义扩展主要用于访问扩展点`MyExtensionPoint1`，在`plugin.xml`中需要包含`<MyExtensionPoint1>`标签，并设置其`key`和`implementationClass`属性（具体参见下面的示例）。

**定义扩展**

1. 在`<extensions>`标签，设置`xmlns`（过时）或`defaultExtensionNs`属性为下列其中的一个：
    * 如果插件需要继承IntelliJ平台的核心特性，则配置为`com.intellij`。
    * 如果插件需要继承其他插件的特性，则配置为`{插件ID}`。
2. 在`<extensions>`中添加子标签，子标签的名字必须和需要访问的扩展点的名字一致。
3. 访问扩展点时，使用如下方式：
    * 如果扩展点使用`interface`属性进行声明时，设置`implementation`属性为实现该接口的实现类。
    * 如果扩展点使用`beanClass`方式定义，那么对于`beanClass`中所有的带有[@Attribute](upsource:///xml/dom-openapi/src/com/intellij/util/xml/Attribute.java)注解的属性进行设值。

如下示例，在`plugin.xml`中定义了两个扩展，第一个用于访问`appStarter`和`applicationConfigurable`IntelliJ平台的扩展点；第二个用于访问测试插件的`MyExtensionPoint1`扩展。
```xml
<!-- Declare extensions to access extension points in the IntelliJ Platform.
     These extension points have been declared using the "interface" attribute.
 -->
  <extensions defaultExtensionNs="com.intellij">
    <appStarter implementation="MyTestPackage.MyTestExtension1" />
    <applicationConfigurable implementation="MyTestPackage.MyTestExtension2" />
  </extensions>

<!-- Declare extensions to access extension points in a custom plugin
     The MyExtensionPoint1 extension point has been declared using *beanClass* attribute.
-->
  <extensions defaultExtensionNs="MyPluginID">
     <MyExtensionPoint1 key="keyValue" implementationClass="MyTestPackage.MyClassImpl"></MyExtensionPoint1>
  </extensions>
```

## 如何获取插件扩展点列表？

获取*IntelliJ平台*的可用扩展点，参考如下的xml配置文件的`<extensionPoints>`部分：

* [`LangExtensionPoints.xml`](upsource:///platform/platform-resources/src/META-INF/LangExtensionPoints.xml)
* [`PlatformExtensionPoints.xml`](upsource:///platform/platform-resources/src/META-INF/PlatformExtensionPoints.xml)
* [`VcsExtensionPoints.xml`](upsource:///platform/platform-resources/src/META-INF/VcsExtensionPoints.xml)

## 附加信息和示例

对于如何为IDEA创建插件的示例和详细说明，参考自定义IDEA配置对话框和Tool Windows的创建。



