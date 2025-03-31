## XML打印功能及更新说明

XML打印功能包含XML设计器与XML打印二部分，设计器设计好模板后，可以使用DHCOPPrint.CAB或LODOP来打印模板，以下介绍二种打印功能

### 使用LODOP打印XML

1. 引用插件`（二选一）`

   + iMedical系统的`CSP`中引入，在`IE`/`Chrome`下使用LODOP打印时请使用以下类方法引用相关

     ```c#
     d ##class(web.DHCXMLPConfig).LODOPInit()    // IE下引用LODOP,Chrome下引用CLODOP
     或
     // 2020-09-18 增加参数NeedCLodop,默认"0"
     d ##class(web.DHCXMLPConfig).LODOPInit("1")   // 强制使用CLodop,可用效解决打印img导致iMedical超时问题
     ```

   + iMedical系统的`组件`中引入，使用隐藏的`CUSTOM`元素，在`Custom Expression`中写入<a href="#t1">以上</a>相应的语句即可。

2. JS脚本代码示例

   ```javascript
   DHCP_GetXMLConfig("encryptItemId","xmlFlagName");  //xmlFlagName为XML模板, 用于加载XML内容
   var LODOP = getLodop();
   /*
    @description 使用LODOP打印XML
    @param {LODOP} LODOP 控件对象
    @param {String} inpara 元素对应的打印值
    @param {String} inlist 列表对应的打印值
    @param {Array} jsonArr [可选] 动态追加打印内容
    @param {String} reportNote [可选] 打印任务名称，可区别本次打印任务
    @param {Object} options [可选] 打印配置信息
   */
   DHC_PrintByLodop(LODOP,inpara,inlist,jsonArr,reportNote,{printListByText:true});
   ```
   
   + <details>
         <summary>入参详细内容</summary>
         <pre><ul><li>@param {LODOP} LODOP 控件对象</li>
     <li>@param {String} inpara 元素对应的打印值
     name_$c(2)_zhangsha^
     patno_$c(2)_000009^
     img1_$c(2)_data:image/png;base64,iVAAA...AA^
     img2_$c(2)_http://172.0.0.1/imedical/web/images/xx/xx.gif^
     img3_$c(2)_c:\\xx.gif
     </li>
     <li>@param {String} inlist 列表对应的打印值
     DrugName1^Price1^DrugUnit1^Qty1^PaySum1_$c(2)
     _DrugName2^Price2^DrugUnit2^Qty2^PaySum2_$c(2)
     _DrugName3^Price3^DrugUnit3^Qty3^PaySum3
     </li>
     <li>@param {Array} jsonArr [可选] 动态追加打印内容
        [{type:"invoice",PrtDevice:"pdfprinter"},{type:"line",sx:1,sy:1,ex:100,ey:100},{type:"text",name:"patno",value:"0009",x:10,y:10,isqrcode:true,lineHeigth:5}]
     </li>
     <li>@param {String} reportNote [可选] 打印任务名称，可区别本次打印任务</li>
     <li>@param {Object} options [可选] 打印配置信息
           <table>
           <tr><td>printListByText:true</td><td>true按label打印列表。false按html-table方式打印。默认false，一般应使用true</td></tr>
           <tr><td>LetterSpacing:0</td><td>控制字符间空隙。0正常空隙，-2紧凑或其它数值。默认0</td></tr>
           <tr><td>preview:0</td><td>0打印,1预览。默认0</td></tr>
           <tr><td>tableBorder:0</td><td>默认是0, 数字表示线宽。替换listHtmlTableBorder</td></tr>
           <tr><td>tdnowrap:true</td><td>true时列宽固定800mm。false时为二列间宽度，内容可自动换行</td></tr>
           <tr><td>pdfDownload:false</td><td>得到打印后的PDF文件并下载下来</td></tr>
           <tr><td>onCreatePDFBase64:undefined</td><td>值为函数时，会把把PDF文件转成base64字符串，以入参方式传给函数</td></tr>
           <tr><td>PrtDevice:undefined</td><td>强制设置打印机名称</td></tr>
     	  <tr><td>columnTitle:undefined</td><td>用于每页重打。如："ColTitle1^ColTitle2^ColTitle3^ColTitle4"</td></tr>
     	  <tr><td>pageShowColunmTitle:false</td><td>是否每页显示表头。默认不显示</td></tr>
     	  <tr><td>pageTableStartPostion:"ONEPAGE"</td><td>第一页启始位置一样。为数字时表示启始位置yrow值单位mm。默认ONEPAGE</td></tr>
     	  <tr><td>rowContentFit: false</td><td>默认false 行内容是否自动换行。替换listHtmlTableWordWrapFlag</td></tr>
     	  <tr><td>rowHeightExpand: false</td><td>默认false 是否推动表格后面元素位置</td></tr>
     	  <tr><td>listAfterCallback:function</td><td>默认null,当表格打印完成后，回调此函数 20230224</td></tr>
     	  <tr><td>onPrintEnd</td><td>打印完成时回调方法</td></tr>
     	  </table>
     </li></ul></pre>
     </details>
   
   - 推荐使用`CLODOP`打印

### 使用CAB打印XML

1. 引入插件（二选一）
   - iMedical系统的`CSP`中引入，在IE下使用`DHCOPPrint.CAB`打印请使用以下类方法引用控件
   
     ```c#
     d ##class(web.DHCBillPrint).InvBillPrintCLSID()
     ```
   
   - iMedical系统的`组件`中引入，使用隐藏的`CUSTOM`元素，在`Custom Expression`中写入<a href="#t1">以上</a>相应的语句即可。

2. JS脚本代码示例

   ```js
   DHCP_GetXMLConfig("encryptItemId","xmlFlagName");  //xmlFlagName为XML模板, 用于加载XML内容
   var PObj = document.getElementById("ClsBillPrint");
   /*
     @description 使用LODOP打印XML
     @param {HTMLObject} PObj 控件对象
     @param {String} inpara 元素对应的打印值。和lodop的差区在不能支持img-base64打印
     @param {String} inlist 列表对应的打印值
     @param {Array} jsonArr [可选] 动态追加打印内容
     @param {Float} invHeight [可选] 票据的高度。CAB中判断打印换页是：发现元素位置top超过height就会换页打印，如果发现一个元素超过一页后，后面所有元素都会分页打印。通过invHeight可以解决。2018-09-20 增加invHeight 分页处理。默认空
   */
   DHCP_XMLPrint(PObj, inpara, inlist, jsonArr,invHeight);
   ```



### 更新日志 ###

### 2024-03-07

- 打印得到base64方法，增加配置项encoderOptions实现压缩文件

```js
// 第6入参为增加配置项encoderOptions,encoderOptions是0到1间的一个小数,表示压缩比例
DHC_PrintByLodop(getLodop(),itmInfo,listInfo,ajson,"xmlname",
      {printListByText:true,tdnowrap:false,encoderOptions:0.2,onCreatePDFBase64:function(pdfbase64){
		console.log(pdfbase64);
	}
});
```



### 2023-02-24

- DHC_PrintByLodop打印方法增加listAfterCallback回调方法, 在打印列表结束后调用

```js
  // 回调方法入参
  listAfterCallback({
      PrinterObj:LODOP,  /*当前打印对象*/
      tableTop: parseInt(tableTop), /*表格的top位置*/
      tableLeft: parseInt(tableLeft), /*表格的left位置*/
      rowHeight:rowHeight, /*每一行的行高多少*/
      y: parseInt(tableTop) + ((currPageRowNo + 1) * rowHeight), /*行的起始y位置*/
      x: tableLeft, /*行的起始x位置*/
      currPageRowNo: currPageRowNo, /**当前行号*/
      pageRows:xmlPageRows, /*一页打印多少行数据*/
      backSlashWidth:xmlBackSlashWidth /*表格反斜线宽度*/
  });
```

- 使用示例

```js
var otherCfg = {};			
otherCfg.printListByText = true;
/*打印列表结束回调。翻页时只有最后一张会进入此方法一次*/
otherCfg.listAfterCallback = function(cfg){
    if (cfg.currPageRowNo!=pageRows){  // 数据不满行时才打印反斜线
        // ADD_PRINT_LINE(起点y,起点x,结束点y,结束点x,0=实线,2=线宽)
	    cfg.PrinterObj.ADD_PRINT_LINE(cfg.y + "mm", "80mm", (parseInt(cfg.y)+10)+"mm", "60mm", 0, 2);
    }
};
DHC_PrintByLodop(getLodop(),itmInfo,listInfo,exPrintJson,"打印任务名", otherCfg);
```



#### 2021-11-25

- 增加获得打印PDF文件后的base64内容功能

  ```js
  // 通过虚拟打印机打印pdf,然后再获得pdf文件的base64内容
  // 第5入参{String}类型, 为打印机任务名。如果虚拟打印机设置文档名与任务名一致时,也为pdf文件名
  // 第6入参为{Obejct}类型, 
  //     PrtDevice: {String} 强制使用包含Zan名称的第一台打印机打印,优先级高于XML模板中打印机配置
  //     onCreatePDFBase64: {function} 为得到base64内容的回调方法
  DHC_PrintByLodop(getLodop(),itmInfo,listInfo,ajson,"pdf1234",
        {printListByText:true,tdnowrap:false,PrtDevice:"Zan",onCreatePDFBase64:function(pdfbase64){
  		console.log(pdfbase64);
  	}
  });
  ```

  

#### 2021-05-23

- 修复点击列表中图片/条码/二维码后，再点击非列表元素会导致有些输入框不能输入 :bug:

#### 2021-05-20

- 增加页脚打印配置，打印到界面的底部中间位置，格式如：`第#页/共&页`

#### 2021-04-19

- 【列表项】可以定义成图片，条形码，二维码及其相关配置 :sparkler:

#### 2021-01-26

* XML设计器支持文本/图片/二维码/条形码/线条配置是否重复打印 :sparkler:

#### 2020-11-17

+ XML设计器支持二维码版本定义功能，解决二维码大小不一问题

#### 2020-09-18

* 提供强制引用CLodop打印功能，解决某些IE使用LODOP打印后，系统超时问题

  ```c#
  // 强制初始化为CLodop
  d ##class(web.DHCXMLPConfig).LODOPInit("1")
  ```

  或使用JS引用

  ```html
  <!--强制初始化为CLodop-->
  <script type="text/javascript" src="../scripts_lib/lodop/LodopFuncs.js?needCLodop=1" charset="UTF-8"></script>
  ```


#### 2020-09-10
- LODOP打印方法

#### 2019-09-06
##### 版本1,0,0,83 #####
* 控件打印支持指定纸张名字。如指定A5纸打印，即可配置`invoice`节点中属性`PrtPage="A5"`

#### 2019-07-24
##### 版本1,0,0,82 #####
* 修复txtdata节点中`printvalue`,`defaultvalue`,`fontsize`,`fontbold`,`fontname`属性不存在时，打印不异常问题

#### 2019-07-09 ##
##### 版本1,0,0,81 #####
* 为`txtdata`增加`width`与`height`属性,如果内容超出时自动换行打印，不配置默认自由长度打印。
* 图片没定义宽高默认图片大小
* 二维码与条形码没定义宽高默认30mm
* 服务器图片打印不再缓存图片，每次都取服务器图片
* 增加多处错误提示

#### 2018-10-11 ##
* xml设计器增加即打即停配置. `LandscapeOrientation`属性值增加Z值，表示即打即停，且纸张设置选为手动设置纸张大小，`height`表示底边留白高度。此属性只支持lodop打印
* xml设计器增加条码打印. `txtdatapara`增加属性`barcodetype`,此属性只支持`lodop`打印
* 实现`DHC_PrintBYLodop`方法，打印xml及数据

#### 2017-11-30 ##
##### 版本1,0,0,65 #####
* 基于QRmaker.ocx控件打印二维码，提升扫码率

#### 2017-06-12 ##
##### 版本1,0,0,64 #####
* 在`ListData`节点增加`BackSlashWidth`属性，定义list输出结束后打印反斜线宽度。用于处方结束处

#### 2017-06-08 ##
##### 版本1,0,0,63 #####
* 在`PICdatapara`节点上增加`width`与`height`属性，可实现图片缩放功能

#### 2017-04-06 ##
##### 版本1,0,0,56 #####
* 支持服务器图片打印，会自动把服务器图片下到本地C:\imedical\xmlprint\cache\目录下。打印方法为ToPrintHDLP。
* 只有**ToPrintHDLP**方法支持打印服务器图片，其它方法只能打印本地图片
* xml打印基于vb开发，支持图片格式为gif,jpg,jpeg,icon,cur,不支持png
* 打印服务器图片时，路径及图片名称中**不要包含汉字**，可能导致某些图片不能下载成功

#### 2017-2-10 ##
* xml设计器中可与类方法关联
* xml设计器中可与query关联

#### 2016-10-26 ##
* 提交第二版xml设计器

### 说明
* 注意所有属性不能省略，老的xml打印没有保护
* CAB打印基于vb开发，支持图片格式为gif,jpg,jpeg,icon,cur,不支持png
* PrtPaperSet=HAND 表示纸张大小手工设置，纸张大小走height与width数值
* PrtPaperSet=WIN 表示纸张大小走PrtPage配置的纸张大小
* PrtDevice打印机名配置
* LandscapeOrientation打印方向，Y横向打印，X纵向打印