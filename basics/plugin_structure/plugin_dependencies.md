---
title: Plugin Dependencies
---

某些场景下，插件需要依赖其他插件、三方依赖或自定义依赖。为了引入这些依赖，需要如下两步：

* 将需要依赖的jar包，放到*IntelliJ平台SDK*的classpath中。

    > **警告**不要将依赖的插件jar添加到lib中：这样会导致IntelliJ平台加载两次插件依赖，而出现运行异常。
    
* 在plugin.xml中添加`<depends>`标签，在内容中添加需要依赖的插件ID。比如：
```xml
<depends>org.jetbrains.idea.maven</depends>
```

可通过插件jar包中的`META-INF/plugin.xml`文件中的`<id>`标签，来获取插件标识。


