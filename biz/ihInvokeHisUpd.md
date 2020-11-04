## 互联网调用HIS诊疗更新

1. 更新标准版的`epr.frames.view.csp`

2. 修改dhc.logon.csp支持DEPARTMENTID传入

   ```vbscript
   Set DepId="" , DepCode=$g(%request.Data("DEPARTMENTCODE",1))
   if (DepCode'="") Set DepId = ##class(web.CTLoc).GetIdFromCodeOrDescription(DepCode)
   If $d(%request.Data("DEPARTMENTID")) Set DepId = $g(%request.Data("DEPARTMENTID",1))
   if ((DepId'="") && (DepId>0)) Set %request.Data("DEPARTMENT",1) = $p(^CTLOC(DepId),"^",2)
   ```
   
3. 配置第三方及视图界面
   第三方系统 ： 代码：`InternetHosp`，描述：`互联网医院` ,公司:`东华软件`
   登录代码：`IH0005`，视图：`eprframesview `，安全组配置为：`住院医生` 
   
4. 使用以下链接免密进入诊疗，不同版本进入的界面不同

   ```php+HTML
   http://10.90.11.21/imedical/web/form.htm?&PatientListPage=inpatientlist.csp&SwitchSysPat=N&TDIRECTPAGE=websys.menugroup.csp&TPSID=IH0005&USERNAME={?}&DEPARTMENTID={?}&EpisodeID={?}&MenuGroupID=46&PatientID={?}
   ```

   

