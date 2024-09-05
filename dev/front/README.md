## 前端公共方法说明

以HOS功能为基础，整合HISUI的前端最小工程范例

## 公共方法

### 获得头菜单Window对象
```js
var menuWin = websys_getMenuWin();
```

### 获得当前会话信息
```js
var session = websys_getSession();
```

### 获得头菜单表单
```js
/// 头菜单表单
websys_setMenuForm({EpisodeID:2,PatientID:1});
var frm = websys_getMenuForm();
var admId = frm.EpisodeID.value;     // 得到2
var patientId = frm.PatientID.value; // 得到1
websys_resetMenuForm();
admId = frm.EpisodeID.value;     // 得到''
patientId = frm.PatientID.value; // 得到''
```
### 请求后台时使用的方法

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
#### 导出Excel

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
#### 文件上传

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

### 国际化方法
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

### 润乾打印方法

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

### 中间件调用
```html
<!--引入中间件js ， 业务插件放入\base\addins\plugin\目录-->
<script type="text/javascript" src="../../scripts/websys.jquery.bsp.js"></script>
<addins></addins>
<!--业务使用 -->
<script type="text/javascript">
    CmdShell.Run("calc.exe");
    CmdShell.Run("chrome.exe https://www.hisui.cn");
</script>
```
