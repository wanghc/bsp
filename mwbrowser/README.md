## 医为浏览器

#### 为满足业务系统需求，基于CEFSharp49实现的现代浏览器

1. 支持NPAPI插件，Flash，PDF
2. 支持多用户登录
3. 支持网站配置
3. 支持跨域调用
## 使用说明
1. [下载文件](http://bsp.hisui.cn/static/DHCWebBrowser.zip),解压后修改DHCWebBrowser49.exe.config

   ```xml
   <!--如果没有负载均衡时，一定要配置-->
   <configSections>
       <section name="pageLinksSection" type="DHCWebBrowser.PageLinksSection,DHCWebBrowser49"/>
   </configSections>
   <pageLinksSection>
       <pageLinks>
           <!-- 这行是注释说明：以下地址会显示到右键的【打开其它服务】菜单中 -->
           <!-- 如果没有负载均衡服务时，一定要把【所有HIS-ECP服务路径】配置到这,以便浏览器实现负载均衡，且homePathIsLoadBalance配置成false -->
           <add name="服务器1" url="http://x.x.x.x:1443/imedical/web/form.htm"/>
           <add name="服务器2" url="http://x.x.x.x:1443/imedical/web/form.htm"/>
           <add name="服务器3" url="http://x.x.x.x:1443/imedical/web/form.htm"/>
       </pageLinks> 
   </pageLinksSection>
   <!--服务器配置结束 -->
   <appSettings>
       <!-- 首页路径 -->
       <add key="homePath" value="https://127.0.0.1:80/imedical/web/form.html"/>
       <!--homePath是否为负载均衡服务的首页路径. false时浏览器自身实现负载-->
       <add key="homePathIsLoadBalance" value="true"/>  
       <add key="cache" value="true" />
       <add key="autoDeleteCache" value="true" /> 
       <add key="log" value="false" />
       <add key="systemflash" value="false" />
       <add key="ClientSettingsProvider.ServiceUri" value="" />
       <add key="webgl" value="false"/>    <!-- 20210122增加 -->
       <add key="ValidOpenWindowName" value="false" /> <!--20210824 校验打开目标iframe窗口 重庆人民-->
       <add key="loadingTime" value="0"/> <!--20210903 首页过渡动画时长，单位毫秒-->
       <add key="titleShowBrowserInfo" value="true"/> <!-- 20220727 是否显示医为浏览器信息 -->
   </appSettings>
   ```

2. 双击DHCWebBrowser49.exe进入对应界面

## 常见问题

### 访问HTTPS网站慢
> 解决办法: 
>
> IE - Internet选项 - 连接页签 - 局域网设置 -  去掉【自动检测设置】的勾

### 安装后运行报错或无响应解决办法

> 解决办法: 
>
> 1. 安装vc_redist_2013.xxx.exe
>2. 还报错再安装vc_redist_2015_xxx.exe
> 3. 还报错再安装MSVBCRT.AIO.2019.10.19.xxx.exe
>
> 操作系统为64位时[下载X64安装包](http://bsp.hisui.cn/static/MedBrowser_vc_redist_x64.rar)(http://bsp.hisui.cn/static/MedBrowser_vc_redist_x64.rar)
>
> 操作系统为32位时[下载X86安装包](http://bsp.hisui.cn/static/MedBrowser_vc_redist_x86.rar)(http://bsp.hisui.cn/static/MedBrowser_vc_redist_x86.rar)
> 
> [下载MSVBCRT.AIO安装包](http://bsp.hisui.cn/static/MedBrowser_MSVBCRT.AIO.2019.10.19.X86X64.rar)(http://bsp.hisui.cn/static/MedBrowser_MSVBCRT.AIO.2019.10.19.X86X64.rar)
## 更新说明

### 2023-10-12 （1.0.40）

- 增加自定义协议`mwwebbrowser1:`

- - ```js
    open('mwwebbrowser1://https://ip/imedical/web/form.htm?CASTypeCode=xx&Code=xx',"_self")
    ```


### 2023-05-08（1.0.39）

- 第一次进入浏览器时，增加加载进度条

- 右键菜单增加`查找`功能

- 启动医为浏览器时，检查中间件是否运行，未运行时，会自动拉启中间件。

- 浏览器图标更改  

## 2022-09-29(1.0.36)

- 增加XML配置来决定是否写环境变量

  ```xml
  <add key="writeEnvironment" value="true"/>
  ```

  

## 2022-08-29(1.0.35)

- 解决当弹出式界面出现服务故障时导致整个系统跳转到错误提示界面问题处理。输血菜单。
- 错误提示界面只提示服务连接问题。2902366
- 试着解决ERR_CACHE_READ_FAILURE问题，增加enable-simple-cache-backend配置

## 2022-07-27(1.0.34)

- 增加浏览器标题是否显示医为浏览器信息配置  :sparkles: 需求号：2823327

## 2022-07-26(1.0.33)

- 有时下载blob文件时，导致弹出窗口关闭问题 :bug: 需求号：2719129
- 增加js方法switchToLanguageMode(imecode)切换输入法,支持`'zh-CN'`与`'en-US'` :sparkles:  需求号：2805324

## 2022-07-13 (1.0.32)

- 有些电脑修改环境变量会慢，导致进入首页慢（10-20秒）。修改注册代码修改环境变量 :bug:。需求号：2791408

## 2022-07-05 （1.0.31）

- 启动医为浏览器时, 写入`DHCWebBrowser_HOME`环境变量，同时程序目录写入Path环境变量中

  ```cmd
  # 使用以下命令即可 使用医为浏览器进入应用系统
  cmd> DHCWebBrowser49.exe "https://ip:port/imedical/web/form.htm?a=1&b=2"      
  ```

- 

## 2022-06-29 (1.0.30)
- 支持data协议，支持用纯JS代码生成并保存Excel功能 :bug: 需求号：2432665

## 2022-06-17 (1.0.29)

- 进入系统后，点叉号关闭医为浏览器时，报错处理（某些电脑）:bug: 需求号：2727030

## 2022-06-09（1.0.28）

- 对于路径报EmptyResponse时，忽略
- `window.open`功能优化，对标chrome浏览器。考虑操作系统的放大应用功能。需求号：2443523
- 右键菜单增加保存图片菜单（但不有保存canvas）。需求号：2527310
- 右键刷新功能优化，右键在哪个iframe上刷新哪个iframe。需求号：2708224

## 2022-04-18（1.0.27）

- 复制他人的【医为浏览器的快捷方式】到本地后，访问床位图时，导致字体变小问题处理 :bug:
- - ```settings.LocalesDirPath = System.Windows.Forms.Application.StartupPath+"\\locales";```

### 2021-03-24（1.0.26）

- 打开三方跨域界面后，在弹出界面缩放功能无效问题处理
- - Portal 弹出 HIS界面后，在HIS界面不能使用浏览器缩放功能(重庆中医)  :bug:
- 按F12打开焦点所在窗口的调试器。~~以前一直是主窗口~~ :bug:

### 2021-12-13（1.0.25）

- 有些界面进入时不能定位光标到输入框中问题 (2212616) :sparkles:

### 2021-11-18（1.0.25）

- 浏览器假死处理（天津中心一） :sparkles:

  ```xml
  <!-- 默认不用配置此行 -->
  <!-- 配置成3,AutoReload表示3分钟网页无响应自动重启浏览器。自助机，无人值守环境 -->
  <!-- 配置成10,Alert表示10分钟网页响应时，给出继续等待与否提示交互-->
  <add key="noResponseTimeOut" value="3,AutoReload"/>
  ```

### 2021-11-08（1.0.24）

- 右键增加打印功能（需求号：2223722）:sparkles:

### 2021-10-29（1.0.23）

- 解决22版修改后，润乾不能PDF打印问题

  ```csharp
  //增加blob支持
  String customProtocol = ",http,https,ftp,ftps,mwbrowser,chrome-devtools,chrome,ws,wss,file,blob,".ToLower(); 
  ```
- 解决ValidOpenWindowName为true时，经常闪退问题（重庆中医）

### 2021-10-15(1.0.22)

- 浏览器标题显示成`title`内容

### 2021-10-10(1.0.21)

- 路径状态为Aborted时不提示错误界面

### 2021-10-09(1.0.21)

- 增加首页不可访问等错误界面
- 自定义协议生效

### 2021-09-30 (1.0.21)

- 增加是否显示右键菜单配置

  ```xml
  <!--显示右键功能,不配置节点时为显示。false时不显示右键菜单，用于自助机-->
  <add key="showRightMenu" value="true"/>
  ```

  

### 2021-09-19（1.0.20）

- 增加F11全屏功能
- 增加js方法，`cefbound.toggleFullScreen()`切换全屏功能

- 打开浏览器时全屏配置

  ```xml
  <!--true全屏打开,false非全屏打开-->
  <add key="openFullScreen" value="true"/>
  ```

### 2021-09-10(1.0.19)

- 直接修改exe名称导致程序报错问题处理 :sparkles:

  

### 2021-09-06(1.0.19)

- 增加动画过渡后，首页光标不自动到用户框中问题 :sparkles:

  ```csharp
  chromeBrowser.Focus();
  ```

### 2021-09-02(1.0.19)

- 增加第一次打开医为浏览器时的过渡动画 :sparkles:

  ```xml
  <!--首页过渡动画时长，单位毫秒-->
  <add key="loadingTime" value="2000"/>
  ```
- 增加常用网址配置（需求号：2047288）

  ```xml
  <pageLinksSection>
      <pageLinks>
          <!-- 这行是注释说明：以下地址会显示到右键的【打开其它服务】菜单中 -->
          <!-- 如果没有负载均衡服务时，一定要把【所有HIS-ECP服务路径】配置到这,以便浏览器实现负载均衡，且homePathIsLoadBalance配置成false -->
          <add name="服务器1" url="http://x.x.x.x:1443/imedical/web/form.htm"/>
          <add name="服务器2" url="http://x.x.x.x:1443/imedical/web/form.htm"/>
          <add name="服务器3" url="http://x.x.x.x:1443/imedical/web/form.htm"/>
      </pageLinks>
  </pageLinksSection>
  ```

- 增加负载首页功能。在没有负载的项目上，自动更新医为浏览器会固定一个首页，会把请求集中到一台服务器上，实现负载。

  ```xml
  <!--homePath是否为负载均衡服务的首页路径,false时浏览器自身实现负载-->
  <add key="homePathIsLoadBalance" value="false"/>  
  ```

  ```vb
  if(Int16.TryParse(arr[3],out Int16 result)){
  	var index = result % pageLinks.Count;
  	List<String> list = new List<string>( pageLinks.Keys);
      return pageLinks[list[index]];
  }
  ```
### 2021-08-27(1.0.18)

- alert，confirm样式/文字保持与IE一致。需求号:2086409
- 浏览器窗口的标题栏内容格式调整，浏览器名及版本放到最后面。需求号：2121917

### 2021-08-24(1.0.17)

- 有时window.open(url, windowName, [windowFeatures])不能在指定窗口打开url问题修复

- - 重庆人民发现收费安全组下，头菜单使用一段时间后，不能在主框架打开界面，会错误的弹出新界面。 
  > 代码修改如下
  ```csharp
  svar myframe = browser.GetFrame(targetFrameName);
  if (myframe != null)
  {
  	myframe.LoadUrl(targetUrl);
      //...
  }
  ```
### 2021-07-17 (1.0.16)

- window.confirm方法弹出的对话窗口关闭叉按钮不能点击问题

- - 天津第一中心

  > 代码修改如下

  ```csharp
  var dr = MessageBox.Show(messageText, "确认信息", MessageBoxButtons.OKCancel);  // YesNo修改成OKCancel
  if (dr == DialogResult.OK){  // Yes修改成OK
      // ...
  }
  ```
- 缩放功能bug。配置定义缩放值为1.44后, 在登录后某个界面缩放，得到值a，再进任意界面，整个系统缩放又回到1.44。

  > 代码修改如下：

  ```c#
  CurPercent = Convert.ToDouble(ConfigurationManager.AppSettings["defaultZoomPercent"]); // 实时取缩放值
  ```

### 2021-07-13(1.0.15)

- 字体模糊问题处理 :sparkles: 
- - 对比IE与医为浏览器字体清晰度差距很大（龙岩二院）

> 主要修改代码，Windows 10 自 1703 开始引入第二代的多屏 DPI 机制（PerMonitor V2）。win7上使用Per-Monitor DPI（true/pm）感知，true-系统DPI感知级别，否则普通的DPI感知。

```xml
<asmv3:application>
<asmv3:windowsSettings xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">
<dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true/pm</dpiAware>
<dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">permonitorv2,permonitor</dpiAwareness>
</asmv3:windowsSettings>
</asmv3:application>
```

### 2021-06-10(1.0.14)

- 在弹出窗口，使用润乾/帆软/blob下载Excel等文件后弹出窗口自动关闭问题 :bug:
- - 公司标准版，导入团体成员界面-导出错误数据按钮-导出Excel成功后，关闭自动界面问题
- - 冀州市医院/衡水市第六人民医院，润乾/帆软打印后窗口关闭问题
- 浏览器UserAgent修改成49版

### 2021-05-25(1.0.13)

- 1.0.6版本修改后，同一客户端多护士同时使用医为浏览器时，护士执行界面空白问题。撤回1.0.6中【删除缓存文件报错保护】修改
### 2021-05-13(1.0.12)

- 解决`HTTPS`网页调用`HTTP`服务报错问题 :sparkles:

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