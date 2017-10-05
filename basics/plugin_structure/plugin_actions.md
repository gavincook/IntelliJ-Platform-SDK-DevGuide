---
title: Plugin Actions
---

*IntelliJ平台*提供了交互的概念。交互是一个类，衍生至类[`AnAction`](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)，当菜单按钮或工具栏按钮点击时，会触发其`actionPerformed`方法。

交互系统允许插件添加其自己的条目到IDEA菜单或者工具栏中。交互以组的形式存在，并且可以包含其它组。一组交互可以是工具按钮或是菜单条目。子组交互会以菜单的子菜单形式存在。你可以在[IntelliJ平台交互系统](/basics/action_system.md)查看关于如何创建和注册交互的详细说明。


