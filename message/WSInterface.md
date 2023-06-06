
### webservice接口 ###

#### wsdl地址 ####
https://127.0.0.1/imedical/web/BSP.MSG.SRV.WSInterface.cls?wsdl=1

#### 方法Call ####
统一的调用方法入口，通过FuncCode参数区分不同功能

| *参数名* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| FuncCode  | 功能代码    |  见功能列表      |
| input |  实际参数      | xml格式，节点随功能而变              |

#### 功能列表 ####

| *功能代码* | *说明*      | 
| -------------- | ----------------- | 
| Send  | <a href="#发送消息">发送消息</a>    |
| Exec |  <a href="#处理消息">处理消息</a>      |
| Cancel |  <a href="#撤销消息">撤销消息</a>      |

##### 发送消息 #####
FuncCode=Send

###### input节点说明 ######

| *节点* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ActionTypeCode  | 消息动作类型代码    |  代码参见[动作类型清单](MSGActionType)      |
| BizObjId |  业务ID      | 如申请单号，申请记录ID，报告ID等              |
| Context |  消息内容      | 消息内容              |
| FromUserCode |  发送人工号      | 发送人在HIS内工号              |
| FromUserName |  发送人姓名      | 发送人工号和姓名至少一个不为空              |
| CreateLocCode |  发送人科室代码      | 可以为空             |
| CreateLocDesc |  发送人科室名称      | 可以为空              |
| EpisodeId |  就诊ID      | 如果业务有尽量传，可以为空            |
| OrdItemId |  医嘱ID      | 如果业务有尽量传，可以为空             |
| EffectiveDays |  有效天数      | 超过有效天数自动置为已处理             |
| ReceiverList |  接收对象列表      | 见<a href="#接收对象列表元素节点说明">接收对象列表元素节点说明</a>            |
| BizLink |  业务处理链接      | 此参数应为完整链接，传了此参数，则BizLinkParam不生效            |
| BizLinkParam |  业务处理链接参数   | 如EpisodeId=1&b=2 和消息类型配置处的链接组合成完整链接              |
| BizLinkWidth |  链接窗口宽度      |    如1200          |
| BizLinkHeight |  链接窗口高度      |     如500         |
| BizLinkTarget |  链接目标窗口      | `_blank`表示新窗口打开，其他为`hisui`模态框             |


接收对象列表元素节点说明
| *节点* | *说明*      | 
| -------------- | ----------------- | 
| RecType  | 接收者类型    | 
| RecCode |  接收者代码      | 

| *RecType取值* | *说明*      | *RecCode取值*                      |
| -------------- | ----------------- | ------------------------------- |
| L  | 科室用户    |  科室代码      |
| LCP  | 科室人员    |  科室代码      |
| LCPD  | 科室医生    |  科室代码      |
| LCPN  | 科室护士    |  科室代码      |
| G  | 安全组用户    |  安全组名称      |
| U  | 用户    |  用户工号      |


###### input示例 ######

```xml
<MsgSend>
    <ActionTypeCode>2000</ActionTypeCode>
    <BizObjId>5</BizObjId>
    <Context>测试消息</Context>
    <FromUserCode>demo</FromUserCode>
    <FromUserName></FromUserName>
    <CreateLocCode></CreateLocCode>
    <CreateLocDesc></CreateLocDesc>
    <EpisodeId>1</EpisodeId>
    <OrdItemId></OrdItemId>
    <EffectiveDays></EffectiveDays>
    <ReceiverList>
        <Receiver>
            <RecType>U</RecType>
            <RecCode>demo^ys01^ys02</RecCode>
        </Receiver>
        <Receiver>
            <RecType>G</RecType>
            <RecCode>Demo Group</RecCode>
        </Receiver>
        <Receiver>
            <RecType>L</RecType>
            <RecCode>ZYHL003</RecCode>
        </Receiver>
    </ReceiverList>
    <BizLink>https://baidu.com?a=1</BizLink>
    <BizLinkParam></BizLinkParam>
    <BizLinkWidth></BizLinkWidth>
    <BizLinkHeight></BizLinkHeight>
    <BizLinkTarget>_blank</BizLinkTarget>
</MsgSend>
```


###### 返回值示例 ######
```xml
<Response>
    <Body>
        <ResultCode>1</ResultCode>
        <ResultContent>12555</ResultContent>
    </Body>
    <Header></Header>
</Response>
```



##### 处理消息 #####
FuncCode=Exec

###### input节点说明 ######

| *节点* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ActionTypeCode  | 消息动作类型代码    |  代码参见[动作类型清单](MSGActionType)      |
| BizObjId |  业务ID      | 如申请单号，申请记录ID，报告ID等，应和发送对应              |
| ExecUserCode |  处理人工号      | 处理人在HIS内工号              |
| ExecDate |  处理日期      | yyyyy-MM-dd 空默认取当前日期             |
| ExecTime |  处理时间      | hh:mm:ss 空默认取当前时间             |
| OnlyExecUserCode |  只处理某个用户的消息      | 适用于一条消息同时发送给多个人，只处理其中某一个人的消息               |
| OnlyExecUserType |  只处理某类用户的消息      | 适用于一条消息同时发送给多个人，只处理其中某类用户的消息 如DOCTOR医生 NURSE护士           |


###### input示例 ######

```xml
<MsgExec>
    <ActionTypeCode>2000</ActionTypeCode>
    <BizObjId>5</BizObjId>
    <ExecUserCode>demo</ExecUserCode>
    <ExecDate></ExecDate>
    <ExecTime></ExecTime>
    <OnlyExecUserCode></OnlyExecUserCode>
    <OnlyExecUserType></OnlyExecUserType>
</MsgExec>
```


###### 返回值示例 ######
```xml
<Response>
    <Body>
        <ResultCode>1</ResultCode>
        <ResultContent>1975</ResultContent>
    </Body>
    <Header></Header>
</Response>
```



##### 撤销消息 #####
FuncCode=Cancel

###### input节点说明 ######

| *节点* | *说明*      | *备注*                                                 |
| -------------- | ----------------- | ------------------------------------------------------------ |
| ActionTypeCode  | 消息动作类型代码    |  代码参见[动作类型清单](MSGActionType)      |
| BizObjId |  业务ID      | 如申请单号，申请记录ID，报告ID等，应和发送对应              |
| CancelUserCode |  撤销人工号      | 撤销人在HIS内工号              |
| CancelDate |  撤销日期      | yyyyy-MM-dd 空默认取当前日期             |
| CancelTime |  撤销时间      | hh:mm:ss 空默认取当前时间             |


###### input示例 ######

```xml
<MsgCancel>
    <ActionTypeCode>2000</ActionTypeCode>
    <BizObjId>5</BizObjId>
    <CancelUserCode>demo</CancelUserCode>
    <CancelDate>2022-01-01</CancelDate>
    <CancelTime>12:01:01</CancelTime>
</MsgCancel>
```


###### 返回值示例 ######
```xml
<Response>
    <Body>
        <ResultCode>-1</ResultCode>
        <ResultContent>消息状态不可撤回</ResultContent>
    </Body>
    <Header></Header>
</Response>
```