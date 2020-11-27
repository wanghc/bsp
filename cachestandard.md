## Cache代码编码规范 ##

该文档目的在于规范Cache数据库的程序开发，以方便开发人员编写程序

### 命名规范 ##

#### 函数（方法）命名 ####

+ 使用动词或动宾结构，函数名应清晰反应函数的用途与功能。

+ 当名称由多个单词组成时，单词首字母大写，且使用驼峰式命名法

+ 函数名最长不超长30个字符

  例如：`Insert`，`Delete`，`Find`，`Update`，`Save`，`Stop`，`SetName`，`GetName`，`Exec`，`SetPatName()`，`FindPatList()`，`StopOrder()`，`SendMessage()`

#### 变量命名 ####

+ 结构：名词、形容词+名词、名词+形容词

+ 变量命名要直观且可拼读，可望文知义，采用英文单词或组合，便于记忆和阅读，切忌使用汉语拼音来命名，程序中英文单词应简单常见，用词应当准确

+ 当名称由多个单词组成时，名称首字母可以大写或小写，且使用驼峰式命名法

  例如：

  >患者：PatientID，PatientName，RegNo，PatAge

  >就诊：EpisodeID，mradm，AdmLocDesc，AdmDoctor

  >医嘱：OEOrdItemID，OEOrdItemStatus，OEORIItmMastDR，OEOrdCatDR

  >医嘱项：ARCIM，ARCIMDesc，ARCIMItemCatDR

  >科室：LocID，LocDesc，LocCode，RecLoc，LocType，DeptDesc，DeptCode

  >病区与床位：WardID，WardDesc，WardCode，BedID，BedNo

  >常用后缀举例：ExecFlag，MsgCount，DrugSum，PatInfoStr , PatInfoJson，PatInfoXml，PatInfoObj，PatInfoStream，OEORIStatus

+ 固定含义词根列表
  | 变量意义     | 规定词根          | 变量举例            |
  | ------------ | ----------------- | ------------------- |
  | 患者记录     | PAPER,PAPMI       | PAPERName           |
  | 就诊记录     | PAADM,ADM,Episode | curEpisodeID        |
  | 病人医嘱记录 | OEORI             | OEORISttDate        |
  | 医嘱项记录   | ARCIM             | ARCIMDesc           |
  | 病案记录     | MRADM             | MRADM               |
  | 科室/部门    | CTLoc,Dept        | LocID,DeptDesc      |
  | 处方         | Presc             | PrescNo，PrescType  |
  | 频次         | Freq              | FreqID，FreqDesc    |
  | 库存项       | INCI              | INCIDesc            |
  | 药学项       | PHCD              |                     |
  | 单位         | UOM               | UOMDesc             |
  | 配液         | POG               |                     |
  | 用户         | User              | UserName,UserCode   |
  | 安全组       | Group             | GroupDesc           |
  | 开始         | Start             | StartDate,StartTime |
  | 结束         | End               | EndDate,EndTime     |
  
  
  
#### 类命名 ####

+ 结构：<a href="#groupList">领域名称</a>.产品线.`BL`/`DTO`/`SRV`/`COM`.名词
+ 当名称由多个单词组成时，名称首字母可以大写，且使用驼峰式命名法
+ 表达出所属产品组以及是哪种类形的抽象
+ 例如：
  | 名称                    | 描述                         | 说明                       |
  | ----------------------- | ---------------------------- | -------------------------- |
  | BSP.SYS.BL.IPList       | 系统模块的业务层IPList类     | 用于IPList维护的业务代码   |
  | BSP.SYS.DTO.IPList      | 系统模块的数据传输层IPList类 | 用于IPList维护时作数据传输 |
  | BSP.GRPHOSP.SRV.SSGroup | 多院区模块的服务层SSGroup类  | SSGroup相关服务提供        |
  | INSU.ONPAY.SRV.HBQHDA   | 医保支持服务类               |                            |
  
+ <a id="groupList" name="groupList">领域名称列表</a>
  
  |产品组 |	产品名称|	产品命名前缀|
  |--------|-------------|---------------|
  |医生工作站组	|医生工作站	|Doc|
  |计费组|门诊计费|OPBill|
  |计费组|住院计费|IPBill|
  |药库药房组|库存管理|ST|
  |护理|门诊护士站|NurOP|
  |护理|住院护士站|NurIP|
  |临床|手术麻醉系统|AN|
  |体检组|体检系统|PE|
  |电子病历|电子病历|EPR|
  |医保组|医保接口|INSU|
  |PACS/RIS组|检查医生工作站|Ris|
  |医政管理组|病案管理系统|WMR|
  |医政管理组|医院医政管理|Med|
  |病理组|病理系统|PIS|
  |检验组|LIS系统|Lis|
  |设备组|设备管理系统|EQ|
  |综合统计组|综合统计系统|WL,MR|
  |公共产品组|报表系统|CPM|
  |心电组|心电系统|EKG|
  |集成平台组|集成系统|ENS|
  |基础平台|基础平台|BSP|
