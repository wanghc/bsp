## LIS打印代码理解

概述：LIS打印前端代码见文件：LISPrintForHisOnlyJS.js；引入这个js后，就可以使用 **PrintWidget** 对象，他是打印控制的唯一对象。所有属性，功能都在对象里面。 **PrintWidget**中，所有的页面的元素，比如文本、变量、条形码、二维码、图片、表格等等，统一用一个数组对象  **printWidget**存储。(注意为小写) 所有的页面元素，可抽象为Item；Item有统一的属性：

### 各种方法打印

1. 打印文本

   ```js
   //添加文本
   AddText(PrintX, PrintY, PrintText, PrintAlignment, PrintColor, PrintFont, PrintFontSize, PrintFontStyle, PrintLength, PrintWidth, PrintHeight, Angle, IsVShow, PageRepeat) 
   ```

2. 打印线

   ```js
   //添加线
   AddLine(StartX, StartY, EndX, EndY, LineSize, LineType, PrintColor);
   ```

3. 打印图片

   ```js
   //添加图片
   AddPic(PrintX, PrintY, PrintWidth, PrintHeight, Data, PrintFlag);
   ```

4. 打印表格

   ```js
   AddGrid(datas, ["Name", "Sex", "Age", "Phone"], null, ["left", "center", "right", "left"], 800, 10, 30, null, null, 60, 1, 50, null, 1168);
   ```

5. 打印点

   ```js
   AddPoint(10, 80, 3, "", "red")
   ```

6. 打开多边形

   ```js
   AddPoly([[200, 100], [250, 100], [280, 150], [250, 200], [200, 200], [150, 150]], "blue");
   ```

7. 打开自定义类型

   ```js
   /**
   * @param {String} PrintType 为打印类型，枚举型
       "PRINTER"类型元素,"PRINTERONLY"类型元素,"PAGE"类型元素,"Label"类型元素,"Data"类型元素,"CheckBox"类型元素
       "Radio"类型元素,"Result"类型元素,"Microbe"类型元素,"ILineN"类型元素,"BarCode"类型元素,"BarCodeN"类型元素
       "Point"类型元素,"Poly"类型元素,"Graph"类型元素,"FILE"类型元素,"PDF"类型元素
   * @param {Number} PrintX 元素X坐标
   * @param {Number} PrintY 元素Y坐标
   * @param {String} PrintFont 元素字体 默认"宋体"
   * @param {Number} PrintFontSize 元素字体大小 默认:10
   * @param {String} PrintFontStyle 元素字体样式 如："Bold"
   * @param {Number} PrintLength 打印长度
   * @param {Number} PrintWidth 元素宽度
   * @param {Number} PrintHeight 元素高度
   * @param {String} PrintText 打印文本
   * @param {String} DataField 数字字段
   * @param {String} PrintFlag 打印标识  打印图片的值是BASE64一样传值就行 PrintFlag为D
   * @param {String} PrintAlignment 文本停靠 如：“Left"
   * @param {String} PrintImageFile 图片文件,路径或base64
   * @param {String} PrintColor 元素颜色
   * @param {Number} Angle 元素旋转角度
   */
   AddPrintItem(PrintType,PrintX,PrintY,PrintFont,PrintFontSize,PrintFontStyle,PrintLength,PrintWidth,PrintHeight,PrintText,DataField,PrintFlag,PrintAlignment,PrintImageFile,PrintColor,Angle,IsVShow)
   ```

   

其他文档说明，参考检验组的文档：《C.检验对外纯JS打印说明.doc》