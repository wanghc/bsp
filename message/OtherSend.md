
<style>
table td:first-of-type {
    word-break:keep-all;
}
</style>

## 消息平台其它消息发送接口 ##

消息平台其它消息发送接口为HIS8.4新增接口，支持了更多的接收者的指定方式，如登录科室、科室人员、科室医生、科室护士、安全组、科室安全组组合、用户、用户工号、人员、诊疗组、消息平台接收对象等

### 1. 发送消息接口 ###
```vb
w ##class(BSP.MSG.SRV.Interface).Send(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , ReceiveCode , OtherInfoJson , EffectiveDays, CreateLoc,TaskSchedule)
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| ReceiveCode    | 接收消息接收代码<br>Type-Keys-Role,Type-Keys-Role  | Type (L登录科室,LCP科室人员,LCPD科室医生,LCPN科室护士,G安全组,LG科室安全组,U用户,UC用户工号,CP人员,MU诊疗组,M消息平台接收对象,PAT患者)<br>Keys 对应类型id或代码,多个可以^拼接<br>Role(Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组)  |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| TaskSchedule      | 定时发送时间安排字符串        |                    |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|

*** 消息实际接收人由`配置`和`传参`共同决定，取并集 ***


##### OtherInfoJson说明 #####

| *键*         | *示例值*                   | *说明*                      |
| -----------| ----------------------- | --------------------------------------------- |
| linkParam | EpisodeId=1&ReportId=002 | 链接参数，与动作类型配置的Link合成URL，便于修改csp路径。<br>如: 把危机值1000类型对应的处理链接维护为criticalvalue.trans.csp则消息明细对应的处理URL为criticalvalue.trans.csp?EpisodeId=1&ReportId=002   |
| link      | criticalvalue.trans.csp?EpisodeId=1&ReportId=002 | 业务处理或查看明细URL，级别高过动作类型配置的link |
| dialogWidth | 1000 默认1000 | 打开处理界面时界面宽度。界面宽度为1000px 支持百分比表示占顶层宽度的百分比如80%(`HIS8.3`以后) |
| dialogHeight | 500  默认 500 | 打开处理界面时界面高度。界面高度为500px支持百分比表示占顶层宽度的百分比如50%(`HIS8.3`以后) |
| target | 默认空 | 目标窗口 如果为_blank 采用window.open新窗口方式打开，否则为顶层界面弹出hisui(easyui)模态框，内嵌iframe形式打开 |
| BizObjId | 1 | 业务系统ID，用于后续消息处理、撤销定位消息 |


### 2. 包装后的消息发送接口 ###

对上接口`发送消息接口`进行包装，更易使用


#### 2.1 消息发送 不指定接收者 ####

取消息类型配置发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendCfg(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|




#### 2.2 消息发送 指定科室 ####

传科室ID及限定类型取科室下用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendLoc(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, LocId , Type , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| LocId      | 科室ID        | 多个以^分割                    |
| Type      | 类型        | DOCTOR医生,NURSE护士,Logon登录权限,其它科室医护人员                  |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|



#### 2.3 消息发送 指定安全组 ####

传安全组ID取安全组下用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendGroup(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, GroupId , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| GroupId      | 安全组ID        | 多个以^分割                    |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|




#### 2.4 消息发送 指定科室安全组 ####

传科室ID安全组ID组合取科室安全组下用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendLocGroup(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, LocGroupId , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| LocGroupId      | 科室ID|安全组ID        | 多个以^分割  <br>如1|1^1|2                  |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|



#### 2.5 消息发送 指定用户 ####

传用户ID取用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendUser(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, UserId , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| UserId      | 用户ID        | 多个以^分割                    |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|


#### 2.6 消息发送 指定用户工号 ####

传用户工号取用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendUserCode(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, UserCode , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| UserCode      | 用户工号        | 多个以^分割                    |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|


#### 2.7 消息发送 指定医护人员 ####

传医护人员ID取用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendCareProv(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, CareProvId , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| CareProvId      | 医护人员ID        | 多个以^分割                    |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|



#### 2.8 消息发送 指定诊疗组 ####

传诊疗组ID取用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendMedUnit(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, MedUnitId , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| MedUnitId      | 诊疗组ID        | 多个以^分割                    |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|



#### 2.9 消息发送 指定接收对象(消息平台维护的接收对象) ####

传接收对象代码（消息平台预定义的常用接收对象）取用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendReceiveType(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, ReceiveTypeCode , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| ReceiveTypeCode      | 消息平台接收对象代码        | 详细见消息消息接收对象维护                    |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|
