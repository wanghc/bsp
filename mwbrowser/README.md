## 医为浏览器

#### 为满足业务系统需求，基于CEFSharp49实现的现代浏览器

1. 支持NPAPI插件，Flash，PDF
2. 支持多用户登录
3. 支持网站配置
## 使用说明
1. [下载文件](https://hisui.cn/tool/gen/mediwaybrowser/download),解压后修改DHCWebBrowser49.exe.config

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
       <add key="loadingTime" value="2000"/> <!--20210903 首页过渡动画时长，单位毫秒-->
   </appSettings>
   ```

2. 双击DHCWebBrowser49.exe进入对应界面

## 常见问题

### 访问HTTPS网站慢
> 解决办法: 
>
> IE - Internet选项 - 连接页签 - 局域网设置 -  去掉【自动检测设置】的勾

### 安装后运行报错

> 解决办法: 
>
> 安装vc_redist_2013.xxx.exe，
>
> 还报错再安装vc_redist_2015_xxx.exe，
>
> 还报错再安装MSVBCRT.AIO.2019.10.19.xxx.exe

## 更新说明

### 2021-10-29（1.0.23）

- 解决22版修改后，润乾不能PDF打印问题

  ```csharp
  //增加blob支持
  String customProtocol = ",http,https,ftp,ftps,mwbrowser,chrome-devtools,chrome,ws,wss,file,blob,".ToLower(); 
  ```

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