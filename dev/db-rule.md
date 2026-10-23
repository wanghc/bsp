### 一.去掉冗余的参数及属性

###### 1.增加字段去掉默认值NULL;

```sql
-- ❌ NULL冗余写法
CREATE TABLE ph_base_message_log (
    ....
    ward_desc VARCHAR(255) NULL,  
	....
);
-- ❌ 冗余写法
ALTER TABLE his.cf_ma_mr_location ADD is_op_co boolean DEFAULT false NULL;
-- ✅ 简洁
ALTER TABLE his.cf_ma_mr_location ADD is_op_co boolean DEFAULT false;

-- ❌ 冗余写法
ALTER TABLE his.ma_cpw_ocp_run_reason MODIFY main_dr NULL;	
-- ✅ 简洁
ALTER TABLE his.ma_cpw_ocp_run_reason MODIFY main_dr;	
```

###### 2.建索引去掉USING BTREE;

```sql
-- ❌ 冗余写法
CREATE INDEX index_ph_in_barcode_rela ON ph_in_barcode_rela USING BTREE (biz_type,biz_pointer);
-- ✅ 正确写法
CREATE INDEX index_ph_in_barcode_rela ON ph_in_barcode_rela (biz_type,biz_pointer);
```

###### 3.建唯一索引去掉NULLS DISTINCT

```sql
-- ❌ 冗余写法
CREATE UNIQUE INDEX cf_oe_emsd_oper_hosp_unique ON sm_his.cf_oe_emsd_oper_hosp USING btree (hosp_dr, oper_dr) NULLS DISTINCT;
-- ✅ 正确写法
CREATE UNIQUE INDEX cf_oe_emsd_oper_hosp_unique ON sm_his.cf_oe_emsd_oper_hosp USING btree (hosp_dr, oper_dr);
```

###### 4.去掉表空间参数 TABLESPACE sys_default

###### 5.创建序列只保留必须参数，其余采用默认值即可

```sql
-- ❌ 冗余写法
CREATE SEQUENCE sm_his.ar_insu_sa_recon_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;
-- ✅ 正确写法
CREATE SEQUENCE sm_his.ph_base_inst_upload_task_id_seq
    START WITH 1
    INCREMENT BY 1;
-- ✅ 正确写法    
CREATE SEQUENCE sm_his.ph_base_inst_upload_task_id_seq;
```

### 二.数据类型兼容性问题

###### 1.自增列兼容性问题,部分数据库不支持自增列，bigserial 、serial4混用

```sql
-- ❌ 错误写法 - BIGSERIAL
CREATE TABLE ph_base_instruction_upload_task (
    id BIGSERIAL PRIMARY KEY,
	....
);
```

建议：
自增列id主键采用序列默认值的方式

```sql
-- ✅ 正确写法  
CREATE SEQUENCE sm_his.ph_base_inst_upload_task_id_seq
    START WITH 1
    INCREMENT BY 1
    CACHE 5;
CREATE TABLE sm_his.ph_base_inst_upload_task (
	id bigint DEFAULT nextval('ph_base_inst_upload_task_id_seq') PRIMARY KEY,
	...
	);
```

###### 2.ID列数据类型应采用bigint或int

bigint范围:约 -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807 （约 ±9.22e18）
int范围：-2,147,483,648 到 2,147,483,647（约 ±21.4 亿）
若数据量巨大时选择bigint

###### 3.字符类型使用char、varchar

不要使用bpchar,character varying等
char是定长，varchar使用时应指定长度

###### 4.布尔类型用boolean,不要用bool

###### 5.选择合适的日期类型、及默认值

###### 6.字段类型不要用int1 int2 int4 int8 tinyint，应该使用int,bigint,smallint

### 三.表设计相关

###### 1.新建的表/视图/序列/函数等的语句后面，要增加一下设置对象owner的语句

```sql
alter table 表名 owner to 用户名；
alter view 视图名 owner to 用户名；
alter sequence 序列名 owner to 用户名；
alter function 函数名(参数，....) owner to 用户名；
```

###### 2.varchar必须定义长度

```sql
-- ❌ 错误写法
CREATE TABLE sm_hai.ma_hai_crf_report (
      ...
      record_title varchar NULL, -- 记录标题
);
-- ✅ 正确写法  
CREATE TABLE sm_hai.ma_hai_crf_report (
      ...
      record_title varchar(255), -- 记录标题
);
```

###### 3.整型建议使用int(integer)、bigint、smallint

###### 4.默认当前日期，应选择通用函数

建议：`current_timestamp`、`sysdate`、`current_date`，不要用~~pg_systimestamp()~~或~~sys_systimestamp()~~

###### 5.避免字段名使用保留字或关键字

字段名禁用的关键字：`level`、`log`，`order`, `group`, `user`, `level`, `type`, `status`, `key`, `value`, `desc`, `index`, `select`,  `insert`,  `update`,  `delete`, `create`, `drop`, `alter`, `table`, `view`, `schema`,  `database`,  `primary`, `foreign`, `constraint`, `unique`,  `check`,  `default`,  `not`,  `null`,`and`,`or`,`like`, `between`, `in`, `is`, `exists`,`case`, `when`,  `then`, `else`, `end`, `from`,  `where`,  `set`,`join`,`union`,`having`, `limit`,  `offset`, `asc`,  `into`, `values`,`char`,`varchar`,`int`,`integer`, `bigint`,`decimal`,`numeric`, `boolean`,`text`,`date`,`time`,`timestamp`, `datetime`,`year`,`cast`, `convert`,`extract`,`substring`,`name`,`path`, `size`,`format`,`mode`,`token`,`role`,`admin`,`public`, `session`,`transaction`,`function`,`procedure`,`trigger`,`sequence`,`package`,`domain`,`data`,`object`,`class`,`rowid`,`trxid`,`online`,`offline`等

6.模式与表名不要用`""`包裹

`表名`二边**去掉双引号**，`字段名`二边**去掉双引号** ，字段后的as 的名称**可以加双引号**(字段二边加双引号后表示不转大小写，而TDSQL默认把字段转成大写,会导致字段不存在错误)

```sql
-- ✅正确写法
select my_Name as "myName" from my_table where my_sex_id = 1
```



### 四.选择语法兼容性较好的写法

###### 1.增加多个字段

```sql
-- ❌ 错误写法
ALTER TABLE ph_ipca_medical_rounds
    ADD   urgency VARCHAR(16),
    ADD   expected_round_date DATE,
    ADD   round_focus VARCHAR(500); 
```

```sql
-- ✅ 正确写法  
ALTER TABLE ph_ipca_medical_rounds
ADD (
    urgency VARCHAR(16),
    expected_round_date DATE,
    round_focus VARCHAR(500)
);
```



### 五.其他

###### 1.新建/删除索引建议加CONCURRENTLY参数，无阻塞生成索引

生产环境的大表建索引，通常应优先使用，业务表建索引要CONCURRENTLY参数，不阻塞正常业务操作

```sql
-- ✅ 正确写法，生产环境的大表建索引
CREATE INDEX CONCURRENTLY 模式名.索引名
ON 模式名.表名(字段1，....)
```

###### 2.insert/update/delete建议显式commit;

###### 3.主键约束不要使用自定义命名，建议列级定义primary key

```sql
--  ❌ 错误写法
CREATE TABLE sm_hai.ma_hai_crf_report (
      id serial4 NOT NULL, 
	  ......
      CONSTRAINT con_ho_hai_ma_hai_crf_report_constraint_1 PRIMARY KEY (id)
  );
```

```sql
-- ✅ 正确写法，生产环境的大表建索引
CREATE TABLE sm_hai.my_table (
      id int DEFAULT nextval('my_table_id_seq') PRIMARY KEY,
	  .....
  );
```

###### 4.不用图形化工具生成`ddl`,建议使用`DBMS_METADATA.GET_DDL`生成

```sql
-- ✅ 正确写法
SELECT DBMS_METADATA.GET_DDL('TABLE', 'my_table');
```

###### 5.核心表的高危操作(drop\alter ... drop),建议先备后删,  update/delete, 

###### 6.业务表遵循设计规范 

表/字段都应有`comment`，每一个sql操作单元应该写注释，备注清楚用途及影响

###### 7.`drop`操作改成`alter ... rename to...`最后单独登记`drop`重名或的对象相关`sql`

8。`DATE_FORMAT`是`mysql`语法函数，应该使用通用的函数`to_char`或`extract`
