# XMLPreView
实现预览xml内容功能, 把xml及数据展示到Canvas上，此功能支持IE11及Chrome浏览器
```js
    DHC_PreviewByCanvas(canvas, inpara, listpara, printjson, xmlflag, cfg)
    canvas : HTMLDocument
    inpara : 与打印参数一致
    listpara : 与打印列表参数一致
    printjson : 打印json数据
```

## 20240307

- 增加encoderOptions配置项，实现文件压缩

  ```js
  // encoderOptions是0到1间的一个小数,表示压缩比例
  DHC_PreviewByCanvas(canvas, inpara, listpara, printjson, xmlflag, {encoderOptions:0.2})
  ```


## 20220422

- 请求增加global:false配置项，解决某些IE下报无效访问

## 20220309
#### 1.0.1
- 实现把xml模板预览成Canvas，方便导成图片, 导成pdf, 导成pdfbase64
