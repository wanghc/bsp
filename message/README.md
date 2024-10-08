
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

- [消息平台](#消息平台)
	- [1. 发送消息接口](#1-发送消息接口)
	- [2. 消息处理](#2-消息处理)
		- [2.1 消息处理接口ExecAll](#21-消息处理接口execall)
		- [2.2 消息处理接口Exec](#22-消息处理接口exec)
		- [2.3 消息撤销接口Cancel](#23-消息撤销接口cancel)
	- [3. 配置说明](#3-配置说明)
		- [3.1 接收对象](#31-接收对象)
		- [3.2 消息动作类型](#32-消息动作类型)
		- [3.3 高级接收对象配置](#33-高级接收对象配置)
	- [4. 其它接口](#4-其它接口)
		- [4.1 获取消息内容ID接口](#41-获取消息内容id接口)
		- [4.2 获取消息明细ID接口](#42-获取消息明细id接口)
		- [4.3 获取消息回复记录接口](#43-获取消息回复记录接口)
		- [4.4 获取某科室最近发送某类型消息记录](#44-获取某科室最近发送某类型消息记录)
		- [4.5 消息确认接口](#45-消息确认接口)
		- [4.6 设置科室消息配置](#46-设置科室消息配置)
		- [4.7 获取阅读信息接口](#47-获取阅读信息接口)
		- [4.8 停止定时发送任务接口](#48-停止定时发送任务接口)
		- [4.9 消息查询接口](#49-消息查询接口)
		- [4.10 用户消息列表接口](#410-用户消息列表接口)
	- [5. 常见问题](#5-常见问题)
		- [5.1 消息变为已处理的几种方式](#51-消息变为已处理的几种方式)




## 消息平台 ##
### 1. 发送消息接口 ###
```vb
w ##class(websys.DHCMessageInterface).Send(Context, ActionTypeCode, FromUserRowId, EpisodeId, OrdItemId, 
        ToUserRowId, OtherInfoJson, ToLocRowId, EffectiveDays , CreateLoc)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 代码参见[动作类型清单](MSGActionType)，如无对应代码请通过BOS提交需求增加                     |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。非空时消息显示时会显示患者信息及就诊信息    |
| OrdItemId      | 医嘱Id            | 多个医嘱ID 用英文逗号分隔，如获取不到可以为空。                                       |
| ToUserRowId    | 接收消息的用户Id  | 多个以^分隔，可以为空 (SS_User.SSUSR_RowId)                                 |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
|ToLocRowId     | 接收消息的科室 Id | 可以为空。<br>格式"LocId1^LocId2^LocId3\|其它标记" <br> "1^2^3" 发给科室1、2、3所有医护人员<br> "1^2^3\|ToNurse"发给科室1、2、3所有护士<br>"1^2^3\|ToDoctor"发给科室1、2、3所有医生<br>"1^2^3\|Logon"发给有此科室1或2或3登录权限的所有用户(HIS8.4)<br>"1^2^3\|OnlyFlag"仅作一个标识告诉我们这个消息 是想发给哪个科室的人的，<br>具体哪些人还需要在前面ToUserRowId参数传<br>此参数主要是为了解决一个发给A科的所有人的消息（比 如会诊），<br>但是某人拥有AB两科的权限，在登录B科时 不查看 A科消息 |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
| TaskSchedule      | 定时发送时间安排字符串        |    定时发送参数见<a href="#taskschedule说明">TaskSchedule说明</a>       `HIS9.0.1`        |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|


*** 消息实际接收人由`消息类型接收对象配置`、`消息类型高级接收对象配置`、`消息类型抄送人配置`、`ToUserRowId参数`、`ToLocRowId参数`共同决定，取并集 ***

*** 如果需要按照安全组等其它方式指定接收者，见[其它发送接口](OtherSend) ***

*** 如果第三方调用，见[webservice接口](WSInterface) ***

*** 使用工具类发送，[SendHelper](SendHelper) ***


###### 消息动作类型 ######

| 消息类型         | 代码 | 入参        | 接受对象           | 备注                                           |
| ---------------- | ---- | ----------- | ------------------ | ---------------------------------------------- |
| 通知 | 2000 | | | 发送通知用 |
| OA通知 | 2001 | | | OA通知，用户传代码，程序单独转成用户ID |
| 危急值 | 1000 | EpisodeID | 危急值平台配置 | 危急值平台通讯，响应对象的接口<br>`危急值平台` |
| 感染 | 1001 | EpisodeID | 主管医生 | `医政` |
| [查看更多类型](MSGActionType) |  |  |  |  |

*** 消息动作类型尽可能保证在不同项目中对同一业务使用相同消息类型，避免消息类型的误用 ***



###### OtherInfoJson说明 ######

| *键*         | *示例值*                   | *说明*                      |
| -----------| ----------------------- | --------------------------------------------- |
| linkParam | EpisodeId=1&ReportId=002 | 链接参数，与动作类型配置的Link合成URL，便于修改csp路径。<br>如: 把危机值1000类型对应的处理链接维护为criticalvalue.trans.csp则消息明细对应的处理URL为criticalvalue.trans.csp?EpisodeId=1&ReportId=002   |
| link      | criticalvalue.trans.csp?EpisodeId=1&ReportId=002 | 业务处理或查看明细URL，级别高过动作类型配置的link |
| dialogWidth | 1000 默认1000 | 打开处理界面时界面宽度。界面宽度为1000px 支持百分比表示占顶层宽度的百分比如80%(`HIS8.3`以后) |
| dialogHeight | 500  默认 500 | 打开处理界面时界面高度。界面高度为500px支持百分比表示占顶层宽度的百分比如50%(`HIS8.3`以后) |
| target | 默认空 | 目标窗口 如果为_blank 采用window.open新窗口方式打开，否则为顶层界面弹出hisui(easyui)模态框，内嵌iframe形式打开 |
| BizObjId | 1 | 业务系统ID，用于后续消息处理、撤销定位消息 |
| ExtReceiveCode |  | 扩展接收对象代码，格式为/mode:type-key-role-tmpl <br>mode 发送方式 I=HIS消息  S 手机短信 WX微信 <br>type 类型 (L科室、G安全组..) <br>key 和类型对应 <br>role 目标角色<br>tmpl 使用模板 <br> `HIS9.0.1`|
| IngoreReceiveCfg |  | 忽略掉消息类型维护的配置：接收对象、高级接收对象、抄送人 `HIS9.0.1` |
| PatientID |  | 患者ID 用于没有患者就诊ID，又想要使用患者本人接收对象或内容模板有患者相关变量时 `HIS9.0.1` |

*** 除以上指定属性外产品组还可以传其他属性，用于通过消息模板组装消息内容、短信内容数据。 ***



OtherInfoJson为Json字符传，建议使用工具类进行构造，规避自己拼接时的错误

```c#
//只指定linkParam形式，最终由消息配置的链接和linkParam共同组合成完整链接
s adm="1234",appno="100001"
s jsonObj=##class(BSP.SYS.COM.ProxyObject).%New()
s jsonObj.linkParam="EpisodeID="_adm_"&ApplyNo="_appno   //消息对应业务界面所需参数
s jsonObj.BizObjId=appno    //业务ID  用于消息后续处理、撤销等
s otherInfoJson=jsonObj.%ToJSON()    //转成Json字符串

//指定link形式，link属性为要开的链接及所需参数，此时不再需要linkParam属性
s adm="1234",appno="100001"
s jsonObj=##class(BSP.SYS.COM.ProxyObject).%New()
s jsonObj.link="xxxxxxxx.csp?EpisodeID="_adm_"&ApplyNo="_appno   //消息对应业务界面链接和参数
s jsonObj.BizObjId=appno  //;业务ID  用于消息后续处理、撤销等
s otherInfoJson=jsonObj.%ToJSON()    //;转成Json字符串

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



### 2. 消息处理 ###

#### 2.1 消息处理接口ExecAll ####

用于已知消息明细记录ID（1.发送时记录下来，2.在消息处打开的处理界面会传入明细记录ID），来处理消息
`HIS8.0.2`

*** 建议优先考虑使用接口[2.2 消息处理接口Exec](#22-消息处理接口exec) ***

```vb
w ##class(websys.DHCMessageInterface).ExecAll(MsgDetailsId, ExecUserDr, ExecDate, ExecTime)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| MsgDetailsId   | 消息明细记录ID    | 不可为空. 点击【处理】按钮弹出的界面可以通过%request.Data("MsgDetailsId",1)拿到消息Id |
| ExecUserDr     | 处理用户ID      | 默认当前会话用户.  %session.Data("LOGON.USERID") |
| ExecDate  | 处理日期  | 默认当前日期.   +$h |
| ExecTime  | 处理时间  | 默认当前日期.  $p($h, ","2) |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-102|消息已执行过||
|-103|消息内容Id为空||
|-104|执行人为空||
|-105|消息明细Id不在明细表中||
|-106|消息明细Id不能为空||
|-108^ErrorMsg|其它错误||

示例
```html
 <Server>
	 //从%request内拿消息明细id
	 Set DetailsId = $g(%request.Data("MsgDetailsId",1))
 </Server>
 <script type="text/javascript">
	var DetailsId = "#(DetailsId)#";
	// 关闭消息弹出窗口方法
	function closewin(){
		window.close();
		top.HideExecMsgWin();
	}
	/// 执行所有相关消息
	function SendExec(){
		tkMakeServerCall("websys.DHCMessageInterface","ExecAll",DetailsId) 
	}
 </script>
```

#### 2.2 消息处理接口Exec ####

用于相应业务处理完成后，将消息置为已处理，此方法为根据业务数据(消息类型、就诊、医嘱、业务ID)去查找消息数据，将查到的最新一条消息置为已处理。注意要避免同一业务数据发送多条消息。
`HIS8.0.2`

```vb
w ##class(websys.DHCMessageInterface).Exec(ToUserId, ActionType, EpisodeId, OEOrdItemId, ObjectId, ExecUserDr, ExecDate, ExecTime,OtherParams)
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ToUserId   | 用户ID    | 为空处理所有人消息，不为空只处理此人消息 |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 如果发送消息的OtherInfoJson有BizObjId属性,请传BizObjId属性值;<br> 如果没有建议传OtherInfoJson的部分值用于确定哪条消息；<br>如果根据就诊或医嘱已经能唯一确定消息可以传空 |
| ExecUserDr     | 处理用户ID      | 默认当前会话用户.  %session.Data("LOGON.USERID") |
| ExecDate  | 处理日期  | 默认当前日期.   +$h |
| ExecTime  | 处理时间  | 默认当前日期.  $p($h, ","2) |
| OtherParams | 其它扩展参数 | 用于后续参数扩展，扩展多个用^分隔 默认空  `HIS8.4` |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-3|未找到消息||
|-102|消息已执行过||
|-103|消息内容Id为空||
|-107|执行人为空||
|-108^ErrorMsg|其它错误||

###### OtherParams以^分隔每个位置说明 ######

| *按^分隔位置* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| 1   | 只处理哪个人员类型的消息    | 为空处理所有`(CT_CarPrvTp.CTCPT_InternalType)[NURSE,DOCTOR,Technician,Pharmacist,Other]` |
| 2   | 审核拒绝标志(Y/N)    | 医呼通 需要审核通过或拒绝标志 Y通过接受 N拒绝驳回 `HIS8.5` |
| 3   | 审核备注拒绝原因    | 审核备注拒绝原因 `HIS8.5` |
| 4   | 查找最近几条消息处理    | 部分业务会出现同一业务ID多次发消息，且只在最后一次处理情况，故增加此参数实现查到最近几条消息来处理 `HIS9.0.1` |


示例
```html
<script type="text/javascript">
        // 关闭消息弹出窗口方法
        function closewin(){
                window.close();
                top.HideExecMsgWin();
	}
        /// 执行所有相关消息
        function SendExec(){
                //tkMakeServerCall("websys.DHCMessageInterface","Exec","","1000","","","PrescNO=102")		  
                tkMakeServerCall("websys.DHCMessageInterface","Exec","","1000","55","55||1","ReportId=102&RepType=1")
        }
</script>

```

#### 2.3 消息撤销接口Cancel ####

用于撤销已发送的消息  
撤销判断逻辑：读即处理消息，有一人读过则不可撤销，其它有一人处理过则不可撤销
`HIS8.3`


```vb
w ##class(websys.DHCMessageInterface).Cancel(ToUserId, ActionType, EpisodeId, OEOrdItemId, ObjectId, ExecUserDr, ExecDate, ExecTime,OtherParams)
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ToUserId   | 用户ID    | 无用参数（为方便，此方法参数设计和Exec一致） |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 如果发送消息的OtherInfoJson有BizObjId属性,请传BizObjId属性值;<br> 如果没有建议传OtherInfoJson的部分值用于确定哪条消息；<br>如果根据就诊或医嘱已经能唯一确定消息可以传空 |
| ExecUserDr     | 撤销用户ID      | 默认当前会话用户.  %session.Data("LOGON.USERID") |
| ExecDate  | 撤销日期  | 默认当前日期.   +$h |
| ExecTime  | 撤销时间  | 默认当前日期.  $p($h, ","2) |
| OtherParams | 其它扩展参数 | 用于后续参数扩展，扩展多个用^分隔 默认空 注意此参数在`HIS9.0.1`之后才有 |

###### OtherParams以^分隔每个位置说明 ######

OtherParams为了和处理方法Exec一致，^分隔的部分位置实际没用

| *按^分隔位置* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| 1   | 未用    |  |
| 2   | 未用    |  |
| 3   | 未用    |  |
| 4   | 查找最近几条消息撤销    | 部分业务会出现同一业务ID多次发消息，且只在最后一次撤销情况，故增加此参数实现查到最近几条消息来撤销 `HIS9.0.1` |


|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|数字|大于0表示成功||
|0|消息状态不可撤回||
|-2|消息状态不可撤回||
|-3|未找到消息||
|-107|撤销人为空||
|-100^ErrorMsg|表示失败|如:-100^ID错误|




### 3. 配置说明 ###

#### 3.1 接收对象 ####
预定义的常见的接收对象，一般和就诊、医嘱相关

| *代码* | *名称*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| AdmDoctor   | 主管医生    | 患者就诊的主管医生或接诊医生 |
| AdmDoctorMedUnit   | 主管医生医疗单元    | 患者就诊主管医生所属的医疗单元 |
| OrderDoctor    | 下医嘱医生    | 下医嘱医生 |
| OrderDoctorMedUnit   | 下医嘱医生医疗单元    | 下医嘱医生医疗单元 |
| AdmLoc   | 就诊科室    | 患者就诊科室所关联的医护人员 |
| WardDoctor     | 科室医生      | 患者就诊科室所关联的医生 |
| WardNurse  | 病区护士  | 患者所在病区关联的护士 |
| WardDoctorNurse  | 科室医生与病区护士  | 患者就诊科室和所在病区所关联的医护人员 |
| OAUserCode   | OA用户Code    | 特殊，配置为此对象时会将发送接口ToUserRowId参数传进来的值当作HIS用户工号处理 |
| OrderRecLocDoc     | 医嘱接收科室医生      | 医嘱接收科室关联的医生 |
| OrderRecLocNur  | 医嘱接收科室护士  | 医嘱接收科室关联的护士 |
| TriageNurse  | 分诊区护士  | 门诊患者对应诊区关联的操作员 |

#### 3.2 消息动作类型 ####

| *字段*      | *说明*                                                 |
| ----------------- | ------------------------------------------------------------ |
| 类型代码    | 唯一值，发送消息时使用代码来区分消息类型，当新增消息类型时请联系我们给统一编排消息类型代码 |
| 类型名称    | 用于显示 |
| 接收对象    | 此消息类型的<a href="#31-接收对象">接收对象</a>，有些消息的接收人无法用常见接收对象描述，那么此字段可为空，具体接收人可以在发送消息时通过参数传入 <br>消息最终接收人由`接收对象`、`高级接收对象`、`抄送人`、`接口参数`共同决定取并集 |
| 发送方式    | 此消息类型可以通过哪种发送方式提醒用户，一般只实现了信息系统 |
| 消息重要性    | 区分消息的重要程度，非常重要和紧急消息在未处理时会自动弹出，其中非常重要消息在读过一次后不再弹出 |
| 有效天数    | 消息多久之后自动变为已处理，同时消息发送接口上也有有效天数字段，参数优先级高于配置 |
| 团队执行消息    | 1.`消息相互独立,读后自己消息不显示`消息读后即变为已处理状态 2.`需要处理`(在老版本中分为了：`有一人处理,消息全部消失`和`全员处理,消息才算处理`,但是实际上控制是业务组调用方法时通过参数控制的，所以合并为一个)表示此消息需要业务处理，即读消息时不改变处理状态 |
| 读消息回调方法    | 消息第一次变为已读时是否要调用业务组的某个方法，说明见<a href="#读消息回调方法">读消息回调方法</a> |
| 消息处理链接    | 消息关联业务的处理业务的链接，和发送消息时OtherInfoJson.linkParam共同组成完整链接，但是优先级低于OtherInfoJson.link `8.4`及之前版本配了此链接则消息必须为需要处理，后续版本则扩展了可以作为一个不需要处理消息的查看详细业务的链接 |
| 工具按钮    | `执行按钮`用于部分消息想实现一人处理全都消失，但是又无法提供业务处理界面或无法对接消息处理接口时 |
| 弹出间隔    | 消息未处理时多少分钟再次弹出，配置应为消息查询间隔的整数倍，为空时当作5分钟 |
| 音频文件    | 此类型消息播放audio目录下哪个音频进行提示，如危急值配置为1000.wav，为空时则播放您有新的消息提示声，为字符串NULL时则不播放音频 |
| 弹出样式    | 消息`处理`或`查看`弹出界面样式 `dialogWidth`宽 `dialogHeight`高 `target`窗口形式 `level`H表示此配置高于OtherInfoJson |
| 需登录科室  | 由于用户可能有多个权限，就希望用户只有登录相应科室才看到相应科室的消息(后续有扩展安全组)，所以就有了目标角色的概念，发送时将目标角色记录下来，当此字段为Y时，则用户只有登录相应角色时才能查看到消息 |
| 出院字自动处理  | 当患者的状态为D时，是否将消息自动置为已处理 |
| 超过有效期不显示  | 消息因超期自动变为已处理时，是否能在已处理再看到 |
| 启用  | 消息类型是否启用 |
| 隐藏发送人  | 消息界面发送人显示为匿名 |
| 隐藏接收人  | 消息界面处理人，消息回复中，会显示为匿名 |
| 允许回复  | 此消息类型是否允许回复 |


##### 读消息回调方法 #####
用户在消息列表中，点击一条未读消息时会将其记为已读，此时消息平台会去调用此消息类型维护的读后回调方法
在DHC-APP命名空间下   类名和方法名自取
```vb
w ##class(FullClassName).MethodName(EpisodeId,OrdItemId,BizObjId,ReadUserRowId,ReadDate,ReadTime)
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| BizObjId   | 业务ID    | 发送消息时OtherInfoJson的BizObjId属性值 |
| ReadUserRowId | 阅读用户ID      |  |
| ReadDate  | 阅读日期  |  |
| ReadTime  | 阅读时间  |  |


#### 3.3 高级接收对象配置 ####
`8.4`版本扩展了高级接收对象配置，简单上使用时可以配置一些固定的科室、安全组作为接收对象，而不用再在程序里写死，复杂上使用是可以根据医院、就诊类型、科室、发送时段、发送方式配置不同的接收对象。

| *字段*      | *说明*                                                 |
| ----------------- | ------------------------------------------------------------ |
| 代码    | 此配置绑定到哪个消息类型代码上去 |
| 医院    | 为空或者某具体院区 |
| 就诊类型    | 为空或者某具体就诊类型 |
| 科室    | 为空或者某具体科室，医院、就诊类型、科室都是根据传进来的就诊进行判断的，具体值得优先级高于空值，当具体值没有满足条件得配置时才会去取空值得配置 |
| 开始时间    | 此配置适用时段的开始时间 |
| 结束时间    | 此配置适用时段的结束时间 |
| 发送方式    | 此配置适用的发送方式，适用时段和方式，多条配置都满足时则取多条 |
| 接收者类型    | `消息平台接收对象`,`科室(登录)`,`科室人员`,`科室医生`,`科室护士`,`用户`,`安全组` |
| 接收者	    | 根据接收者类型选则的具体对象 |
| 目标角色	    | 消息是想发送给哪个角色的，用户需要登录哪个角色才可以看到(需要消息类型处的`需登录科室`勾上)<br>`自动判断`如科室医生就是某科室，用户就是任意角色，安全组就是某安全组<br>`就诊科室`患者就诊科室 <br>`下医嘱科室`下医嘱科室<br>`任意角色`任意角色，即登录任何角色都可看到<br>`其它`通过指定具体科室安全组 |


### 4. 其它接口 ###

#### 4.1 获取消息内容ID接口 ####

根据消息类型、就诊、医嘱、业务ID（或`OtherInfonJson`部分值）条件取最后一条消息内容表ID，也可以根据结果是否大于0判断是否发送过消息
`HIS8.0.2`

```vb
w ##class(websys.DHCMessageInterface).FindContentId(ActionType, EpisodeId, OEOrdItemId, ObjectId,FindMaxCnt)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 如果发送消息的OtherInfoJson有BizObjId属性,请传BizObjId属性值;<br> 如果没有建议传OtherInfoJson的部分值用于确定哪条消息；<br>如果根据就诊或医嘱已经能唯一确定消息可以传空 |
| FindMaxCnt   | 最多查找数    | 大于99当99，0-99取实际值，其它当作1  支持按条件查找多条消息记录并返回`2023-06-26` |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|消息内容ID||
|-1|消息类型代码为空||
|-2|消息类型不存在||
|空|未找到消息||

#### 4.2 获取消息明细ID接口 ####

根据消息类型、就诊、医嘱、业务ID（或`OtherInfonJson`部分值）条件获取此消息最新一条消息记录，然后获取到发给此用户的消息明细记录ID
`HIS8.0.2`

```vb
w ##class(websys.DHCMessageInterface).FindDetialsId(ToUserId,ActionType, EpisodeId, OEOrdItemId, ObjectId)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ToUserId   | 用户ID    | 用户ID |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 如果发送消息的OtherInfoJson有BizObjId属性,请传BizObjId属性值;<br> 如果没有建议传OtherInfoJson的部分值用于确定哪条消息；<br>如果根据就诊或医嘱已经能唯一确定消息可以传空 |
| FindMaxCnt   | 最多查找数    | 大于99当99，0-99取实际值，其它当作1  支持按条件查找多条消息记录并返回`2023-06-26` |


|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|消息明细记录ID||
|0|未获取到|未获取到消息内容记录或者此记录没有发给此用户|

#### 4.3 获取消息回复记录接口 ####

根据消息类型、就诊、医嘱、业务ID（或`OtherInfonJson`部分值）条件获取此消息最新一条消息记录，然后获取到关于此消息的所有回复记录
`HIS8.4`

```vb
d ##class(%ResultSet).RunQuery("websys.DHCMessageInterface","QryReplyList",ActionType , EpisodeId , OEOrdItemId , ObjectId , ContentId)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 如果发送消息的OtherInfoJson有BizObjId属性,请传BizObjId属性值;<br> 如果没有建议传OtherInfoJson的部分值用于确定哪条消息；<br>如果根据就诊或医嘱已经能唯一确定消息可以传空 |
| ContentId   | 消息内容ID    | 如果已经获取到消息内容ID，前4个参数都不需要 |

|*输出列* |*说明*|*备注*|
| --- | -- | -- |
| replyUserName | 回复人姓名 |  | 
| replyDate | 回复日期 |  | 
| replyTime | 回复时间 |  | 
| replyContent | 回复内容 |  | 
| replyType | 回复类型  | A所有人、S仅发送人 | 
| isSendUser | 是否是消息发送人的回复 |  | 
| replyId | 回复记录ID |  | 
| replyUserId | 回复人ID |  | 



#### 4.4 获取某科室最近发送某类型消息记录 ####

使用的是消息类型+业务ID索引,关于最近的判断用的是索引的自然排序 (所以只适用于BizObjId为空的，或者本身满足消息发送时间序列，或者同条件下BizObjId一致)
`HIS8.4`

```vb
w ##class(websys.DHCMessageInterface).GetLatestMsgInfo(ActionType,CreateLocId)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ActionType   | 消息类型代码    |  |
| CreateLocId    | 消息发送科室ID    |  |


|*返回值* |*说明*|*备注*|
| --- | -- | -- |
| 空 | 未获取到消息记录 ||
| 不为空 | 获取到的最近发送记录的 日期^时间^消息内容ID |  |




#### 4.5 消息确认接口 ####

将消息置为一个中间态，确认状态,用于实现霸屏消息在不置为已处理的情况下解除锁屏的效果，如危急值“接收”操作后可以不再锁屏
`HIS9.0`

```vb
w ##class(websys.DHCMessageInterface).Confirm(ToUserId, ActionType, EpisodeId, OEOrdItemId, ObjectId, ConfirmUserDr, ConfirmDate, ConfirmTime,OtherParams)
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ToUserId   | 用户ID    | 传空 只是为了和处理接口参数顺序保持一致 |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 如果发送消息的OtherInfoJson有BizObjId属性,请传BizObjId属性值;<br> 如果没有建议传OtherInfoJson的部分值用于确定哪条消息；<br>如果根据就诊或医嘱已经能唯一确定消息可以传空 |
| ConfirmUserDr     | 确认用户ID      | 默认当前会话用户.  %session.Data("LOGON.USERID") |
| ConfirmDate  | 确认日期  | 默认当前日期.   +$h |
| ConfirmTime  | 确认时间  | 默认当前日期.  $p($h, ","2) |
| OtherParams | 其它扩展参数 | 用于后续参数扩展，扩展多个用^分隔 默认空  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-3|未找到消息||
|-102|消息已确认过||
|-103|消息内容Id为空||
|-107|确认人为空||
|-108^ErrorMsg|其它错误||

###### 确认方法OtherParams参数以^分隔每个位置说明 ######

| *按^分隔位置* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| 1   | 只确认哪个人员类型的消息    | 为空处理所有`(CT_CarPrvTp.CTCPT_InternalType)[NURSE,DOCTOR,Technician,Pharmacist,Other]` |



#### 4.6 设置科室消息配置 ####

设置科室的消息配置（是否禁用消息、是否禁止自动弹窗） 
`2023-03-30` `HIS9.0`

```vb
w ##class(websys.DHCMessageInterface).SetLocMsgCfg(LocId, Disabled, NoAlert )
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| LocId   | 科室ID    |  |
| Disabled   | 是否禁止显示消息    | 空不修改 1不显示消息 其它显示消息 |
| NoAlert    | 是否禁止自动弹出消息   | 空不修改 1禁止自动弹出 其它允许自动弹出  |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
| 1 | 成功 | |
| 0 |失败| |


#### 4.7 获取阅读信息接口 ####

1. 先根据消息类型和业务ID找消息记录，如果找不到再根据消息类型、就诊、医嘱ID找消息记录
2. 如果用户ID为空 则获取此消息记录中所有用户最早阅读的时间信息返回;如果用户ID不为空则获取此此消息为此用户产生的记录的阅读信息返回
`HIS9.0.1`

```vb
w ##(websys.DHCMessageInterface).GetReadInfo(ActionType, EpisodeId, OEOrdItemId, ObjectId, UserId)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 如果发送消息的OtherInfoJson有BizObjId属性,请传BizObjId属性值;<br> 如果没有建议传OtherInfoJson的部分值用于确定哪条消息；<br>如果根据就诊或医嘱已经能唯一确定消息可以传空 |
| UserId   | 用户ID    | 要查此消息具体为某用户产生的记录的阅读信息 |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
| 空 | 未获取到消息记录 | |
| 其它 | 阅读日期(格式为系统配置)^阅读时间(hh:mm:ss)^用户ID^用户姓名 | |


#### 4.8 停止定时发送任务接口 ####

1. 为产品组提供通过消息类型和业务ID来终止之前业务设置的消息定时发送任务。只会根据条件找到最后一条任务记录。
`HIS9.0.1`

```vb
w ##(websys.DHCMessageInterface).StopTask(ActionType, BizObjId, UserId)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ActionType   | 消息类型代码    | 发送定时消息时传的动作代码 |
| BizObjId    | 业务ID    | 发送消息时OtherInfoJson的BizObjId属性值 |
| UserId   | 用户ID    | 终止操作的用户ID |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
| 1 | 成功 | |
| -1^ErrorMsg | 失败 | |


#### 4.9 消息查询接口 ####

使用场景：

- 查询一段日期内消息数据
- 查询一段日期内某类型消息数据 
- 查询患者某次就诊所有消息数据
- 查询患者某次就诊某类型消息数据
- 查询某类型消息某业务ID的数据

`HIS9.0.3`

```vb
d ##class(%ResultSet).RunQuery("websys.DHCMessageContentMgr","FindByAct",pActionCodes,pDateStart,pDateEnd,pAdm,pBizObjId)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| pActionCodes   | 消息类型代码    | 多个用逗号分隔 |
| pDateStart    | 开始日期    | 就诊ID为空和业务ID也为空时 开始日期结束日期必须要有 |
| pDateEnd   | 结束日期    |  |
| pAdm   | 患者就诊ID    |  |
| pBizObjId   | 业务ID    | 如果发送消息的OtherInfoJson中的BizObjId属性 |

|*输出列* |*说明*|*备注*|
| --- | -- | -- |
| ContentId | 消息记录ID |  | 
| CActionType | 消息类型ID |  | 
| CActionTypeDesc | 消息类型名称 |  | 
| CText | 消息内容 |  | 
| CSendDate | 消息发送日期  |  | 
| CSendTime | 消息发送时间 |  | 
| CSendUser | 消息发送人 |  | 
| CFirstReadDate | 消息首次阅读日期 |  | 
| CFirstReadTime | 消息首次阅读时间 |  | 
| CFirstReadUser | 消息首次阅读人 |  | 
| CExecDate | 消息处理时日期 |  | 
| CExecTime | 消息处理时间 |  | 
| CExecUser | 消息处理人 |  | 
| CExecFlag | 消息处理标志 | Y已处理N未处理O无需处理 | 
| CStatus | 消息状态 | N正常 E已处理 C已撤销 <br> EP超期  D出院 <br> R无需处理消息 F强制处理 | 
| CSendMode | 发送方式 |  | 
| CBizObjId | 业务ID |  | 
| CPatName | 患者姓名 |  | 
| PatientID | 患者ID |  | 
| EpisodeId | 就诊ID |  | 
| OEOrdItemId | 医嘱ID |  | 


#### 4.10 用户消息列表接口 ####

用于在其它系统下获取用户消息列表数据
`HIS9.1.1`

```vb
d ##class(%ResultSet).RunQuery("websys.DHCMessageDetailsMgr","FindInfo",UserId, ReadFlag, SendDateStart, SendDateEnd , ActionTypeArg, LevelType, MarqueeShow, OtherParams ,OneDetailsId, SessStr)
```

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| UserId   | 用户ID    | ss_user.rowid 必须 |
| ReadFlag    | 处理状态标志    | N未处理或有新回复 Y已处理 必须 |
| SendDateStart   | 开始日期    | 消息发送日期开始日期 HIS配置日期格式 |
| SendDateEnd   | 结束日期    | 消息发送日期结束日期 HIS配置日期格式 |
| ActionTypeArg   | 消息类型代码    | 如果为空 不限制消息类型 |
| LevelType   | 消息重要性   | D紧急、V非常重要、I重要、G一般，为空不限制，也可传多个英文`,`分隔 |
| MarqueeShow   | 是否仅查跑马灯显示的消息    | Y是 |
| OtherParams   | 扩展参数    | 暂为内部使用，传空 |
| OneDetailsId   | 单消息明细记录ID    | 暂为内部使用，传空 |
| SessStr   | 会话字符串    | userId^locId^groupId^hospId 用户ID^科室ID^安全组ID^医院ID <br> 当传了科室、安全组等，接口会按照消息配置过滤掉目标科室、安全组不是当前科室、安全组的消息 |

<span style="color:red;">*注意：*</span> 某项目上由于历史原因，Query第9个入参是SessStr


|*输出列* |*说明*|*备注*|
| --- | -- | -- |
| DetailsId | 消息明细记录ID |  | 
| PatientId | 患者ID |  | 
| EpisodeId | 就诊ID |  | 
| OEOrdItemId | 医嘱ID |  | 
| SendUserDesc | 发送人姓名  |  | 
| Content | 消息内容 |  | 
| SendDate | 发送日期 | HIS配置日期格式 | 
| SendTime | 发送时间 | HH:mm:ss | 
| ActionCode | 消息类型代码 |  | 
| ActionDesc | 消息类型描述 |  | 
| TReadFlag | 阅读标志 | readFlag-Y已读 readFlag-N未读 | 
| OtherJson | 消息发送时的OtherJson |  | 
| ActionLevelType | 重要性 |  | 
| ActionLevelTypeDesc | 重要性描述 |  | 
| TExecFlag | 处理标志 | read-exec已读已处理、unread-exec未读已处理、<br> read-unexec已读未处理、unread-unexec未读未处理<br> read已读、unread未读 | 
| TExecLink | 处理链接 | 消息类型处配置 | 
| NewReplyCount | 新回复数量 |  | 
| TDialogStyle | 弹窗链接属性 |  | 
| BedNo | 床位 |  | 
| AdmLoc | 就诊科室名称 |  | 
| PatName | 患者姓名 |  | 
| TCellContentStyle | 内容单元格样式 |  | 
| ContentId | 消息记录ID |  |

### 5. 常见问题 ###

#### 5.1 消息变为已处理的几种方式 ####

##### 5.1.1 消息相互独立，读后自己消失不显示 #####

消息阅读后(点击消息列表的一条记录)即变为已处理(仅自己的明细记录，该消息发送给其他人的记录还是未处理)。

优点：简单

缺点：和业务状态没有关系

应用场景：仅作为消息提醒用，接收者们各自阅读各自的

相关配置：消息动作类型维护 【团队执行消息】选择为`消息相互独立,读后自己消息不显示`


##### 5.1.2 需要处理 #####

需要处理的需要在消息动作类型维护界面将相应消息类型的【团队执行消息】选择为`需要处理`


###### 5.1.2.1 Exec接口（最正统推荐的） ######

从消息明细点击须处理进业务界面或者通过系统菜单等进入业务界面，业务处理完成后，产品组调用消息处理接口处理消息

优点：业务状态和消息状态可以完美对应，可以在任何界面处理业务，业务处理后消息也会已处理

缺点：对接时要注意约定业务ID，发送和处理要能对应上，避免一个业务ID发送了多条，后期处理不掉消息。

应用场景：需要和业务状态紧密关联的，业务不处理消息一直提醒的

相关配置：消息动作类型维护 【处理(查看)链接	】需维护业务处理链接或者发送消息事OtherInfoJson.link传链接


###### 5.1.2.2 ExecAll接口 ######

根据消息明细记录ID处理消息。消息明细记录ID获取方法：发送时记录返回结果；通过消息明细点击须处理按钮时弹出业务界面会带入参数MsgDetailsId。

优点：对接简单

缺点：需要知道消息ID

应用场景：需要和业务状态紧密关联的，业务不处理消息一直提醒的，而且要能获取到消息记录ID，如处理业务只会通过消息进入的

相关配置：消息动作类型维护 【处理(查看)链接	】需维护业务链接或者发送消息事OtherInfoJson.link传链接



###### 5.1.2.3 工具按钮【执行按钮】 ######

通过配置的工具按钮【执行按钮】处理 

优点：配置即可，无需接口对接

缺点：完全由用户点击确定，和业务状态没有任何关系

应用场景：只需一个人处理的，但是处理业务又无法明确系统表述的或不好对接处理接口的，交由用户自身判断处理完成后点击按钮处理。

`up:2021-01-14`才修改为允许链接和执行按钮并存的，此前版本如果配置执行按钮则链接不再显示

相关配置：消息动作类型维护 【工具按钮】需勾选执行按钮



###### 5.1.2.4 点击按钮弹窗即处理（HIS8.5.3+） ######

点击消息根据业务处理链接生成的【须处理】按钮弹窗时即处理 `add:2022-04-07`

优点：配置即可

缺点：完全由用户点击确定，和业务状态没有任何关系

应用场景：只需一个人处理的，但是处理业务又无法明确系统表述的或不好对接处理接口的，在用户点击按钮进入相应业务界面即处理

相关配置：消息动作类型维护 【处理(查看)链接	】需维护业务链接或者发送消息事OtherInfoJson.link传链接，且【弹窗链接属性】中的execMsgOnOpen参数值维护为All或者One


###### 5.1.2.5 Cancel接口 ######

撤销与处理相似，撤销一般应用于取消申请时撤销消息，或者修改申请后先撤销消息再重新发送新消息。



###### 5.1.2.6 通用处理链接处理 ######

消息平台提供了一简单的处理界面，可以简单记录一下处理备注，当某些业务（如第三方标版拒收）不方便提供处理界面时使用。 `add:2022-03-14`

相关配置：消息动作类型维护 【处理(查看)链接	】需维护为dhc.message.commexec.csp



##### 5.1.3 自动处理 #####

自动处理为消息平台避免消息过度打扰用户的一个功能，自动处理只会将消息的状态置为已处理，不会改变业务状态，如果对应业务涉及后期处理率统计等建议不要使用，避免后期数据不好看问题。


###### 5.1.3.1 出院自动处理 ######

患者就诊状态为D的，消息在查询未处理数量时，自动将其置为已处理。

应用场景：此消息患者出院后不需要再关注，且对应业务也不需要处理了。

相关配置：消息动作类型维护【出院自动处理】勾上


###### 5.1.3.2 消息超期自动处理 ######

消息在查询未处理数量时，发现此时日期大于消息有效日期时，自动将其置为已处理。

消息有效日期为消息发送时根据当时日期+有效天数产生存入表中。 

有效天数有两处决定：消息类型维护上有有效天数，发送接口上有有效天数参数，接口参数优先级大于类型配置。

应用场景：某些具有时效性的消息通知，如发送一个放假通知，可能过了通知日期，再看也没有意义了。

相关配置：消息动作类型维护【有效天数】

