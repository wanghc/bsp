## XML打印功能及更新说明 ##

### 第一步. 引用插件(`三选一`)

#### 	1. iMedical系统CSP中，在IE下使用`DHCOPPrint.cab`打印请使用以下类方法引用控件 ####
```vb
d ##class(web.DHCBillPrint).InvBillPrintCLSID()
```
#### <span name="t2" id="t2">	2</span>. iMedical系统CSP中，在`IE`/`Chrome`下使用`LODOP`打印时请使用以下类方法引用相关 ####
```c#
d ##class(web.DHCXMLPConfig).LODOPInit()  // IE下引用LODOP，Chrome下引用CLODOP
或
// 2020-09-18 增加参数NeedCLodop
d ##class(web.DHCXMLPConfig).LODOPInit("1")   //强制使用CLodop,可用效解决打印img时iMedical超时问题
```
#### 	3. 组件中引入，使用隐藏的`CUSTOM`元素，在`Custom Expression`中写入<a href="#t2">以上</a>相应的语句即可。 ####
### 第二步. JS代码修改

####  	LODOP打印脚本代码示例  ####

```javascript
DHCP_GetXMLConfig("encryptItemId","xmlFlagName");  // 加载名为xmlFlagName的模板
var LODOP = getLodop();
/*
  inpara => name_$c(2)_zhangsha^patno_$c(2)_000009
  inlist => DrugName^Price^DrugUnit^Qty^PaySum_$c(2)_DrugName2^Price2^DrugUnit2^Qty2^PaySum2
  jsonArr => [{type:"invoice",PrtDevice:"pdfprinter"},{type:"line",sx:1,sy:1,ex:100,ey:100},{type:"text",name:"patno",value:"0009",x:10,y:10,isqrcode:true,lineHeigth:5}]
  reportNote => 打印任务名称，可区别本次打印任务
  options => printListByText:true表示按label打印列表，LetterSpacing:-2控制字符间空隙
*/
DHC_PrintByLodop(LODOP,inpara,inlist,jsonArr,reportNote,{printListByText:true})
```
* 推荐使用`CLODOP`打印

# 更新日志 #

## 2020-11-17

+ XML设计器支持二维码版本定义功能，解决二维码大小不一问题

## 2020-09-18 ##

 * 提供强制引用CLodop打印功能，解决某些IE使用LODOP打印后，系统超时问题
 ```java
// 强制初始化为CLodop
d ##class(web.DHCXMLPConfig).LODOPInit("1")
 ```
 或使用JS引用
```html
<!--强制初始化为CLodop-->
<script type="text/javascript" src="../scripts_lib/lodop/LodopFuncs.js?needCLodop=1" charset="UTF-8"></script>
```


## 2020-09-10 ##

#### LODOP打印方法 ####
## 2019-09-06 ##
##### 版本1,0,0,83 #####
* 控件打印支持指定纸张名字。如指定A5纸打印，即可配置`invoice`节点中属性`PrtPage="A5"`

## 2019-07-24 ##
##### 版本1,0,0,82 #####
* 修复txtdata节点中`printvalue`,`defaultvalue`,`fontsize`,`fontbold`,`fontname`属性不存在时，打印不异常问题

## 2019-07-09 ##
##### 版本1,0,0,81 #####
* 为`txtdata`增加`width`与`height`属性,如果内容超出时自动换行打印，不配置默认自由长度打印。
* 图片没定义宽高默认图片大小
* 二维码与条形码没定义宽高默认30mm
* 服务器图片打印不再缓存图片，每次都取服务器图片
* 增加多处错误提示

## 2018-10-11 ##
* xml设计器增加即打即停配置. `LandscapeOrientation`属性值增加Z值，表示即打即停，且纸张设置选为手动设置纸张大小，`height`表示底边留白高度。此属性只支持lodop打印
* xml设计器增加条码打印. `txtdatapara`增加属性`barcodetype`,此属性只支持`lodop`打印
* 实现`DHC_PrintBYLodop`方法，打印xml及数据

## 2017-11-30 ##
##### 版本1,0,0,65 #####
* 基于QRmaker.ocx控件打印二维码，提升扫码率

## 2017-06-12 ##
##### 版本1,0,0,64 #####
* 在`ListData`节点增加`BackSlashWidth`属性，定义list输出结束后打印反斜线宽度。用于处方结束处

## 2017-06-08 ##
##### 版本1,0,0,63 #####
* 在`PICdatapara`节点上增加`width`与`height`属性，可实现图片缩放功能

## 2017-04-06 ##
##### 版本1,0,0,56 #####
* 支持服务器图片打印，会自动把服务器图片下到本地C:\imedical\xmlprint\cache\目录下。打印方法为ToPrintHDLP。
* 只有**ToPrintHDLP**方法支持打印服务器图片，其它方法只能打印本地图片
* xml打印基于vb开发，支持图片格式为gif,jpg,jpeg,icon,cur,不支持png
* 打印服务器图片时，路径及图片名称中**不要包含汉字**，可能导致某些图片不能下载成功

## 2017-2-10 ##
* xml设计器中可与类方法关联
* xml设计器中可与query关联

## 2016-10-26 ##
* 提交第二版xml设计器

## 说明 ##
* 注意所有属性不能省略，老的xml打印没有保护
* xml打印基于vb开发，支持图片格式为gif,jpg,jpeg,icon,cur,不支持png
* PrtPaperSet=HAND 表示纸张大小手工设置，纸张大小走height与width数值
* PrtPaperSet=WIN 表示纸张大小走PrtPage配置的纸张大小
* PrtDevice打印机名配置
* LandscapeOrientation打印方向，Y横向打印，X纵向打印
