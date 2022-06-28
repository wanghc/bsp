# 依赖类方法列表

### 集成视图

- 判断**医嘱**是药品用法  `手麻`

  ```php
  Set Rtn = ##class(web.DHCSTINTERFACE).GetPackFlag(OrdItemId)
  // Rtn = OM--口服药,PIVAS--静脉,OTHER--其他,
  ```

- 判断**医嘱项**是否为毒麻药品 `手麻`

  ```php
  Set ret = ##class(PHA.IN.Narc.Com).IsNarcDrugByArcim(arcim)
  // ret=Y 表示 毒麻药品
  ```

- 通过**执行记录**查找口服药品的执行情况 `手麻`

  ```php
  Set rs = ##class(%ResultSet).%New("web.DHCPHACOM.ComInterface.FaceTimeLine:OralRecords")
  Do rs.Execute(Oeore)
  // 得到药品的执行节点，时间，人物
  ```

- 通过**执行记录**查找静脉用药的执行情况 `手麻`

  ```php
  s rs = ##class(%ResultSet).%New("web.DHCPHACOM.ComInterface.FaceTimeLine:PIVASRecords")
  d rs.Execute(Oeore)
  // 得到药品的执行节点，时间，人物
  ```
- 通过**执行记录**查找批次信息 `手麻`

  ```php
  s rs=##class(PHA.FACE.OUT.Com).QueryOrderDispInclb(Orore)  
	```
  

- 获得**体检医嘱**的费用

  ```php
  Set rtn = ##class(web.DHCPE.Service.DHCPEOutInterface).GetPayInfo(OrdItemId)
  // 日期^时间^收费员
  ```

- 获得**非体检医嘱**的费用

  ```php
  Set rtn = ##class(web.DHCBillInterface).IGetOrdItmPayInfo(OrdItemId)
  // 日期^时间^收费员^金额
  ```

  


### 诊疗与病历
- 获得**某病历编目**在**科室**，**安全组**及**就诊**下的配置信息 `病历`

    ```php
    Set emrJson = ##Class(EMRservice.BIEMRService).GetCategoryStatus(CTLocID,GroupID,EpisodeID,CategoryID)
    // 返回的值为json
    // {Visible:显示与否,Sequence:顺序,Num:待书写数量}
    ```

- 判断**就诊**是流水还是留观 `新产品`

  ```php
  Set ret = ##class(web.DHCEMPatChange).GetStayStatus(EpisodeID)
  // ret=-1表示流水病人,1留观
  ```


- 获得**某病人**的过敏记录数量 `医生站`

  ```php
  s rs = ##class(%ResultSet).%New("web.DHCPAAllergy:Allergies")
  s sc = rs.Execute(PatientID)
  // 为了得到某病人过敏记录数量
  ```

- 判断**某就诊**是否入径  `医务管理`

  ```php
  s ret = ##class(DHCMA.CPW.CPS.InterfaceSrv).GetCPWStatusToDOC(EpisodeID)
  // ret = 1表示入径
  ```

  

