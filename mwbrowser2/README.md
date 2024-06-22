## 医为浏览器2版

#### 为满足业务系统需求，基于CEFSharp109实现的现代浏览器

1. 支持网站配置，及客户端负载
2. 支持跨域调用
3. 支持多用户,多会话
## 使用说明
1. 直接访问此http://bsp.hisui.cn/static/DHCWebBrowser110.zip路径下载,解压后修改DHCWebBrowser110.exe.config

   ```xml
   <!--如果没有负载均衡时，一定要配置-->
   <configSections>
       <section name="pageLinksSection" type="DHCWebBrowser.PageLinksSection,DHCWebBrowser110"/>
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

2. 双击DHCWebBrowser110.exe进入对应界面

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

### 2024-06-22(1.0.20)

- :bug: 在框架内右键刷新iframe时，丢失post数据问题处理 [4679065]

### 2024-06-19（1.0.19）

- :bug: 解决偶尔读卡时丢失字符问题

### 2023-06-11(V2.0.18)

- 同时下载多个文件时，浏览器只提示一次选择框问题，其它文件不知道保存到何处。修改成提示多次 [4642229]

### 2023-05-27(V2.0.17)

- :sparkles: 处理点击`<a href="#">xx</a>`链接带来的界面偏移问题 [1547086]

  ```csharp
  // 因上次修改影响切换科室界面，重新修改MyResourceRequestHandler类中代码
  request.Url = request.Url.Substring(0, request.Url.IndexOf("#"));
  ```  

### 2023-05-21(V2.0.16)

- :sparkles: 处理点击`<a href="#">xx</a>`链接带来的界面偏移问题 [1547086]

  ```c#
  //修改浏览器的OnBeforeBrowse方法
  if (request.Url.EndsWith("#")) {
      return true;
  }
  ```  

### 2024-03-22(V2.0.15)

- 提供`restartWebsysServer`方法，供js端调用，以便重新启动中间件 [4407602]
- 关闭按钮任务加入超时1000毫秒, 防止一直等待假死


### 2024-01-10（V2.0.14）

- :bug: 还原V2.0.10的视频播放功能，不再支持视频播放，此功能导致无法查看PDF文件 [4164644]

### 2024-01-10 (V2.0.13)
- 修改浏览器图标[4187892]

### 2024-01-02 (V2.0.12)
- 不再忽略代理功能

### 2023-11-08 (V2.0.11)

- 远程调试端口(`8080`)与手写板ws服务端口冲突，不再使用远程调试功能 [4027187]

### 2023-10-30 (V2.0.10)

- 增加视频功能
- - `cmake下载源代码，修改c++源代码，build得到ceflib等dll,再替换109版原生的dll`


### 2023-10-12（V2.0.9)

- 增加自定义协议`mwwebbrowser2:`

- - ```js
    open('mwwebbrowser2://https://ip/imedical/web/form.htm?CASTypeCode=xx&Code=xx',"_self")
    ```

### 2023-09-27

### V2.0.8

- :sparkles: [3910704] 退出浏览器时调用`unlockbeforeunload`以便实现提示功能

### 2023-09-08

### V2.0.7

- :bug: 修复点击`<a href="#">click</a>`链接，再右键刷新界面会导致浏览器卡白问题 [3874057]

### 2023-09-07

### V2.0.6

-  :sparkles: 自定义协议打开功能实现 [3866498]

- :sparkles: 查找功能修改成跨线程调用

```csharp
// 不再允许跨线程调用，解决冲突
// Control.CheckForIllegalCrossThreadCalls = false; // 加载时取消跨线程检查
```

### 2023-07-27

#### V2.0.5

- 修改浏览器UserAgent信息，包含MWBrowser与浏览器版本。如：MWBrowser/2.0.5
- 修改浏览器版本信息为2.0.5  需求号：[3742500]
- CefSharp 104版才加入PermissionHandler类，104之前的版本都没有PerminssionHandler类
- CefSharp 104版JsDialogHandler也会出现alert后焦点丢失问题

### 2023-07-21

#### V110.0.4

- :bug: 弹出的窗口上`alert`,`confirm`，会导致焦点跳到主界面，而不是回到弹出窗口上 ，不再重写`alert`,`confirm` 

### 2023-07-09

#### V110.0.3

- 修改浏览器UserAgent

#### V110.0.2
- 为了兼容win7操作系统，内核切换成109.1.110版
- 解决弹出alert后界面失焦导致不能点击输入框问题 [3712527]
- 解决操作系统缩放125%后，进入医为浏览器时不最大化问题

### 2023-05-04
#### V110.0.1
- 实现医为浏览器110版本
- 浏览器内核使用CefSharp110版
- 开发框架使用framework 4.5.2
