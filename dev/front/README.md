## 前端公共方法说明

以HOS功能为基础，整合HISUI的前端最小工程范例

### 在html界面中引入HISUI

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>界面名称</title>
    <!--引用HISUI-->
    <script type="text/javascript" src="../../../base/scripts/hisui.js"></script>
</head>
<body>
 ...
</body>
</html>
```

引入hisui.js后，会同步获取后台HISUI版本、语言、字体大小等配置，引入对应的js，相关样式的css；引入界面定义相关js，来自动获得界面元素定义及表格定义。

如果不希望自动引入界面定义相关文件，可以在引入hisui.js前，把变量$pageOptionsDisable设置为true即可。

```html
<script type="text/javascript" >window.$pageOptionsDisable = true;</script>
<script type="text/javascript" src="../../../base/scripts/hisui.js"></script>
```



## 公共方法

### 1、获得头菜单Window对象
```js
var menuWin = websys_getMenuWin();
```

### 2、获得当前会话信息
```js
var session = websys_getSession();
```

### 3、获得头菜单表单
```js
/// 头菜单表单用于存储全局参数，如:病人id,就诊id,就诊类型
// --- 重设全局信息
websys_resetMenuForm();
// --- 从全局信息中获取值
var frm = websys_getMenuForm();
let admId = frm.EpisodeID.value;     // 得到''
let patientId = frm.PatientID.value; // 得到''
let admType = frm.admType.value; // 得到''
// --- 设置值, 把EpisodeID=2,PatientID=2,admType=I写到全局信息中
websys_setMenuForm({EpisodeID:2,PatientID:1,admType:'I'});
// --- 取值
admId = frm.EpisodeID.value;     // 得到2
patientId = frm.PatientID.value; // 得到1
admType = frm.admType.value;     // 得到I
```
补充说明：

`websys_resetMenuForm(forceGlobalPatient)`、`websys_getMenuForm`(forceGlobalPatient)、`websys_setMenuForm(options,forceGlobalPatient)`方法入参`forceGlobalPatient`表示【是否强制处理全局就诊信息】，如果为true则强制对全局就诊信息处理，否则【包含(switchSysPat=N参数)的最近Window对象上的就诊信息】或全局就诊信息

```js
// 以下三个方法强制处理全局头菜单上就诊信息，不考虑switchSysPat=N参数
websys_resetMenuForm(true);
websys_getMenuForm(true);
websys_setMenuForm({EpisodeID:2,PatientID:1,admType:'I'},true);
```



### 4、弹出HISUI窗口

```js
// 在【顶层】窗口弹出界面
websys_showModal({
    title:'弹出窗口',
	width:700,height:400,
	url:'../base/runqian/html/test.html?open=2',
	myData:{startDate:'2024-12-09'}  /*附加参数对象*/
});
// 在弹出界面test.html中获得附加参数
let myData  = websys_showModal('options').myData; // {startDate:'2024-12-09'}
websys_showModal('close') //$hisui.window组件有的方法都可以在此使用
```
如果需要在某区域iframe内弹出窗口，可以指定targetFrm或targetName。如果这二值都传则会在targetFrm下查找targetName
```js
// 在【指定window】内弹出界面
websys_showModal({
    title:'弹出窗口',
	width:700,height:400,
	targetFrm:window,              // 也可以是iframe名称 targetName:"MyName"
	url:'../../../base/runqian/html/test.html?open=1',
	myData:{startDate:'2024-12-09'}
});
// 在test.html中可以使用以下代码获得附加参数
var myData = websys_showModal('options',{targetFrm:window.parent}).myData; // {startDate:'2024-12-09'}
websys_showModal('close',{targetFrm:window.parent}); // $hisui.window组件有的方法都可以在此使用
```

#### 4.1 公共方法列表

```js
function websys_rewriteUrl(url: String,paramObj: Object) // 为url增加请求参数，返回字符串
// 示例：
websys_rewriteUrl("my.html?id=1",{id:2,name:'whc'}); /* 返回： my.html?id=2&name=whc  */
websys_rewriteUrl("my.html",{id:2,name:'whc'});      /* 返回： my.html?id=2&name=whc */
websys_rewriteUrl("",{id:2,name:'whc'});             /* 返回： ?id=2&name=whc  */
```

```js
function websys_frontPathCombine(urlForBasePath: String); // 相对static/base/目录转换成绝对路径，入参是相对于static/base/的html路径，生成绝对路径，保证在不同vue路由下能找对界面,来解决404问题
// 示例1：
websys_frontPathCombine("../emr/ip/html/my.html"); 
/* 返回：/his/base/../emr/ip/html/my.html,访问的实际是static/emr/ip/html/my.html文件 */

// 示例2：
// 如果以/或//开头不处理，
websys_frontPathCombine("//emr/ip/html/my.html"); /* 返回：//emr/ip/html/my.html */
websys_frontPathCombine("/emr/ip/html/my.html");  /* 返回：/emr/ip/html/my.html */
```

```js
// 获得同域下最顶层window对象。一直向上查找parent，遇到跨域则返回当前window
function websys_getTop();
let myTop = websys_getTop();
```

```js
function websys_urlToParamObj(url: String) // 获取请求路径中的参数对象
// 示例：
websys_urlToParamObj(location.href)  /* 返回当前界面的请求参数对象 */
websys_urlToParamObj("my.html?id=1&name=whc")  /* 返回： {id:1,name:"whc"} */
websys_urlToParamObj("id=1&name=whc")          /* 返回： {id:1,name:"whc"} */
```

```js
function websys_objToParam(obj: Object, originParam: String, containEmpty: boolean|undefined)
// 在originParam内补充obj参数, containEmpty为【false或不传】时会忽略obj中空值对
// 示例：
websys_objToParam({id:1},"id=2&type=",true);         /*返回：'id=2&type='*/
websys_objToParam({id:1,sex:''},"id=2&type=",false); /*返回：'id=2&type='*/
```



创建新窗口`websys_createWindow`方法

```js
function websys_createWindow(url: String, name: String, features: String); // 打开窗口
// 示例：
websys_createWindow('my.html','_blank');
websys_createWindow('my.html','TRAK_hidden','status,scrollbars,resizable,hisui=true,width=80%,height=80%');
```



### 5、请求后台时使用的方法($ipost，$iget)

```js
// 向后台发送post请求，默认requestbody-json方式
// $ipost(allpath,param,successHandler,errorHandler);
$ipost('/cf/sys/savemenu',{code:'his'},function(json){
    // 后台返回的json数据
});
```
```js
// 向后台发送get请求，默认使用formdata方式
// $iget(allpath,param,successHandler,errorHandler)
$iget('/cf/sys/getmenu',{code:'his'},function(json){
    // 后台返回的json数据
})
```
### 6、参数加密及返回值加密

#### 6.1 请求参数加密再请求($iget_enc，$ipost_enc)

前台使用`$ipost_enc`或`$iget_enc`发送请求时，会先把`所有参数`加密，然后再发送到后台，后台java使用`@InterfaceDecrypt`来解密`所有参数`，还原入参

- GET请求全参数加密示例

```js
$iget_enc('/xx/xxx',{password:"123456",username:"医生02",loc:'所有科室'},function(rtn){
	console.log(rtn.data);
});
```

```java
@GetMapping("/xxx")
@InterfaceDecrypt
public BaseResponse<MyVo> xxxGet(String password,String username,String loc){
    // do something
    return BaseResponse.success(myVo);
}
```
- POST请求全参数加密示例
```js
$ipost_enc('/xx/xxx',{password:"123456",username:"医生02",loc:'所有科室'},function(rtn){
	console.log(rtn.data);
});
```

```java
@PostMapping("/xxx")
@InterfaceDecrypt
public BaseResponse<dto> xxxPost(@RequestBody MyDto dto){
    // do something
    return BaseResponse.success(dto);
}
```

如果只要部分参数加密，可以使用websys_interfaceEncrypt方法来加密发送，后台使用`@InterfaceDecrypt`来解密指定的某些参数

- GET请求部分参数加密示例：

```js
let passwordEnc = websys_interfaceEncrypt("123456");
let usernameEnc = websys_interfaceEncrypt("admin");
$iget('/xx/xxx',{password:passwordEnc,username:usernameEnc,loc:'部分科室'},function(rtn){
	console.log(rtn.data);
});
```

```java
@GetMapping("/xxx")
@InterfaceDecrypt(keyParams = {"password","username"}) // 前台只对用户与密码参数加密
public BaseResponse<MyVo> xxxGet(String password, String username, String loc){
    // do something
    return BaseResponse.success(myVo);
}
```

- POST请求部分参数加密示例：

```js
let passwordEnc = websys_interfaceEncrypt("123456");
let usernameEnc = websys_interfaceEncrypt("admin");
$ipost('/xx/xxx',{password:passwordEnc,username:usernameEnc,loc:'部分科室'},function(rtn){
  	console.log(rtn.data);
});
```

```java
@PostMapping("/xxx")
@InterfaceDecrypt(bodyParams = {"password","username"})  // 前台只对用户与密码参数加密
public BaseResponse<MyDto> xxxPost(@RequestBody MyDto dto){
    return BaseResponse.success(dto);
}
```

  

#### 6.2 后台数据加密返回

```java
@InterfaceEncrypt
@PostMapping("/xxx")
public BaseResponse<IPage<MyVO>> findByCodeOrDescription(@RequestBody MyDto dto) {
    return BaseResponse.success(myServices.find(dto));
}
// 返回{code:"200",msg:"success",data:"xxxxxxxxxx"}
// 前台自动解密成{code:"200",msg:"success",data:{records:[{}],current:1,page:1,size:10...}}
```



### 7、导出Excel

- 1. `后台Java代码`（确保引入了`hiscfsv-bsp`依赖）

```java
// 在后台Controller方法上加上@EnableExcelExport注解，表明需要导出
// 返回可以是 BaseResponse<List<MyVo>> 或  BaseResponse<IPage<MyVo>> 或  BaseResponse<HisDataAndMapPage<MyVO>> 或 实现了接口IEnableExcelResponse的MyResponse
@ApiOperation("使用EnableExcelExport示例")
@GetMapping("/list")
@EnableExcelExport(voClass=MyVO.class,note="导出说明")
public BaseResponse<List<MyVo>> getListByItemId(@RequestParam("p1") String p1) {
   return BaseResponse.success(myService.getList(itemId));
}
```
> 当访问此Rest服务时，会把MyVo中属性及属性上的@ApiModelProperty(value = "属性中文")注解值存入`基础配置`-`表格导出管理`界面，可以在此配置界面配置列信息

- 2. `前台JS代码`

```js
// 把rest对应的list或page数据转成excel并下载到前台
// 通过传入{resultSetType:"excel"}参数实现导出Excel功能,fileName来定义导出文件名称(不带后缀),
$ipost('/xx/xxx',{resultSetType:"excel",fileName:'文件名'}); // 导出excel
$ipost('/xx/xxx',{resultSetType:"pdf",fileName:'文件名'});   // 导出pdf文件
$ipost('/xx/xxx',{resultSetType:"excelPrint",printer:''}) // 导出并打印pdf
```

```js
// 导出过程并增加提示层
$.messager.progress({title: "提示",msg: 'Excel文件下载中',text: $g('下载中...')});
$ipost('/xx/list',{p1:"入参",resultSetType:"excel",fileName:'文件名'},function(){
    $.messager.progress("close");  // 关闭遮罩层
});
```


```js
// 指定纸张大小为A3/A4且横向输出,默认A4-纵向，rotate只支持0与90
$ipost('/x/x',{resultSetType:"pdf",fileName:'文件名',pageSize:"A3",rotate:"90"});
$ipost('/x/x',{resultSetType:"excelPrint",printer:'x',pageSize:"A4",rotate:"90"})
```



### 8、文件上传

- 使用HOS的`/file/uploadReturnUrl`服务上传，文件存储在minio服务上

  前端代码写法如下：

```html
<input type="file" id="upfile" onchange="return uploadFile();">
```
```js
function uploadFile(){
    let fileObj = document.getElementById('upfile').files[0];
    var formData = new FormData();
    formData.append('file', fileObj); // file参数名与后台Controller参数一致
    //formData.append('fileName', fileObj.name); // 可以传其它参数
    
    $.messager.progress({title: "提示",msg: '文件上传中',text: $g('上传中...')});
    // 使用HOS公共的后台服务(FileUploadController)，文件存储在minio服务上
    $ipost('/file/uploadReturnUrl',formData,function(rtn){
        // 返回后台文件相关信息
        $.messager.progress("close");
    });
}
```
- 也可以自己编写后台服务

```java
@PostMapping({"/xxx"})
public BaseResponse<FileVo> uploadReturnUrl(@RequestParam(value = "myparam") String myparam, MultipartFile file) {
    // :DOTO
}
```
```js
function uploadFile(){
    let fileObj = document.getElementById('upfile').files[0];
    var formData = new FormData();
    formData.append('file', fileObj); // file参数名与后台Controller参数一致
    formData.append('myparam', "myparam"); // 可以传其它参数
    $ipost('/xx/xxx',formData,function(rtn){
        // 返回后台文件相关信息
    });
}
```

### 9、国际化方法
```js
// 用于翻译
// 在/base/scripts/locale/zh_CN.js内写入对应键值对
// 可以使用$g来实现转对应提示功能
$g('notNull.code')  //返回代码不能为空
$g('password.errorTimes',[3,2]); // 你已错误登录3次,还有2机会
$g('你已错误登录{0}次,还有{1}机会',[3,2]); // 你已错误登录3次,还有2机会
$g('你已错误登录{etimes}次,还有{rtimes}机会',{etimes:3,rtimes:2}); // 你已错误登录3次,还有2机会
```
```html
<!-- html内翻译使用hisui-label -->
<td class="hisui-label">姓名</td>
<span class="hisui-label">登记号</span>
```

### 10、润乾打印方法

```html
<!--查看报错界面 runqian/html/runqian.preview.html?reportName=bsp/sys/test.rpx -->
<!--引入公共打印js-->
<script src="../../scripts/websys.print.comm.js"></script>
<script type="text/javascript">
    websys_printByRunQian({
        {
           reportName:"报表名称"      //不用带报表的后缀,必填 bsp/sys/test
           needSelectPrinter:'yes|no'  //是否需要用户选择打印机, 默认no
           printerName:'打印机名称'   // 打印机名
           reportParam : {   // 会自动加上session中信息作为参数
              stDate:'2023-11-18',
              endDate:'2023-11-19' 
         }
    });
</script>
```

### 11、中间件调用
```html
<!--引入中间件js ， 业务插件放入\base\addins\plugin\目录-->
<script type="text/javascript" src="../../scripts/websys.jquery.bsp.js"></script>
<addins></addins>
<!--业务使用 -->
<script type="text/javascript">
// 在非window客户端上使用ws实现中件间功能,只能通过回调方法得到返回值
// 示例1。不同操作系统下区别调用
if ((navigator.platform.indexOf("Linux")>-1){
    CmdShell.Run("ipconfig",rtn=>console.log(rtn));
	CmdShell.Run("calc.exe");
    CmdShell.Run("chrome.exe https://www.hisui.cn");
}else{
    // 推荐回调
    CmdShell.Run("ip a",rtn=>console.log(rtn));
    CmdShell.Run("chrome https://www.hisui.cn");
}
</script>
```
```js
// 示例2。不同平台下不同zip包示例
XX.clear();
if (navigator.platform.indexOf("Linux x86_64")>-1){
	XX.data[0]["_dllDir"] = XX.data[0]["_dllDir"].replace('.zip',"-linux-x64.zip");
}				
if ((navigator.platform == "Win32") || (navigator.platform == "Windows")){
	XX.data[0]["_dllDir"] = XX.data[0]["_dllDir"].replace('.zip',"-win.zip");
}
if ((navigator.platform == "Linux aarch64"))
	XX.data[0]["_dllDir"] = XX.data[0]["_dllDir"].replace('.zip',"-linux-arm64.zip");
}
XX.mymethod(arg,rtn=>console.log(rtn));
```
```js
//示例3。 不同操作系统下脚本命令相同时，可以统一写法
helloTestObj.clear();
helloTestObj.cmd('HelloTest.jar myArg1 myArg2',function(rtn){
    console.log(rtn);
    //{msg:"success",rtn:"第0个入参myArg1,第1个入参myArg2,~.~Hello Addins !",status:200}
});
```


### 12、图表组界面调用

路径：`/bsp/menugroup/html/chart.html`

参数说明

| 参数名             | 参数说明                                                     | 是否必传 |
| ------------------ | ------------------------------------------------------------ | -------- |
| `chartBookCode`    | 图表组代码。如：`IPOrderChart`                               | 必传     |
| `switchSysPat`     | 切换病人时，是否修改头菜单框架病人信息。默认：N；[ Y 或 N]   | 可选     |
| `patientListPanel` | 左侧病人列表路径，不传时左侧区域隐藏。如：`../emr/browse/html/emr.browse.episodelist.html` | 可选     |
| `NoPatientBanner`  | 是否显示病人信息条，[ Y或N ]                                 | 可选     |
| `patientListPage`  | 当病人信息条显示时，病人信息条 - 病人列表按钮关联的界面      | 可选     |
| `episodeId`        | 就诊id，不传时会取头菜单中就诊id                             | 可选     |
| `patientId`        | 病人id，不传时会取头菜单中病人id                             | 可选     |
| `patListCollapse`  | 是否折叠侧边病人列表，1时折叠，空或不传展开                  | 可选     |

