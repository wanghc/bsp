## 医为浏览器

#### 为满足业务系统需求，基于CEFSharp49实现的现代浏览器

1. 支持NPAPI插件，Flash，PDF
2. 支持多用户登录
3. 支持网站配置
## 使用说明
1. [下载文件](https://hisui.cn/tool/gen/mediwaybrowser/download),解压后修改DHCWebBrowser49.exe.config

   ```xml
   <!--<add key="webServerIP" value="xx.xx.xx.xx" />
   <add key="appPath" value="/imedical/web/form.htm" />
   <add key="port" value="80" />
   <add key="https" value="0" />-->
   <!-- 2021-04-03增加homePath -->
   <add key="homePath" value="https://127.0.0.1:80/imedical/web/form.html"/>
   <add key="cache" value="true" />
   <add key="autoDeleteCache" value="true" />    
   <add key="log" value="false" />
   <add key="appVersion" value="iMedical8.4" />
   <add key="systemflash" value="false" />
   <add key="ClientSettingsProvider.ServiceUri" value="" />
   <add key="webgl" value="false"/>    <!-- 20210122增加 -->
   ```

2. 双击DHCWebBrowser49.exe进入对应界面

## 更新说明

### 2021-04-03(1.0.11)

+ 增加homePath配置,方便配置首页

### 2021-03-24(1.0.10)
+ 弹出菜单最大化后，点击页签时，界面还原大小问题 :sparkles:

### 2021-03-18(1.0.9)
+ 切换到framework4.0（宝安人民） :sparkles:
+ 版本取3位，忽略第二位 :sparkles:
+ 删除缓存时报错加保护

### 2021-03-15(1.0.0.8)
+ 新窗口自动顶层显示 :sparkles:
+ 解决弹出菜单重复打开时不能置顶问题

### 2021-01-22(1.0.0.7)

* 支持webgl配置支持开启`true`或关闭`false`:sparkles:

### 2021-01-14(1.0.0.6)

+ 删除缓存文件报错保护 :bug:
+ 关闭窗口同时卸载浏览器时，判断浏览器是否初始化完成 :bug:

### 2021-01-11(1.0.0.5)

+ 支持首页路径参数配置:sparkles:  [示例](https://hisui.cn/bsp/mwbrowser/arg5.png)

### 2021-01-11 (1.0.0.4)

+ 浏览器版本从程序集信息中读取
+ CefSharp组件最大内存限制设置为16GB

### 2020-12-01（1.0.3）

+ 实现按F5快捷键时刷新大框架  :sparkles:

### 2020-11-21(1.0.3)

+ 实现头菜单大刷新（九江人民） :sparkles:

### 2020-11-18 (1.0.2) ###

+ 解决登录光标问题导到浏览器有时崩溃问题（淮南朝阳）:bug:


### 2020-10-19（1.0.1） ###

+ 文档加载完成后聚焦文档界面。解决第一次进入界面时光标不定位问题。

### 2020-10-10（1.0） ###

+ 支持自动保存当前窗口缩放比例到配置文件
+ 显示调试工具菜单
+ iMedical8.4发布，升版本为1.0

### 2020-10-01 （0.5）

+ 支持配置调用本地入口网页

+ 支持配置默认缩放比例

+ 增加右键菜单"粘贴"

+ 隐藏调试工具菜单

### 2020-09-01 （0.4）

+ 右键菜单显示翻译成中文

+ 支持accurad协议加载资源


### 2020-08-01（0.3）

+ 修正autodeletecache=true时，打开第二个浏览器缓存文件被占用的错误

+ 修正版本号在进入系统后显示为0.1的问题


### 2020-07-01 （0.2） ###

+ 修改window.open时参数不正确时，无法弹出窗口的问题

+ 页面beforeunload事件增加处理后，导致页面切换卡死的现象

+ 使用HTTPS协议处理

+ 增加网页缩放比例功能


### 2020-03-21（0.1） ###
+ 使用CEFSharp49搭建医为浏览器环境
+ 开发同一域下多用户同时在线浏览器