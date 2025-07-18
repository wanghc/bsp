## 信创版-开发备忘录

#### 2025-07-18

- 在5个字段上创建唯一索引

- ```sql
  CREATE UNIQUE INDEX IDX_APP_KEY_TYPE_REF_INDEXDATA
      ON BSP_PREFERENCES
      USING btree (ST_CODE, ST_ITEM_CODE, OBJECT_TYPE, OBJECT_REF, INDEX_DATA)
  ```

- 插入（前4个字段一样，第5个字段(INDEX_DATA)为NULL值）的二条记录时，`TDSQL`数据库上报唯一性约束不通过，`人大金仓`可以成功插入

- ```sql
  -- ✅正确写法，创建索引时增加WHERE条件
  DROP INDEX IF EXISTS IDX_APP_KEY_TYPE_REF_INDEXDATA;
  CREATE UNIQUE INDEX IDX_APP_KEY_TYPE_REF_INDEXDATA
      ON BSP_PREFERENCES
      USING btree (ST_CODE, ST_ITEM_CODE, OBJECT_TYPE, OBJECT_REF, INDEX_DATA)
      WHERE INDEX_DATA IS NOT NULL;
  ```

> 猜测原因：`人大金仓`数据库在字段为NULL时，不生成索引，从而索引不重复，主动增加生成索引条件来保证兼容性

#### 2025-07-11

新增【标准配置】时提示:*null value in column "ID" violates not-null constrain*，运行以下语句设置默认值

```sql
ALTER TABLE bsp_standardtype ALTER COLUMN "ID" SET DEFAULT nextval('BSP_STANDARDTYPE_ID_SEQ');
```

#### 2025-06-30

- 为兼容TDSQL数据库，约定以下修改

- 1、在`mybatis.xml`或`java`代码内的sql，`表名`二边**去掉双引号**，`字段名`二边**去掉双引号** ，字段后的as 的名称**可以加双引号**(字段二边加双引号后表示不转大小写，而TDSQL默认把字段转成大写,会导致字段不存在错误)

  ```sql
  -- ✅正确写法
  select my_Name as "myName" from my_table where my_sex_id = 1
  ```

- 2、**未承继**com.mediway.his.api.entity.BaseEntity的PO类，要的PO类中指定id属性，且一定对应大写ID字段；**如果承继了不用修改**

  ```java
  // ✅正确写法
  @TableId(value="ID",type=IdType.AUTO)
  private Long id;
  ```

- 技术说明（建表时不加双引号时,字段默认强制转成大写）
  
  ```sql
  CREATE TABLE my_table (field_name VARCHAR(255),"field_name2" varchar(255)); 
  -- 在默认大写字段名情况下，实现列名为FIELD_NAME,field_name2
  ```
  
  ```sql
  -- ✅
  SELECT field_name,"field_name2" FROM my_table; -- 肯定工作正常
  SELECT FIELD_NAME,"field_name2" FROM my_table; -- 可能工作正常(列名默认转成大写)
  -- 查询时也必须使用相同的大小写和双引号 
  ```
  
- 3、~~now()~~修改成CURRENT_TIMESTAMP

- - SQL中now()带有时区，CURRENT_TIMESTAMP不带时区，update_datetime和create_datetime都是无时区的，所以使用CURRENT_TIMESTAMP来获得当前时间戳

  ```sql
  -- ❌错误写法
  update my_table set update_datetime=now() where id=1;   -- 在tdsql下运行会报错
  ```

  修改成

  ```sql
  -- ✅ [增加与修改]语句要修改，删除与查询暂时不作要求
  update my_table set update_datetime=CURRENT_TIMESTAMP where id=1;
  ```

- 4、`select`语句查询count(`*`)，sum(`*`)，int类型字段，bigint字段时，返回列值默认为`bigdecimal`类型

- - 4.1 返回类型转成int来解决兼容性

  ```xml
  <!-- ✅ 正确写法 resultType="int" -->
  <select id='mybatisMethod' resultType="int">
      select count(*) from my_table where userid=#{userid}
  </select>
  ```

- - 4.2 查询不固定列得到map对象后，转换类型兼容性
  - 现假设my_table表有amount字段，类型为 int8

  ```xml
  <select id="getPriority" resultType="java.util.HashMap">
   SELECT amount, display_name as "caption" FROM my_table
   where activity = true and (end_date >= now() or end_date is null)
  </select>
  ```

  - java中获取`数量`值写法

    ```java
    // ❌错误写法, 兼容性差
    Long amount = myMap.get("amount");  // TDSQL下报错，因为BigDecimal类型转成Long报错
    ```

  - 修改成

  - ```java
    // ✅正确写法
    Object value = myMap.get("amount"); // TDSQL下value为BigDecimal类型,人大金仓下为Long
    Long amount = 0L;
    if (value instanceof BigDecimal) {
        amount = ((BigDecimal) value).longValueExact();
    }else{
        amount = (Long)value;
    }
    ```

  - 或

  - ```java
    // ✅正确写法
    import cn.hutool.core.convert.Convert; // 引入hutool的类
    Long amount = Convert.toLong(myMap.get("amount"),0L);
    ```

#### 2025-05-02

- sql查询优化测试

  ```sql
  -- 当test_patient表patient_id为varchar类型时
  explain analyze
      select p.name from cf_bsp_test_patient t 
      left join pa_pat_mast p on p.id=t.patient_id        -- 125ms
  
  explain analyze 
  	SELECT p.name FROM pa_pat_mast p WHERE EXISTS (    -- 70ms
      	SELECT 1 FROM cf_bsp_test_patient t WHERE p.id = t.patient_id
  	)
  explain analyze 
  	select p.name from pa_pat_mast p where p.id in ( -- 76ms
      	select t.patient_id from cf_bsp_test_patient t
  	)
  explain analyze 
  	select p.name from pa_pat_mast p where p.id in (  -- 0.099ms
       '72258','3','1','2'
  	)
  -- 强转类型再联接 或 字段类型修改成bigint后查询
  explain analyze
      select p.name from cf_bsp_test_patient t 
      left join pa_pat_mast p on p.id=CAST(t.patient_id as bigint) -- 0.09ms
  或
  -- ✅正确方式, 要连表的字段的类型与原表字段保持一致
  alter table cf_bsp_test_patient modify patient_id bigint null;  
  explain analyze
      select p.name from cf_bsp_test_patient t 
      left join pa_pat_mast p on p.id=CAST(t.patient_id as bigint) -- 0.07ms
  ```
  
  

#### 2025-04-07

- 微服务下，SQL语句中~~`::text`~~转字符串会报错，应使用TOCHAR()✅
- 不使用外键功能，在微服务下调用事务未提交，此时如果被调用方法中插入子记录时，关联字段对应记录数据库中并不能找到。

#### 2025-04-01

- 使用menuForm.EpisodeID.value属性赋值时，会提示错误，浏览器`控制台`也会抛出异常，通过`控制台`的堆栈信息，可以定义到自己的代码。修改成`websys_setMenuForm({EpisodeID:2,PatientID:1,admType:'I',PPRowId:1}); ` 这种格式即可。 `读取值功能`不受影响。

#### 2025-03-29

- 切换数据库大小写敏感方式后，like匹配是大小写敏感的，修改like功能

  如果别名中有大小写字符时，使用双引号包裹处，以防数据库把列名转成全小写

  ```sql
  -- ✅正确写法,别名使用双引号包裹
  select locId as "ctLocId" from ct_org_location
  ```
  
  如果使用模糊查询功能可以使用`ilike`功能，来忽略大小查询
  
  ```sql
  -- ✅正确写法, like修改成ilike，以便忽略大小查询
  (code ilike concat('%', #{dto.code}, '%') or description ilike concat('%',#{dto.code},'%'))
  ```

#### 2025-03-25

- 登录界面路径修改成`/imedical/his`

- 前台代码中写死路径的地方要修改

- nginx中以前`/his`的代理修改为以下代理

  ```nginx
  location /imedical/his {
  	alias D:/his-mediway-front/hisfront/static;
  	if ($request_filename ~* ^.+\.(?:html|js|css|png|jpg|gif|woff)$){
  		break;
  	}
  	if ($request_filename ~* "(.*?)(undefined)$"){
  		return 200 '{"message":""}';
  	}
  	try_files $uri $uri/ /imedical/his/hos/index.html;
  	index  index.html index.htm;
  }
  ```

#### 2025-03-01

- 查询his内数据时，`mapper`文件中**不写死**数据库schema名称`ho_his`

- feign类与controller类的路径要一致

- ```java
  // Feign内path
  @FeignClient(value="${mediway.application.emnur}",path="${server.servlet.context-path}/comem/emer/nurorder")
  //  Controller
  @RequestMapping("/comem/emer/nurorder")
  ```

#### 2025-02-27

晚间19点会议

#### 1、pom.xml中引入依赖jar时，除特殊情况外，不使用~~`<optional>`~~与~~`<scope>`~~配置

```xml
  <dependency>
  	<groupId>com.mediway.his</groupId>
  	<artifactId>xx-xxx</artifactId>
  	<version>1.0-SNAPSHOT</version>
  	<!--<optional>true</optional> 不使用-->
  	<!--<scope>provided</scope> 不使用-->        <!--compile值可以-->
  </dependency>
```

#### 2、程序中不直接注入 hos 的 service,应调用api接口，来解藕对HOS的依赖

#### 3、微服务工程中的`api`模块应该包含`Configuration`类及`spring.factories`配置文件

- ![micro-Configuration](micro.png)

#### 2025-02-26

- 说明：为统一各种操作系统下插件与医为浏览器，决定windows下也使用新版本插件及医为浏览器(内核chrome108)
- - 1、进入【常德】或【87系统】登录界面时会提示安装插件，点击下载安装即可，安装完成（建议使用常德登录界面下载，大小77M）。
- - 2、刷新登录界面会自动生成医为浏览器快捷方式到桌面上（不同项目生成的医为浏览器首页不同，医为浏览器图标与iMedical图标一样）
- - 3、使用桌面快捷方式入口进入系统



### 2024-09-27

- `gitlab`删除`addins/plugin`目录的管理，移到84服务器`/home/his/base/addins/plugin`下
- `.gitignore`文件忽略plugin目录，防止有人提交插件大文件

### 2024-09-26

- `nacos`服务器地址不再使用188的了，统一改成了http://81.70.230.87:8848/nacos

### 2024-09-24

- 因为现在测试和开发并行，现在如果开发库84上还有表结构更改和配置的变动，需要向崔工报备，并在代码提交后同步更新到测试库87上，避免代码和数据库匹配不上导致测试报错

### 2024-09-09

- 基础数据平台表名修改了一下内容
  `ct_ar_item_addinfo` -> `ct_mat_item`
  以下表没有做维护，只是迁移his里之前有的但是业务含义不明确或者不确定是否在用的字段，请用到的同事联系我确认
  `ct_oe_itmmast_addinfo` -> `ct_oe_itmmast_ext`  
  `ct_org_location` -> `ct_org_location_ext`
  `ct_org_useraccount_addinfo` -> `ct_org_useraccount_ext`
  `ct_org_healthorg_addinfo` -> `ct_org_healthorg_ext`
  `ct_rb_careprov_addinfo` -> `ct_rb_careprov_ext`
  `ct_or_operation_addinfo` -> `ct_or_operation_ext`
  `ct_mr_icddx_addinfo` -> `ct_mr_icddx_ext`
  注意事项：
  (1)医护人员的手机号，请大家优先取`hos_org_person`人员表里的mobile
  (2)是否麻醉师  请取医护人员表`ct_rb_careprov` 的 `anaesthetist_flag`
  (3)医嘱项的通用名方式获取方式联系药房药库，不要取自`ct_oe_itmmast_ext`表

### 2024-09-03

- 搭建`错误日志平台`，查看[详细说明](https://106.63.4.7:8000/his-mediway-java/his-document/-/blob/master/%E6%97%A5%E5%BF%97%E5%B9%B3%E5%8F%B0/readme.md)

### 2024-08-27

- 在`高斯`数据库中关于 `field=''`问题

- 如果表字段**非varchar**类型，高斯不允许使用`field=''`, 应该使用`field is null`

  ```sql
  select field1 from t_test where field_varchar=''   -- 可以运行
  ```

  ```sql
  select field1 from t_test where field_amount='' -- 人大金仓可以，高斯报错 field_amount为int类型
  select field1 from t_test where start_date=''   -- 人大金仓可以，高斯报错 start_date为date类型
  ```
  应使用以下方式
  ```sql
  -- ✅正确写法，非varchar字段不会存入''值
  select field1 from t_test where field_varchar is null or field_varchar=''
  select field1 from t_test where field_amount is null
  select field1 from t_test where start_date is null
  ```
  
  

### 2024-08-26

- 关于`count(*)`与`order by` 使用

  ```sql
  -- ❌兼容性差，高斯数据库下报错
  select count(*) as total from oe_ord_exec order by ex_stdatetime desc
  ```
  
  以上代码可以在`人大金仓`上运行，在`高斯`数据库下报错，**正确**的写法应该如下：
  
  ```sql
  -- ✅正确写法
  select count(*) as total from oe_ord_exec
  ```
  存在`group by`时，`count`与`order by`可以同时存在
  
  ```sql
  -- ✅正确写法
  select count(*) as total, rule_alias from bsp_cache_tables where 1=1
  group by rule_alias order by rule_alias;
  ```
  



###　2024-08-24

- 润乾服务器地址由之前的： `111.205.6.225` 修改为`111.205.100.74` , 只修改ip地址即可，其他不变



### 2024-08-18

- 关于`LISTAGG`方法兼容性问题

  ```sql
  -- ❌兼容性差，高斯数据库下报错
  SELECT LISTAGG(doc.id,",") FROM mr_emrdb0_docdata doc
  ```
  
  以上SQL在KingBase下可以执行，但在GaussDB下会报`missing WITHIN keyword`，应该使用以下兼容写法
  
  ```sql
  -- ✅正确写法
  SELECT LISTAGG(doc.id,",") WITHIN GROUP (ORDER BY doc.id ASC)
  FROM mr_emrdb0_docdata doc
  ```

### 2024-08-17

- 升级HOS-2.5.4版本前端
- 修改表中字段名称
- - 表名全小写，单词间用下划线连接
  - 列名全小写，单词间用下划线连接

### 2024-08-16

- 大家删一下本地maven仓库中的hos-app-config的包，昨天hos修改了bug

- 因为`高斯`数据库不支持~~datetime~~数据类型，所以在导入时崔工统一修改成了date，但是测试发现date转LocalDatetime会报这个错，提个脚本将数据类型转成timestamp就可以解决这个问题

  ```cmd
  cannot convert the column of type DATE to requested type java.time.LocalDateTime
  ```

### 2024-08-14

- 因为`高斯`数据库中没有current_date()函数，所以要检查sql语句中是否使用了~~current_date()~~函数，如果有都修改成current_date

  ```sql
  -- ✅正确写法 使用current_date，不使用current_date()
  where end_date>=current_date or end_date is null
  ```

  

- current_date变量取得`当天日期`，`时分秒`都为0，now()是取的当前的时间，精确到毫秒，按实际场景选择使用

### 2024-08-13

- 因为`高斯`数据库中~~日期字段名=''~~写法不被允许，需要删除相关代码

  ```sql
  -- ❌兼容性差
  start_date is null or start_date='' or start_date>=current_date    -- start_date=''兼容性差
  ```
  
  删除~~start_date=''~~，修改成
  
  ```sql
  --  ✅正确写法 删除start_date=''
  start_date is null or start_date>=current_date
  ```

### 2024-07-08

- 引入hisui方式支持

  ```html
  <script type="text/javascript" src="../../../base/scripts/hisui.js"></script>
  ```

  实际引入了所有hisui相关js及css，也引入了websys.jquery.bsp.js

### 2024-06-29

- 润乾打印支持api打印功能可进入`http://106.63.4.7:8000/his-mediway-java/his-document/-/tree/master/润乾打印`界面查看说明