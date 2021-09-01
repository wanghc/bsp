# [信息安全等级保护测评](https://baike.baidu.com/item/%E4%BF%A1%E6%81%AF%E5%AE%89%E5%85%A8%E7%AD%89%E7%BA%A7%E4%BF%9D%E6%8A%A4/2149325?fr=aladdin)处理

## 1. 防注入 ##
+ 为了系统安全，在需求去除SQL,HTML,JS注入关键词的地方使用, 常用在csp或cls中

```vb
 if ##class(websys.Conversions).IsValidClassName("websys.Filter") d ##class(websys.Filter).InjectionFilter()
```

```html
如：
<csp:method name=OnPreHTTP arguments="" returntype=%Boolean>
	 if ##class(websys.Conversions).IsValidClassName("websys.Filter") d ##class(websys.Filter).InjectionFilter()
     if ##Class(websys.SessionEvents).SessionExpired() q 1  // 登录后才能查看，否则弹出登录界面
     q 1
    ...
</csp:method>
```
> 测试办法，在某个界面输入框中写入：`'><sVg/OnLoAd=prompt(1)>` 看是否弹出界面


+ 前端处理html/js注入

```js
function htmlEncode (str) {
	var ele = document.createElement('span');
	ele.appendChild( document.createTextNode( str ) );
	return ele.innerHTML;
}
// 在发送值到后台前使用
desc = htmlEncode(desc); //会把html代码转成
```

## 2. 目录浏览禁用

+ 进入IIS - 选中imedical目录 - 功能视图中找到【目录浏览】-- 右边点击【禁用】按钮

## 3. 缺少响应头

 解决办法进入IIS - 选中imedical目录 - 功能视图中找到【HTTP响应标头】，添加相应响应标头

+ 缺少X-XSS-Protection响应头
> 添加X-XSS-Protection:1;mode=block
>
> 如果启用XXS保护可能影响系统
+ 缺少X-Content-Type-Options响应头
> 添加X-Content-Type-Options:nosniff  
+ 缺少X-Frame-Options响应头
> 添加X-Frame-Options:SAMEORIGIN
>
> 表示当前系统只能引用同源界面
>
> X-Frame-Options:ALLOW-FROM uri 时可以指定来源的frame
+ 缺少Content-Security-Policy响应头
> ~~添加Content-Security-Policy:script-src 'self' 'unsafe-eval' 'unsafe-inline' clef.io jquery.min.js;~~

  ## 4. 信任Referer安全

```vb
if (%request.GetCgiEnv("HTTP_REFERER")["ibm.com"){ Q 0 }
if (%request.GetCgiEnv("HTTP_REFERER")["hcl.com"){ Q 0 }
```

## 5. 登录提示

1. demo -- 翻译菜单 -- 字典翻译，在界面中找到`用户不存在`，翻译成`用户或密码错误`


## 6. 越权访问

   

