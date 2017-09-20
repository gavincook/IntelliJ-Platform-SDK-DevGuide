#配置开发环境
## 准备步骤
* **IntelliJ IDEA**：安装9.0及更新版本
* **IntelliJ IDEA社区版源码**：这一步非必须，但源码能帮助我们更好的调试插件。关于源码检出，请参见：[检出并构建IntelliJ IDEA社区版](/basics/checkout_and_build_community.md)
* **插件开发工具包**需要被启用
* **IntelliJ 平台SDK**配置，具体参见：[配置IntelliJ平台SDK](#configuring-intellij-platform-sdk)

##配置IntelliJ平台SDK
配置插件开发环境：
* 检出IntelliJ IDEA社区版代码（[检出并构建IntelliJ IDEA社区版](/basics/checkout_and_build_community.md)）
* 创建IntelliJ平台SDK
打开File->Project Structure：

![Create IntelliJ Platform SDK](img/create_intellij_idea_sdk.png)


* 选择社区版的安装目录


  ![Set Home Directory](img/set_home_directory.png)
* 选择在[检出并构建IntelliJ IDEA社区版](http://www.jetbrains.org/intellij/sdk/docs/basics/checkout_and_build_community.html)中创建的IDEA JDK为默认的JDK版本


![Set IDEA JDK](img/set_java_sdk.png)

* 在SDK配置界面的源码路径Tab中，点击Add：


   ![Add Sourcepath](img/add_sourcepath.png)
* 选择IntelliJ社区版源码检出路径


![Specify Source Paths](img/community_sources_directory.png)
* 选择菜单File->New->Module，并选中IntelliJ Platform Plugin模块类型


![IntelliJ Platform Plugin Module](img/intellij_platform_plugin_module.png)
* 输入插件名称
* 选择菜单：File->Project Structure，选中最新创建的模块，并将IntelliJ Platform SDK配置为模块的SDK


![Set Plugin Module SDK](img/set_plugin_module_sdk.png)


