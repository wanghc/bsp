# LIS新打印
LISPrint是检验组的打印插件。原使用LODOP打印XML模板，通过相应的改变，可以使用新的LISPrint打印。具体说明如下。
## 项目更新步骤
- 1. `web/scripts/DHCPrtComm.js`文件发我们组对比修改，修改完后更新。
- 2. 放资源 `\web\scripts\LISPrintForHisOnlyJS.js`
- 3. 把lisprint.zip放入`\web\addins\plugin\lisprint\`目录下(目录不存在需要新建目录)
- 4. 每台电脑使用管理员安装iMedical插件管理：WebsysServerSetup.msi；存在的跳过此步骤。
- 5. 环境测试，导入LisPrintTest.csp，访问csp页面，点打印。打印成功表示环境部署成功，成功后可以选择删除测试页。

## 产品组更换打印方法
通知项目组开发人员修改：

### 1、js引入

```html
<script type="text/javascript" src="../scripts/DHCPrtComm.js"></script>
<script type="text/javascript" src="../scripts/LISPrintForHisOnlyJS.js"></script>
```

> a、js 路径是否需要修改
> b、DHCPrtComm.js已经有就不用新加

### 2、 DHC_PrintByLodop修改为 DHC_PrintByLIS

```js
//LODOP打印 xml 模板
DHCP_GetXMLConfig("encName","xmlFlagName");
var LODOP = getLodop();
DHC_PrintByLodop(LODOP,inpara,inlist,jsonArr,reportNote)
```
可以使用以下打印替换

```js
//LISPrint打印xml模板
DHCP_GetXMLConfig("encName","xmlFlagName");
DHC_PrintByLIS("",inpara,inlist,jsonArr,reportNote)
```

## 打印案例


### 1、xml 模板用LIS打印
```javascript
// 打印测试2——模板打印
function print2() {
    var xmlFlagName = "hztest1"; 				
    var modelName = document.getElementById('modelName').value;	
    if (modelName) xmlFlagName = modelName;
    // 获取打印模板
    DHCP_GetXMLConfig("", xmlFlagName);
    // 打印测试数据：
    var c2 = String.fromCharCode(2);
    var inpara = "name" + c2 + "myName^patno" + c2 + "000009" + "^img0" + c2+"data:image/svg;base64,iVBO.....";
    var inlist = "DrugName1^Price1^DrugUnit1" + c2 + "DrugName2^Price2^DrugUnit2";
    var jsonArr = [];
    var reportNote = xmlFlagName; //打印任务名
    // 打印
    DHC_PrintByLIS("",inpara,inlist,jsonArr,reportNote)
}
```
### 2、自由打印

```javascript
//打印表格
function print3 () {
    PrintWidget.Init();
    var datas = [];
    datas.push({ Name: "张三", Sex: "男", Age: "24", Phone: "15122222222" });
    datas.push({ Name: "李四", Sex: "男", Age: "24", Phone: "15122222222" });
    datas.push({ Name: "王二", Sex: "男", Age: "24", Phone: "15122222222" });
    datas.push({ Name: "zlz", Sex: "男", Age: "26", Phone: "18900752521" });
    PrintWidget.AddGrid(datas, ["Name", "Sex", "Age", "Phone"], null, ["left", "center", "right", "left"], 800, 10, 30, null, null, 60, 1, 50, null, 1168);
    PrintWidget.PrintOut();
}
```

```javascript
function print() {
    PrintWidget.Init();
    // 文本
    PrintWidget.AddText(340, 10, "第一页测试文本", "center", "red", "楷体", "22", "bold", "10", "", "", "", "");
    // 图片
    PrintWidget.AddPic(340, 20, 100, pheight, pval);
    // 线条
    PrintWidget.AddLine(10, 42, 800, 42, 3, "", "#000000");
    // 点
    PrintWidget.AddPoint(10, 80, 3, "", "red");
    PrintWidget.AddPoint(60, 80, 4, "", "red");
    PrintWidget.AddPoint(110, 80, 9, "[]", "red");
    PrintWidget.AddPoint(160, 80, 12, "", "red");
    PrintWidget.AddPoint(210, 80, 14, "", "red");
    //多边形
    PrintWidget.AddPoly([[200, 100], [250, 100], [280, 150], [250, 200], [200, 200], [150, 150]], "blue");
    PrintWidget.PrintOut();
}
```
