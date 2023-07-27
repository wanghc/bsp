## 医为浏览器2版

#### 为满足业务系统需求，基于CEFSharp110实现的现代浏览器

1. 支持网站配置，及客户端负载
2. 支持跨域调用
3. 支持多用户,多会话
## 使用说明
1. [下载文件](http://bsp.hisui.cn/static/DHCWebBrowser110.zip),解压后修改DHCWebBrowser110.exe.config

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
> [下载X64安装包](http://bsp.hisui.cn/static/MedBrowser_vc_redist_x64.rar)(http://bsp.hisui.cn/static/MedBrowser_vc_redist_x64.rar)
>
> [下载X86安装包](http://bsp.hisui.cn/static/MedBrowser_vc_redist_x86.rar)(http://bsp.hisui.cn/static/MedBrowser_vc_redist_x86.rar)
> 
> [下载MSVBCRT.AIO安装包](http://bsp.hisui.cn/static/MedBrowser_MSVBCRT.AIO.2019.10.19.X86X64.rar)(http://bsp.hisui.cn/static/MedBrowser_MSVBCRT.AIO.2019.10.19.X86X64.rar)
## 更新说明
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
