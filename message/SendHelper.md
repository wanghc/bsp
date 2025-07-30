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
- [发送工具类SendHelper](#发送工具类SendHelper)
  - [%New](#-new)
  - [SetBizData](#setbizdata)
  - [SetBizLink](#setbizlink)
  - [SetContextByMode](#setcontextbymode)
  - [AddReceiver](#addreceiver)
  - [ClearReceiver](#clearreceiver)
  - [IngoreReceiveCfg](#ingorereceivecfg)
  - [AddTmplData](#addtmpldata)
  - [Send](#send)
  - [SendAt](#sendat)
  - [SendFreq](#sendfreq)
  - [SendCron](#sendcron)
- [调用流程](#调用流程)
- [使用JSON对象或字符串发送](#使用json对象或字符串发送)




### 发送工具类SendHelper ###

由于消息平台越来越复杂，支持的功能越来越多，原本的发送接口参数被扩展的越来越臃肿，参数的各种分隔符越来越多，所以实现了此工具类用于消息发送。
`HIS9.0.3`

#### %New ####

创建工具类实例对象

```vb
s helper=##class(BSP.MSG.SRV.SendHelper).%New(context , actionTypeCode , createUser , createLoc , effectiveDays)
```

##### 参数说明 ######

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| context  | 消息内容或模板代码   |        |
| actionTypeCode |  消息动作类型代码      | 代码参见[动作类型清单](MSGActionType)，如无对应代码请通过BOS提交需求增加       |
| createUser |  发送消息的用户Id       | 如果获取不到HIS用户Id, 可以传入"^姓名"            |
| createLoc |  发送人科室Id      | 如果没有科室Id，可传"＾科室描述"               |
| effectiveDays |  消息有效天数      | 此有效天数级别高于动作类型所配置              |

##### 返回值 ######

SendHelper实例



#### SetBizData ####

设置业务相关数据，就诊ID、医嘱ID、患者ID、关联业务ID

```vb
d helper.SetBizData(episodeId , ordItemId , patientId , bizObjId )
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| episodeId  | 就诊ID    |  |
| ordItemId | 医嘱ID      | 多个医嘱ID 用英文逗号分隔 |
| patientId | 患者ID    |  |
| bizObjId |  关联业务ID    | 如果需要做需要处理的消息，此参数需要传 |

##### 返回值 ######

该对象


#### SetBizLink ####

设置业务链接

```vb
d helper.SetBizLink(link , linkParam , dialogWidth , dialogHeight , target  )
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| link  | 业务链接    | 可以是his内相对路径如 `criticalvalue.trans.hisui.csp?ReportId=1000001\|\|1&RepType=1` <br> 也可以是其它完整的url路径 如 `https://www.baidu.com/s?ie=UTF-8&wd=a`> |
| linkParam | 链接参数      | 用于和消息类型上配置的链接 共同拼接成业务链接 <br>如配置成`criticalvalue.trans.hisui.csp`，linkParam为`ReportId=1000001\|\|1&RepType=1`   <br> 拼接后为`criticalvalue.trans.hisui.csp?ReportId=1000001\|\|1&RepType=1` |
| dialogWidth | 窗口宽度    | 打开处理(查看)界面时界面宽度。数字或百分比(`HIS8.3`以后) |
| dialogHeight |  窗口高度    | 打开处理(查看)界面时界面高度。数字或百分比(`HIS8.3`以后) |
| target |  目标窗口  | 如果为_blank 采用window.open新窗口方式打开，否则为顶层界面弹出hisui(easyui)模态框，内嵌iframe形式打开 |

##### 返回值 ######

该对象


#### SetContextByMode ####

按照发送方式设置消息内容或模板

```vb
d helper.SetContextByMode(mode , context )
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| mode  | 发送方式    | I信息系统即HIS，S短信，E邮箱，TEL电话，HCCS医呼通 |
| context | 消息内容或模板代码      |  |

##### 返回值 ######

该对象


#### AddReceiver ####

添加接收者

```vb
d helper.AddReceiver(type , key , role , mode , tmpl)
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| type  | 类型    | L登录科室,LCP科室人员,LCPD科室医生,LCPN科室护士,<br>G安全组,LG科室安全组,<br>U用户,UC用户工号,CP人员,MU诊疗组,<br>M消息平台接收对象,PAT患者,CPTT人员类型,<br>H院区用户,HCP院区医护人员,HCPD院区医生,HCPN院区护士 |
| key | 对应类型类型的数据标识      | 如U对应key应是用户ID，G对应key是安全组ID... |
| role | 目标角色    | Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组 |
| mode |  发送方式    | I信息系统即HIS，S短信，E邮箱，TEL电话，HCCS医呼通 |
| tmpl |  模板代码  | 使用的消息模板代码  <br> <span style="color:red">只有某些发送方式才支持在接收对象上设置模板：短信</span> |


##### 返回值 ######

该对象


#### ClearReceiver ####

清空接收者

```vb
d helper.ClearReceiver()
```

##### 返回值 ######

该对象



#### IngoreReceiveCfg ####

忽略掉消息动作类型维护上的接收者（接收对象、抄送人、高级接收对象）

```vb
d helper.IngoreReceiveCfg()
```

##### 返回值 ######

该对象


#### AddTmplData ####

增加模板数据 

```vb
d helper.AddTmplData( key , value)
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| key  | 模板变量代码    |  |
| value | 模板变量值      |  |


##### 返回值 ######

该对象


#### Send ####

发送消息

```vb
s ret=helper.Send()
```

##### 返回值 ######

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|


#### SendAt ####

在某时间点发送消息

```vb
s ret=helper.SendAt(datetime1 , datetime2 , datetime3 , datetime4 , datetime5)
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| datetime1  | 时间1    | yyyy-MM-dd hh:mm  如2024-01-01 11:58 |
| datetime2  | 时间2    |  |
| datetime3  | 时间3    |  |
| datetime4  | 时间4    |  |
| datetime5  | 时间5    |  |

##### 返回值 ######

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|


#### SendFreq ####

从某时间开始，每隔一段时间进行发送

```vb
s ret=helper.SendFreq(startDatetime, stopDateTime, maxTimes, intervalMinutes)
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| startDatetime  | 开始时间    | yyyy-MM-dd hh:mm  如2024-01-01 11:58 |
| stopDateTime  | 结束时间    |  |
| maxTimes  | 最大发送次数    |  |
| intervalMinutes  | 发送间隔(分钟)    | 从开始时间起每隔多久发送一次 |

##### 返回值 ######

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|



#### SendCron ####

按照cron表达式规则发送

```vb
s ret=helper.SendCron(startDatetime, stopDateTime, maxTimes, cronExp)
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| startDatetime  | 开始时间    | yyyy-MM-dd hh:mm  如2024-01-01 11:58 |
| stopDateTime  | 结束时间    |  |
| maxTimes  | 最大发送次数    |  |
| cronExp  | cron表达式    |  |

##### 返回值 ######

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|



### 调用流程 ###

1.创建实例对象

```vb
    s helper=##class(BSP.MSG.SRV.SendHelper).%New(context , actionTypeCode , createUser , createLoc , effectiveDays)
```

2.其它设置（此步骤下的调用需结合业务实际情况决定要不要进行设置，下面每个方法返回值都是当前对象引用，所以可以链式调用）

2.1 设置业务数据

```vb
    d helper.SetBizData(episodeId , ordItemId , patientId , bizObjId )
```

2.2 设置业务链接信息

```vb
    d helper.SetBizLink(link , linkParam , dialogWidth , dialogHeight , target  )
```

2.3 设置不同发送方式的消息内容

```vb
    d helper.SetContextByMode(mode , context )
```


2.4 增加模板变量

```vb
    d helper.AddTmplData( key , value)
```

2.5 增加接收人

```vb
    d helper.AddReceiver(type , key , role , mode , tmpl)
```

2.6 忽略配置的接收人

```vb
    d helper.IngoreReceiveCfg()
```

3.发送（支持立即发送和定时发送，下面方法选择一个即可）

3.1 立即发送

```vb
    s ret= helper.Send()
```

3.2 固定时间发送

```vb
    s ret=helper.SendAt(datetime1 , datetime2 , datetime3 , datetime4 , datetime5)
```

3.3 固定频率发送

```vb
    s ret=helper.SendFreq(startDatetime, stopDateTime, maxTimes, intervalMinutes)
```

3.4 按Cron表达式发送

```vb
    s ret=helper.SendCron(startDatetime, stopDateTime, maxTimes, cronExp)
```



### 使用JSON对象或字符串发送 ###

工具类提供了一系列的成员方法进行消息发送，但是需要多次调用不同的方法设置不同的参数，某些场景下产品组可能想自己构造好参数，只调用一次方法发送消息，此时可以使用JSON对象或字符串进行发送。`HIS9.2.0`

```vb
    s data={......}  //自己构造参数
    s ret=##class(BSP.MSG.SRV.SendHelper).SendByJson(data)
```

##### 参数说明 ######

| *参数名* | *说明*      |  *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| data  | 参数    | 可以为JSON对象（`%Library.DynamicObject`、`BSP.SYS.COM.ProxyObject`）或JSON字符串 |

###### data结构 #######

| *参数名* | *说明*      | *类型*      |  *备注*                                                 |
| -------------- | ----------------- | ----------------- | ------------------------------------------------------------ |
| context  | 消息内容或模板代码   | string |         |
| actionTypeCode |  消息动作类型代码      | string | 代码参见[动作类型清单](MSGActionType)，如无对应代码请通过BOS提交需求增加       |
| createUser |  发送消息的用户Id       | string | 如果获取不到HIS用户Id, 可以传入"^姓名"            |
| createLoc |  发送人科室Id      | string | 如果没有科室Id，可传"＾科室描述"               |
| effectiveDays |  消息有效天数      | string | 此有效天数级别高于动作类型所配置              |
| episodeId  | 就诊ID    | string | 就诊ID |
| ordItemId | 医嘱ID      | string | 多个医嘱ID 用英文逗号分隔 |
| patientId | 患者ID    | string | 患者ID |
| bizObjId |  关联业务ID    | string | 如果需要做需要处理的消息，此参数需要传<br>标本拒收记录ID、处方点评记录、危急值报告ID、申请单ID、申请单ID-审核阶段、会诊申请子表ID等 |
| link  | 业务链接    | string | 可以是his内相对路径如 `criticalvalue.trans.hisui.csp?ReportId=1000001\|\|1&RepType=1` <br> 也可以是其它完整的url路径 如 `https://www.baidu.com/s?ie=UTF-8&wd=a&usercode=${LOGON.USERCODE}` |
| linkParam | 链接参数      | string | 用于和消息类型上配置的链接 共同拼接成业务链接 <br>如配置成`criticalvalue.trans.hisui.csp`，linkParam为`ReportId=1000001\|\|1&RepType=1`   <br> 拼接后为`criticalvalue.trans.hisui.csp?ReportId=1000001\|\|1&RepType=1` |
| dialogWidth | 窗口宽度    | string | 打开处理(查看)界面时界面宽度。数字或百分比 |
| dialogHeight |  窗口高度   | string | 打开处理(查看)界面时界面高度。数字或百分比 |
| target |  目标窗口  | string | 如果为_blank 采用window.open新窗口方式打开，否则为顶层界面弹出hisui(easyui)模态框，内嵌iframe形式打开 |
| multiContext |  多发送方式消息内容  | object | 当不同发送方式需要指定不同的内容或模板时使用，一般可以不传 <br>对象的属性为发送方式代码，属性值为对应发送方式的内容或模板 |
| &ensp;&ensp;&#124;─I |  HIS消息内容或模板  | string | HIS消息内容或模板  |
| &ensp;&ensp;&#124;─S |  短信内容或模板  | string | 短信内容或模板  |
| &ensp;&ensp;&#124;─... |  其他  | string |   |
| receivers |  消息接收者  | array | 消息接收者数组，支持按照用户、科室、安全组、预定义的消息接收对象 |
| &ensp;&ensp;&ensp;&ensp;&#124;─ |  | object |  |
| &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&#124;─type  | 类型    | string | L登录科室,LCP科室人员,LCPD科室医生,LCPN科室护士,<br>G安全组,LG科室安全组,<br>U用户,UC用户工号,CP人员,MU诊疗组,<br>M消息平台接收对象,PAT患者,CPTT人员类型,<br>H院区用户,HCP院区医护人员,HCPD院区医生,HCPN院区护士 |
| &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&#124;─key | 对应类型类型的数据标识     | string | 如U对应key应是用户ID，G对应key是安全组ID... |
| &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&#124;─role | 目标角色    | string | Auto自动,AdmLoc就诊科室,OrdLoc下医嘱科室,Any任何,Lx某科室,Gx某安全组,LxGx某科室某安全组 |
| &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&#124;─mode |  发送方式   | string | I信息系统即HIS，S短信，E邮箱，TEL电话，HCCS医呼通 |
| &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&#124;─tmpl |  模板代码  | string | 使用的消息模板代码  <br> <span style="color:red">只有某些发送方式才支持在接收对象上设置模板：短信</span> |
| tmplData |  模板变量数据  | object | 当使用消息模板组织内容数据时，有些变量需要产品组传进来<br>对象的属性为变量的名称，属性值为对应变量的值  |
| &ensp;&ensp;&#124;─var1 | 变量1  | string |   |
| &ensp;&ensp;&#124;─varA |  变量A  | string |   |
| &ensp;&ensp;&#124;─... |  其他  | string |   |
| ingoreReceiveCfg | 是否忽略消息配置的接收者 | string |  忽略掉消息动作类型维护上的接收者（接收对象、抄送人、高级接收对象） |
| sendAt | 在什么时间发送 | string |  指定发消息的时间点，支持指定多个，多个用单竖线分割，如`2025-07-29 18:00`  |
| intervalMinutes | 每隔几分钟发一次 | string |  从`startDatetime`起，每隔`intervalMinutes`分钟发一次，直到`stopDateTime`，最多发送`maxSendTimes`次   |
| cronExp | cron表达式 | string |    从`startDatetime`起，按cron表达式`cronExp`发送，直到`stopDateTime`，最多发送`maxSendTimes`次    |
| startDatetime | 开始时间 | string |  如`2025-07-29 18:00`  |
| stopDateTime | 结束时间 | string |  如`2025-07-31 18:00`  |
| maxSendTimes | 最大发送次数 | string |  如`5`  |

*** `sendAt`、`intervalMinutes`、`cronExp` 为需要使用消息定时发送时才需要的参数，如果只是正常的立即发消息，不需要指定 ***

##### 返回值 ######

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|