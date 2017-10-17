# 介绍

sweetest 是一款 Web 自动化测试框架，或者说是解决方案，名字取 Selenium, Web UI, Excel, Element, Test 含义。
特点:
1. 简单快速，轻松上手
2. 无需编码能力
3. 在 Excel 中以文本编写测试用例
3. 维护成本低
4. 支持千、万级别的用例规模
5. 拥抱变化，支持敏捷


## 背景

目前，Web 自动化测试基本上是以 Selenium 为接口来编写测试代码，但效果往往不是很好，普遍遇到如下问题：
1. 用例设计人员的编码能力很弱，测试代码编写和维护成本高，效果差；
2. 测试代码量大，测试意图不直观，无法支撑千、万级别的用例规模；
3. Web 页面元素的定位非常繁琐，且页面结构经常变动，导致用例失效。

我们知道，传统的测试用例一般是在 Excel 中用文本编写的，如果自动化测试用例也这么写，是不是就可以解决问题1和2？
对于问题3，我想是时候对开发提出一些要求了，同时我们的元素定位也要优化，让页面自由的去变化，而我们的定位只做最小适用。

## 实现思路

1. Selenium 为底层接口；
2. 在 Excel 中用文本编写测试用例；
3. 元素定位表格化，且优先使用“板块通用定位法”；
4. 要求开发提供必要的、统一的元素属性；
5. 框架负责解析测试用例，执行用例，记录日志，输出测试结果。

## 方案

1. 开发语言：Python
2. 底层接口：Selenium
3. 用例工具：Excel

测试用例如下图：
![testcase](https://github.com/tonglei100/sweetest/blob/master/snapshot/testcase.png?raw=true)


# 安装

## 环境要求

- 系统要求：Windows
- Python 版本：3.6+
- Selenium
- 浏览器：Chrome
- Chrome 驱动: chromedriver

## 安装 sweetest
`pip install sweetest`

## 快速体验
打开 cmd 命令窗口，切换到某个目录，如：D:\Autotest
```
sweetest
cd sweetest_sample
python start.py
```

![install](https://github.com/tonglei100/sweetest/blob/master/snapshot/install.png?raw=true)

OK，如果一切顺利的话，sweetest 已经跑起来了

> QQ交流群：158755338 (验证码：python)


# 目录结构

![dir](https://github.com/tonglei100/sweetest/blob/master/snapshot/dir.png?raw=true)

目录     | 说明
--------------------|-----------------------------------------------------
element\            | 页面元素表目录
\Baidu-Elements.xlsx| 页面元素表，名称格式：project_name + "-Elements.xlsx"
junit\              | junit格式测试结果目录
log\                | 自动化测试运行日志目录
report\             | Excel 格式测试结果目录
snapshot\           | 错误截图目录
testcase\           | 测试用例目录
\Baidu-TestCase.xlsx| 测试用例，名称格式：project_name + "-TestCase.xlsx"
start.py            | 启动脚本，`test = Autotest(project_name, sheet_name)`

备注：以上3处的 project_name 必须一致


# 页面元素表

页面元素表的作用主要是把元素定位独立出来，一是方便维护定位信息，二是测试用例中用元素名称书写，可读性更高。

![elements](https://github.com/tonglei100/sweetest/blob/master/snapshot/elements.png?raw=true)

目录     | 注释
---------|-----------------------------------------------------
page     | element 所在的页面，在所有页面都可用的 element 放在“通用”下面，如 title
name     | element 名称，在不同的 page 下面可以同名
by       | Selenium 定义方式
value    | Selenium 定义的值
备注     | 注释作用

## 元素定位

1. id, link_text, partial_link_text, xpath, class_name
如：百度搜索页面 | 搜索框 | id | kw
则自动化运行时会以 `find_element_by_id('kw')` 来定位

2. 带变量的定位方式
如示例中：

page      |  name      | by    | value
----------|------------|-------|--------------------
百度搜索页面 | 搜索结果# | xpath | //*[@id="#"]/h3/a

写用例时，需要在 搜索结果# 后面带上变量，如： 搜索结果#1
则自动化运行时会以 `find_element_by_xpath('//*[@id="1"]/h3/a')` 来定位
已定义好的常用变量定位方式：
    - id#
    - link#
    - *link#
    - xpath#
    - class#
    - name#
    - url#
    当然，如果#后面的变量不够直观的话，不建议太多使用这几个变量方式。

3. 页面标题
页面的 title

4. 页面URL
页面的url

一般来讲，导航栏在所有页面都存在，应该把导航栏放在“通用”下面，做成变量定位方式，如示例中的:

page | name           | by    | value
-----|----------------|-------|--------------------
通用 | 搜索页导航栏#	| xpath | //*[@class="s_tab"]//a[text()="#"]

# 测试用例

![testcase](https://github.com/tonglei100/sweetest/blob/master/snapshot/testcase.png?raw=true)

## 二级菜单配置

操作、页面、元素这些列其实都是选项列表，利用 Excel 的下拉菜单功能，能够让书写用例更方便，且不易笔误。
下图是测试用例文件中的 ElementsMap 页面：
![maps](https://github.com/tonglei100/sweetest/blob/master/snapshot/maps.png?raw=true)

我们可以通过 Excel 的菜单栏“公式-名称管理器”来配置这些菜单，同时在用例页面通过“数据-数据有效性”配置好“操作”的下拉菜单，以及“页面-元素”的二级下拉菜单。


## 用例字段

必填字段：
- 用例编号
- 步骤编号

### 前置条件：
    - BASE： 整个测试套件的基础，必须才会执行下一步，一般为第一个测试用例
    - SETUP：每个测试用例执行前需要执行的用例，如：返回首页
    - MAIN：一组用例的第一用例，和 SUB 一起使用
    - SUB：和 MAIN 一起使用，当前一个用例执行结果为通过时，才执行；且此用例不会执行 SETUP用例
    - SNIPPET：用例片段，需要做其他用例中使用“执行”关键字调用，配合“执行”关键字的变量赋值功能，可以实现用例复用。

### 操作(关键字)

- 打开
-
即 `get` 方法，打开一个链接

- 检查

取页面元素的值、属性和预期结果对比。测试数据列中如果没有写“k=v”的方式，则默认是取元素的 text。
    - 检查 text：

    操作  | 页面        | 元素    | 测试数据
    -----|------------ |---------|----------
    检查 | 百度搜索页面 | 搜索按钮 | 百度一下

    等价于

    操作  | 页面        | 元素    | 测试数据
    -----|------------ |---------|----------
    检查 | 百度搜索页面 | 搜索按钮 | text=百度一下

    - 检查属性：

    操作  | 页面        | 元素    | 测试数据
    -----|------------ |---------|----------
    检查 | 百度搜索页面 | 搜索框   | name=wd

    - “页面标题”和“页面URL”直接在测试数据列中写预期结果即可。

    操作  | 页面        | 元素    | 测试数据
    -----|------------ |---------|----------
    检查 | 百度搜索页面 | 页面标题 | 百度一下\，你就知道

    注意：测试数据列中，由于逗号(，or ,)是多个“k=v”的分隔符，所以如果 v 中有逗号，要用反斜杠(\\)转义；但等号(=)无需转义。

- 输入

在输入看中输入文本

- 点击

点击按钮或则链接等一切可点击操作的元素。此关键字支持在同一个页面上顺序点击多个元素，在元素列以竖杠(|)分割即可，如：

操作  | 页面        | 元素
-----|------------ |--------------------
点击 | 百度搜索页面 | 搜索按钮\|搜索结果#1

- 移动到
有些页面元素，当鼠标移动到上面时，会弹出下拉菜单等。才操作同关键字“点击”类似。

- 执行

即执行测试用例片段，支持在测试数据列给变量赋值，如我们有用例片段 SNIPPET_001，则测试用例 BAIDU_002 中，步骤1如下：

操作  | 页面        | 元素        | 测试数据
-----|------------ |-------------|----------
执行 | 用例片段	  | SNIPPET_001	|keywords=搜狗

把变量 keyswords 赋值为 搜索，此步骤会执行用例片段 SNIPPET_001，其搜索的值为“搜狗”。

- #检查

把检查结果反向，此关键字暂时未完整实现。

### 输出数据：

在运行时，把元素的值或属性赋值给变量，此变量可以在之后的步骤中使用"<>"引用变量名。

操作  | 页面       | 元素        | 测试数据  | 输出数据
-----|------------|-------------|----------|---------
点击 | 百度搜索页面 | 搜索结果#1  |          | title=text
检查 | 通用        | 页面标题    | \<title>  |


# 测试执行

`python start.py`

# 测试报告

见 report 目录
