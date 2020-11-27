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
| OtherInfoJson  | 其它信息          |  可以为空。格式为json<br> `"link":"xx.csp",linkParam:"EpisodeId=1&ReportId=002"`,<br>`"dialogWidth":1000,"dialogHeight":500,`<br>`"target":"blank","BizObjId":1` ，其中属性均为可选项  |
|ToLocRowId     | 接收消息的科室 Id | 可以为空。<br>格式"1^2^3" 发给科室所有医护人员<br> "1^2^3\|ToNurse"发给科室所有护士<br>"1^2^3\|ToDoctor"发给科室所有医生<br>"1^2^3\|Logon"发给有此科室登录权限的所有用户<br>"1^2^3\|OnlyFlag"仅作一个标识告诉我们这个消息 是想发给哪个科室的人的，<br>具体哪些人还需要在前面ToUserRowId参数传<br>此参数主要是为了解决一个发给A科的所有人的消息（比 如会诊），<br>但是某人拥有AB两科的权限，在登录B科时 不查看 A科消息 |
| EffectiveDays  | 消息有效天数      | 可以为空。此有效天数级别高于动作类型所配置                   |
| CreateLoc      | 发送者科室        | 可以为空。传HIS中科室Id，可传“＾科室描述”                    |
|*返回值* |*说明*|*备注*|
|数字|大于0表示成功||
|-100^ErrorMsg|表示失败|如:-100^动作类型不存在|

消息动作类型

| 消息类型         | 代码 | 入参        | 接受对象           | 备注                                           |
| ---------------- | ---- | ----------- | ------------------ | ---------------------------------------------- |
| 通知             | 2000 |             |                    | 消息与病人无关，请用此类型                     |
| OA通知           | 2001 |             |                    | OA通知，用户传代码，程序单独转成用户ID         |
| 危急值           | 1000 | EpisodeID   | 危急值平台配置     | 危急值平台通讯，响应对象的接口<br>`危急值平台` |
| 感染             | 1001 | EpisodeID   | 主管医生           | `医政`                                         |
| 处方点评(事后)   | 1002 | EpisodeID   | 主管医生           | `药房`                                         |
| 处方审核(事前)   | 1003 | OEOrdItemID | 主管医生           | `检验`                                         |
| 检验报告取消审核 | 1004 | EpisodeID   | 医疗单元、主管医生 | `检验`                                         |
|                  |      |             |                    |                                                |
|                  |      |             |                    |                                                |
|                  |      |             |                    | :sparkles:                                     |

