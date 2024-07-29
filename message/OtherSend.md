
<style>
table td:first-of-type {
    word-break:keep-all;
}

/*超过1600 侧边显示目录*/
@media screen and (min-width: 1600px) {
    .markdown-body>ul:first-of-type{
        position: fixed;
        right: calc(50% - 800px) ; 
        width: 300px;
        height: 90%;
        top: 5%;
        overflow: auto;
        list-style-type:none;
    }
     .markdown-body>ul:first-of-type ul{
        list-style-type:none;
     }

}

</style>
- [1. 发送消息接口](#1-发送消息接口)
    - [OtherInfoJson说明](#otherinfojson说明)
- [2. 包装后的消息发送接口](#2-包装后的消息发送接口)
  - [2.1 消息发送 不指定接收者](#21-消息发送-不指定接收者)
  - [2.2 消息发送 指定科室](#22-消息发送-指定科室)
  - [2.3 消息发送 指定安全组](#23-消息发送-指定安全组)
  - [2.4 消息发送 指定科室安全组](#24-消息发送-指定科室安全组)
  - [2.5 消息发送 指定用户](#25-消息发送-指定用户)
  - [2.6 消息发送 指定用户工号](#26-消息发送-指定用户工号)
  - [2.7 消息发送 指定医护人员](#27-消息发送-指定医护人员)
  - [2.8 消息发送 指定诊疗组](#28-消息发送-指定诊疗组)
  - [2.9 消息发送 指定接收对象(消息平台维护的接收对象)](#29-消息发送-指定接收对象消息平台维护的接收对象)
    - [消息接收对象](#消息接收对象)
  - [2.10 消息发送 指定院区](#210-消息发送-指定院区)
  - [2.11 消息发送 指定人员类型](#211-消息发送-指定人员类型)


## 消息平台其它消息发送接口 ##

消息平台其它消息发送接口为HIS8.4新增接口，支持了更多的接收者的指定方式，如登录科室、科室人员、科室医生、科室护士、安全组、科室安全组组合、用户、用户工号、人员、诊疗组、消息平台接收对象等
`HIS8.4`


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
| ReceiveCode    | 接收消息接收代码<br>Type-Keys-Role,Type-Keys-Role  | Type (L登录科室,LCP科室人员,LCPD科室医生,LCPN科室护士,<br>G安全组,LG科室安全组,<br>U用户,UC用户工号,CP人员,MU诊疗组,<br>M消息平台接收对象,PAT患者,CPTT人员类型,<br>H院区用户,HCP院区医护人员,HCPD院区医生,HCPN院区护士)<br>Keys 对应类型id或代码,多个可以^拼接<br>Role(Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组)  |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| TaskSchedule      | 定时发送时间安排字符串        |    定时发送参数见<a href="#taskschedule说明">TaskSchedule说明</a>                 |

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
| ExtReceiveCode |  | 扩展接收对象代码，格式为/mode:type-key-role-tmpl <br>mode 发送方式 I=HIS消息  S 手机短信 WX微信 <br>type 类型 (L科室、G安全组..) <br>key 和类型对应 <br>role 目标角色<br>tmpl 使用模板 |
| IngoreReceiveCfg |  | 忽略掉消息类型维护的配置：接收对象、高级接收对象、抄送人 |
| PatientID |  | 患者ID 用于没有患者就诊ID，又想要使用患者本人接收对象或内容模板有患者相关变量时  |

*** 除以上指定属性外产品组还可以传其他属性，用于通过消息模板组装消息内容、短信内容数据。 ***

OtherInfoJson为Json字符传，建议使用工具类进行构造，规避自己拼接时的错误

```vb
;只指定linkParam形式，最终由消息配置的链接和linkParam共同组合成完整链接
s adm="1234",appno="100001"
s jsonObj=##class(BSP.SYS.COM.ProxyObject).%New()
s jsonObj.linkParam="EpisodeID="_adm_"&ApplyNo="_appno   ;消息对应业务界面所需参数
s jsonObj.BizObjId=appno ;业务ID  用于消息后续处理、撤销等
s otherInfoJson=jsonObj.%ToJSON()    ;转成Json字符串

;指定link形式，link属性为要开的链接级所需参数，此时不再需要linkParam属性
s adm="1234",appno="100001"
s jsonObj=##class(BSP.SYS.COM.ProxyObject).%New()
s jsonObj.link="xxxxxxxx.csp?EpisodeID="_adm_"&ApplyNo="_appno   ;消息对应业务界面链接和参数
s jsonObj.BizObjId=appno ;业务ID  用于消息后续处理、撤销等
s otherInfoJson=jsonObj.%ToJSON()    ;转成Json字符串

```


##### TaskSchedule说明 #####

定时发送时间安排字符传，支持多种规则，按^分隔。对于产品组来说一般只使用传具体时间点这种规则。

| *按^分隔位置* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| 1   | 开始时间    | 格式 yyyy-MM-dd hh:mm |
| 2   | 结束时间    | 格式 yyyy-MM-dd hh:mm |
| 3   | 最多执行次数    |  |
| 4   | 规则1 固定时间点    | 格式 yyyy-MM-dd hh:mm，多个时间点以\|分隔 |
| 5   | 规则2 固定间隔    | 单位秒，应尽量使用整分钟即60的整数倍 |
| 6   | 规则3 cron表达式    | 以cron表达式定义规则，需对cron表达式有所了解 |

*** 规则1、2、3都会受限于于开始日期，结束日期，最多执行次数；且规则2是依托于开始时间进行计算的。 ***

对于产品组来说，一般使用的是规则1，按固定时间点进行发送，即此参数^分隔第四位传时间点，其它位置空。

```vb
s TaskSchedule=""
s $p(TaskSchedule,"^",4)="2023-07-27 17:00|2023-07-27 17:30|2023-07-27 18:00"  //^分隔第四位为固定时间点  多个时间点用|符号分隔

```





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
| LocGroupId      | 科室ID\|安全组ID        | 多个以^分割  <br>如1\|1^1\|2                  |
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


##### 消息接收对象 #####

需看项目实际版本

| *代码* | *描述*      | *备注*         |
| AdmDoctorMedUnit | 主治医生医疗单元 |  | 
| AdmDoctor | 主治医生 |  | 
| OrderDoctor | 下医嘱医生 |  | 
| OrderDoctorMedUnit | 下医嘱医生医疗单元 |  | 
| AdmLoc | 就诊科室 |  | 
| WardDoctor | 科室医生 |  | 
| WardNurse | 病区护士 |  | 
| WardDoctorNurse | 科室医生与病区护士 |  | 
| OAUserCode | OA用户Code |  | 
| OrderRecLocDoc | 医嘱接收科室医生 |  | 
| OrderRecLocNur | 医嘱接收科室护士 |  | 
| TriageNurse | 分诊区护士 | 就诊-->队列-->分诊区-->诊区操作员 | 
| OrderRecLoc | 医嘱接收科室医护人员 |  | 
| OrderRecLocLogon | 医嘱接收科室用户 |  | 
| OrderLoc | 开单科室医护人员 |  | 
| OrderLocDoc | 开单科室医生 |  | 
| OrderLocNur | 开单科室护士 |  | 
| OrderLocLogon | 开单科室用户 |  | 
| SpecCollNurse | 标本采集护士 |  | 
| AdmLocDirector | 科主任  | 拥有患者科室x某某主任安全组登录权限的用户 | 
| AdmLocDirectorCT | 科主任(科室表) | 科室表上的科主任字段 | 
| WardNurseDirector | 病区护士长 | 拥有患者护理单元x某某护士长安全组登录权限的用户 | 
| OpAdmLinkIpLoc | 门诊关联科室 | 发送给患者门诊科室关联的住院科室去 | 
| TheHospDoc | 本院医生 | 本院依赖发送科室进行判定 | 
| TheHospNur | 本院护士 | 本院依赖发送科室进行判定 | 
| TheHospLogon | 本院所有用户 | 本院依赖发送科室进行判定 | 
| TheHospALLCP | 本院所有医护人员 | 本院依赖发送科室进行判定 | 
| TheSiteDoc | 本系统所有医生 |  | 
| TheSiteNur | 本系统所有护士 |  | 
| TheSiteLogon | 本系统所有用户 |  | 
| TheSiteALLCP | 本系统所有医护人员 |  | 




#### 2.10 消息发送 指定院区 ####

传院区ID及限定类型取院区下用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendHosp(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, HospId, Type , TargetRole)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见 [消息类型](MSGActionType)                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 多个医嘱ID 用英文逗号分隔，如获取不到可以为空                                           |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| HospId      | 院区ID        |                     |
| Type      | 类型        | DOCTOR医生,NURSE护士,空所有用户,ALLCP所有医护人员                 |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|



#### 2.11 消息发送 指定人员类型 ####

传人员类型取系统下所有用户以及消息类型配置的并集发送
```vb
w ##class(BSP.MSG.SRV.Interface).SendCPTT(Context, ActionTypeCode, FromUserRowId, EpisodeId , OrdItemId , OtherInfoJson , EffectiveDays, CreateLoc, Type , TargetRole)
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
| Type      | 类型        | DOCTOR医生,NURSE护士,空所有用户,ALLCP所有医护人员                 |
| TargetRole      | 目标角色  <br>（此消息希望用户登录哪个科室哪个安全组看到）      | 传空自动判断<br>Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|