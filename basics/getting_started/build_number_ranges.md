#构建版本区间
基于构建版本区间部分的文档，你可以为插件设置兼容的起始版本号和结束版本号。

从IntelliJ IDEA9 beta版开始，使用了复杂的版本号，比如IU-162.94。
版本号包含了如下几部分：
* 产品ID
    - IDEA社区版：IC
    - IDEA商业版：IU
    - RubyMine：RM
    - PyCHarm：PY
* 分支编号（162）
* 分支中的构建编号（94）
IntelliJ平台从2016.2版本开始，分支编码基于YYYY.R格式。分支编码使用年的最后两位和季度组成。比如：162代表版本2016.2，163代表版本2016.3，171代表2017.1。在版本号IU-162.94就对应2016.2版本。
从2016.2版本开始，构建版本号也可能包含多种组成部分：
IU-162.94, IU-162.94.11, IU-162.94.11.256.42。这使得对三方插件和IDE开发者更灵活的限定版本。插件就可以更精确地指定其兼容的版本；IDE厂商可以指定其基于特定版本的IntelliJ平台的版本号，同时也可以指定自身额外的内部版本号，使得这些IDE的插件开发者可以更精确指定兼容的版本区间。
复杂的构建版本号也可以用于指定插件兼容的IDEA的起始版本号和结束版本号。一般来说，会省去产品ID，直接使用分支编号和构建编号，比如：
```xml
<idea-version since-build="94.539"/>
<idea-version since-build="162.539.11"/>
<idea-version until-build="162"/> <!-- 所有早于162的版本，不包括162版本-->
<idea-version since-build="162" until-build="162.*"/> <!-- 所有基于162的版本-->
```
如下的分支编号用于基于IntelliJ平台的产品的最近几个版本：

|分支编号|IntelliJ平台版本|
|---|----|
|171|2017.1|
|163|2016.3|
|162|2016.2|
注意：没有170版本号.YYYY.R模式下的版本号，R从1开始。

## IntelliJ平台早于2016.2版本的分支和构建编号

|分支编号|产品版本|
|---|----|
|[145](https://github.com/JetBrains/intellij-community/tree/145) |IntelliJ IDEA 2016.1, AppCode 2016.1, CLion 2016.1, DataGrip 2016.1, PhpStorm 2016.1, PyCharm 2016.1, RubyMine 2016.1, WebStorm 2016.1|
|[143](https://github.com/JetBrains/intellij-community/tree/143) 	|IntelliJ IDEA 15, AppCode 3.3, CLion 1.2, DataGrip 1.0, PhpStorm 10, PyCharm 5, RubyMine 8, WebStorm 11|
|[141](https://github.com/JetBrains/intellij-community/tree/141)	|IntelliJ IDEA 14.1, Android Studio 1.3, PyCharm 4.1, WebStorm 10|
|[139](https://github.com/JetBrains/intellij-community/tree/139) 	|IntelliJ IDEA 14, PhpStorm 8, PyCharm 4, RubyMine 7, WebStorm 9|
|[135](https://github.com/JetBrains/intellij-community/tree/135)	|IntelliJ IDEA 13.1, WebStorm 8|
|[133](https://github.com/JetBrains/intellij-community/tree/133)	|IntelliJ IDEA 13, PhpStorm 7, PyCharm 3.1, WebStorm 7|
|[131](https://github.com/JetBrains/intellij-community/tree/131)	|PhpStorm 7, PyCharm 3.0, WebStorm 7|
|[129](https://github.com/JetBrains/intellij-community/tree/129)	|IntelliJ IDEA 12.1, bug-fix updates for PyCharm 2.7 and PhpStorm/WebStorm 6.0|
|[127](https://github.com/JetBrains/intellij-community/tree/127)	|AppCode 2.0, PhpStorm/WebStorm 6.0|
|[125](https://github.com/JetBrains/intellij-community/tree/125)	|PyCharm 2.7, RubyMine 5.0|
|[123](https://github.com/JetBrains/intellij-community/tree/123)	|IntelliJ IDEA 12|
|[121](https://github.com/JetBrains/intellij-community/tree/121)	|AppCode 1.6, PyCharm 2.6, PhpStorm/WebStorm 5.0|
|[119](https://github.com/JetBrains/intellij-community/tree/119)	|RubyMine 4.5|
|[117](https://github.com/JetBrains/intellij-community/tree/117)	|IntelliJ IDEA 11.1, AppCode 1.5, PyCharm 2.5, RubyMine 4.0.x, PhpStorm/WebStorm 4.0|
|111	|IntelliJ IDEA 11.0|
|107	|IDEA 10.5|
|103	|IDEA 10.0.2+|
|99	|IDEA 10.0+|
|95	|IDEA 9.0.2+|
|93	|IDEA 9.0|

IDEA构建编号：

|IDEA版本|构建编号|
|---|----|
|12.0	|123.72|
|11.1.3|	117.798|
|11.1.2	|117.418|
|11.1.1	|117.117|
|11.1	|117.105|
|11.0.2|	111.277|
|11.0.1|	111.167|
|11.0	|111.69|
|10.5.2|	107.587|
|10.5.1|	107.322|
|10.5|	107.105|
|10.0.3|	103.255|
|10.0.2|	103.72|
|10.0.1|	99.32|
|10.0|	99.18|
|9.0.4|	95.627|
|9.0.3|	95.429|
|9.0.2|	95.66|
|9.0.1|	93.94|
|9.0|	93.13|

##早于9.0的IDEA构建版本
在9.0版本之前，版本号使用数字线性增长的方式，如下：

|IDEA版本|构建编号区间|
|---|----|
|8.1.x	|9500-9999|
|8.0.x	|9100-9499|
|8.0	|8000-9099|
|7.0.2+	|7500-7999|
|7.0 final	|7200-7499|
|7.0 pre-M2	|6900-7199|
|7.0 pre-M1	|6500-6899|
|6.0.2 branch	|6000-6499|
|6.0 branch	|5000-5999|
|5.1 branch	|4000-4999|

每个发布版本对应的构建编号如下：

|IDEA版本|构建编号|
|---|----|
|8.1|	9732|
|8.0.1|	9164|
|8.0|	9013|
|8.0M1|	8664|
|7.0.5|	7971|
|7.0.3|	7757|
|7.0.2|	7590|
|7.0 final|	7361|
|7.0 M2|	7126|
|7.0 M1|	6813|
|6.0.6|	6197|
|6.0.5|	6180|
|6.0.1|	5784|
|5.1.2|	4267|


