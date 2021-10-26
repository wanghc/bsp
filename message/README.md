
<style>
table td:first-of-type {
    word-break:keep-all;
}
</style>

## 消息平台 ##
### 1. 发送消息接口 ###
```vb
d ##class(websys.DHCMessageInterface).Send(Context, ActionTypeCode, FromUserRowId, EpisodeId, OrdItemId, 
        ToUserRowId, OtherInfoJson, ToLocRowId, EffectiveDays , CreateLoc)
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Context        | 发送的消息内容    | 可以为空，系统会根据就诊与医嘱id生成内容                     |
| ActionTypeCode | 消息动作代码      | 如1002表示处方点评。具体值见<a href="#消息动作类型">动作类型</a>                         |
| FromUserRowId  | 发送消息的用户Id  | 如果获取不到HIS用户Id, 可以传入"^姓名"                       |
| EpisodeId      | 病人就诊Id        | 如获取不到可以为空。                                         |
| OrdItemId      | 医嘱Id            | 如获取不到可以为空                                           |
| ToUserRowId    | 接收消息的用户Id  | 可以为空。为空走配置.                                        |
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"_blank","BizObjId":1` ，其中属性均为可选项 具体值见<a href="#otherinfojson说明">OtherInfoJson说明</a>  |
|ToLocRowId     | 接收消息的科室 Id | 可以为空。<br>格式"1^2^3" 发给科室所有医护人员<br> "1^2^3\|ToNurse"发给科室所有护士<br>"1^2^3\|ToDoctor"发给科室所有医生<br>"1^2^3\|Logon"发给有此科室登录权限的所有用户<br>"1^2^3\|OnlyFlag"仅作一个标识告诉我们这个消息 是想发给哪个科室的人的，<br>具体哪些人还需要在前面ToUserRowId参数传<br>此参数主要是为了解决一个发给A科的所有人的消息（比 如会诊），<br>但是某人拥有AB两科的权限，在登录B科时 不查看 A科消息 |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|

##### 消息动作类型 #####

| 消息类型         | 代码 | 入参        | 接受对象           | 备注                                           |
| ---------------- | ---- | ----------- | ------------------ | ---------------------------------------------- |
| 通知 | 2000 | | | 消息与病人无关，请用此类型 |
| OA通知 | 2001 | | | OA通知，用户传代码，程序单独转成用户ID |
| 危急值 | 1000 | EpisodeID | 危急值平台配置 | 危急值平台通讯，响应对象的接口<br>`危急值平台` |
| 感染 | 1001 | EpisodeID | 主管医生 | `医政` |
| [查看更新类型](MSGActionType) |  |  |  |  |



##### OtherInfoJson说明 #####

| *键*         | *示例值*                   | *说明*                      |
| -----------| ----------------------- | --------------------------------------------- |
| linkParam | EpisodeId=1&ReportId=002 | 链接参数，与动作类型配置的Link合成URL，便于修改csp路径。<br>如: 把危机值1000类型对应的处理链接维护为criticalvalue.trans.csp则消息明细对应的处理URL为criticalvalue.trans.csp?EpisodeId=1&ReportId=002   |
| link      | criticalvalue.trans.csp?EpisodeId=1&ReportId=002 | 业务处理或查看明细URL，级别高过动作类型配置的link |
| dialogWidth | 1000 默认1000 | 打开处理界面时界面宽度。界面宽度为1000px 支持百分比表示占顶层宽度的百分比如80%(`HIS8.3`以后) |
| dialogHeight | 500  默认 500 | 打开处理界面时界面高度。界面高度为500px支持百分比表示占顶层宽度的百分比如50%(`HIS8.3`以后) |
| target | 默认空 | 目标窗口 如果为_blank 采用window.open新窗口方式打开，否则为顶层界面弹出hisui(easyui)模态框，内嵌iframe形式打开 |
| BizObjId | 1 | 业务系统ID，用于后续消息处理、撤销定位消息 |


### 2. 消息处理 ###

#### 2.1 消息处理接口ExecAll ####
用于已知消息明细记录ID（1.发送时记录下来，2.在消息处打开的处理界面会传入明细记录ID），来处理消息
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
```vb
w ##class(websys.DHCMessageInterface).Exec(ToUserId, ActionType, EpisodeId, OEOrdItemId, ObjectId, ExecUserDr, ExecDate, ExecTime,OtherParams)
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ToUserId   | 用户ID    | 为空处理所有人消息，不为空只处理此人消息 |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 发送消息时OtherInfoJson的BizObjId属性值，再不传BizObjId时，此参数为OtherInfoJson包含的字符串 |
| ExecUserDr     | 处理用户ID      | 默认当前会话用户.  %session.Data("LOGON.USERID") |
| ExecDate  | 处理日期  | 默认当前日期.   +$h |
| ExecTime  | 处理时间  | 默认当前日期.  $p($h, ","2) |
| OtherParams | 其它扩展参数 | 用于后续参数扩展，扩展多个用^分隔 默认空 注意此参数在8.4之后才有 |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|大于0|成功||
|-3|未找到消息||
|-102|消息已执行过||
|-103|消息内容Id为空||
|-107|执行人为空||
|-108^ErrorMsg|其它错误||

##### OtherParams以^分隔每个位置说明 #####

| *按^分隔位置* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| 1   | 只处理哪个人员类型的消息    | 为空处理所有`(CT_CarPrvTp.CTCPT_InternalType)[NURSE,DOCTOR,Technician,Pharmacist,Other]` |
| 2   | 审核拒绝标志(Y/N)    | 医呼通 需要审核通过或拒绝标志 Y通过接受 N拒绝驳回 |
| 3    | 审核备注拒绝原因    | 审核备注拒绝原因 |

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

```vb
w ##class(websys.DHCMessageInterface).Cancel(ToUserId, ActionType, EpisodeId, OEOrdItemId, ObjectId, ExecUserDr, ExecDate, ExecTime)
```


| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ToUserId   | 用户ID    | 无用参数（为方便，此方法参数设计和Exec一致） |
| ActionType   | 消息类型代码    | 发送消息时传的动作代码 |
| EpisodeId    | 病人就诊ID    | 发送消息时传的EpisodeId |
| OEOrdItemId   | 医嘱ID    | 发送消息时传的OEOrdItemId |
| ObjectId   | 业务ID    | 发送消息时OtherInfoJson的BizObjId属性值，再不传BizObjId时，此参数为OtherInfoJson包含的字符串 |
| ExecUserDr     | 处理用户ID      | 默认当前会话用户.  %session.Data("LOGON.USERID") |
| ExecDate  | 处理日期  | 默认当前日期.   +$h |
| ExecTime  | 处理时间  | 默认当前日期.  $p($h, ","2) |

|*返回值* |*说明*|*备注*|
| --- | -- | -- |
|数字|大于0表示成功||
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



