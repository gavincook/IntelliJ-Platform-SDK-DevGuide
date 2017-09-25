---
title: Plugin Content
---
有两种形式来组织插件内容：

1. 插件目录中单独的`.jar`文件：
    
    该文件存档应该包含配置文件（`META-INF/plugin.xml`）和实现相关功能的class文件。配置文件将指定插件名字、描述、版本、厂商、支持的IDEA版本、插件组件、交互、交互组以及用户界面。

   ```
   .IntelliJIDEAx0/
     plugins/
       sample.jar/
         com/foo/...
         ...
         ...
         META-INF/
           plugin.xml
   ```
   

2. 打包为`.jar`，并存放在lib目录下：

   ```
   .IntelliJIDEAx0/
     plugins/
       Sample/
         lib/
           libfoo.jar
           libbar.jar
           Sample.jar/
             com/foo/...
             ...
             ...
             META-INF/
               plugin.xml
   ```
   所有`lib`文件夹下的jar包都会被自动添加到classpath中。
  


