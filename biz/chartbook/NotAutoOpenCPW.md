## 【诊疗与病历】是否自动打开【临床路径】修改

1. 下载<a href='websys.DHCChartStyle.xml' download="websys.DHCChartStyle.xml">websys.DHCChartStyle.xml</a>文件，导入DHC-APP名字空间下的Studio.

2. 写配置父表

   ```sql
   select * from websys.StandardType where code='websys'
   --上面查询如果没有记录，则插入记录
   INSERT INTO websys.StandardType (code) VALUES('websys')
   ```
   
4. 插入配置子表

   ```sql
   select * from websys.StandardTypeItem where parref='websys'
   -- 上面查询结果中如果没有NotAutoOpenCPW，则插入记录
INSERT INTO websys.StandardTypeItem (parref,code,description,storedvalue) VALUES('websys','NotAutoOpenCPW',"不自动打开临床路径界面",1)
   ```
   
   

