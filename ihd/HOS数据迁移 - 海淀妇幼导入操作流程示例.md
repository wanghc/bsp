# 海淀妇幼 — 数据迁移操作流程

| 项目 | 说明 |
|------|------|
| 适用医院 | 海淀妇幼保健院 |
| 适用对象 | 实施、运维、现场数据迁移人员 |
| 关联文档 | 《IHD数据迁移-实施操作手册》（平台通用操作见该文档） |
| 文档目的 | 按现场实测节奏，完成本院患者档案、病案号、门诊预约三类业务迁移 |

> **说明**：本文聚焦「现场执行顺序与 SQL 分片」。JDBC 配置、任务配置、字段/字典对照、界面按钮等通用操作，请先按《IHD数据迁移-实施操作手册》完成。

---

## 1. 迁移范围与预计耗时

| 序号 | 业务 | 临时表（中间表） | 临时表量级 / 并发 | 临时表预计 | 正式表策略 | 正式表预计 |
|------|------|------------------|-------------------|------------|------------|------------|
| 1 | 患者档案 | `bsp_ihd_pa_pat` | 约 150 万 / 15 进程 | 约 1.5 小时 | 每进程 10 万条，约 16 进程 | 约 8 小时 |
| 2 | 患者病案号 | `bsp_ihd_ma_mr_medicareno` | 约 44 万 / 15 进程 | 约 2 分钟 | 每进程 5 万条，约 8 进程 | 约 0.5 小时 |
| 3 | 门诊预约 | `bsp_ihd_paadm_op_appointment` | 约 8000 条 | 可忽略 | 按实际量导入即可 | 可忽略 |
| — | **合计** | — | — | — | — | **约 9～10 小时** |

**建议导入顺序**（有依赖时勿颠倒）：

1. 患者档案  
2. 患者病案号  
3. 门诊预约  

---

## 2. 导入前检查清单

开始前请逐项确认：

- [ ] K8s 中 **ihd 服务**：建议 **8 核（约 16 线程）+ 8G 内存**（与现场实测一致）  
- [ ] 关闭日志，进一步提升效率

---

## 3. 清库与清中间表

### 3.1 清除 HIS 业务数据

按院方/项目组既定方案，清除目标 HIS 库中相关**业务正式数据**。

### 3.2 检查并清空中间表

清业务数据后，检查下列中间表是否已空；**若不为空**，执行 truncate：

```sql
TRUNCATE TABLE bsp_ihd_pa_pat;
TRUNCATE TABLE bsp_ihd_ma_mr_medicareno;
TRUNCATE TABLE bsp_ihd_paadm_op_appointment;
```

可用快速核对：

```sql
SELECT 'bsp_ihd_pa_pat' AS tbl, COUNT(*) AS cnt FROM bsp_ihd_pa_pat
UNION ALL
SELECT 'bsp_ihd_ma_mr_medicareno', COUNT(*) FROM bsp_ihd_ma_mr_medicareno
UNION ALL
SELECT 'bsp_ihd_paadm_op_appointment', COUNT(*) FROM bsp_ihd_paadm_op_appointment;
```

期望结果：`cnt` 均为 `0`。

### 3.3 清除导入日志表

导入前清空进度/明细日志，避免与历史批次混淆（在 HIS 库执行）：

```sql
TRUNCATE TABLE bs_bsp_ihd_temp_log;
TRUNCATE TABLE bs_bsp_ihd_temp_itm_log;
TRUNCATE TABLE bs_bsp_ihd_formal_log;
TRUNCATE TABLE bs_bsp_ihd_formal_itm_log;
```

也可在数据迁移平台使用「清除日志」类功能（以界面实际按钮为准）。

---

## 4. 导入患者档案

### 4.1 导入临时表

| 项 | 值 |
|----|-----|
| 任务 | 患者档案（中间表 `bsp_ihd_pa_pat`） |
| 数据量 | 约 150 万 |
| 并发 | 15 个进程（界面将多段 SQL 用 `;` 分隔提交） |
| 预计耗时 | 约 1.5 小时 |

在「数据迁移平台」选择对应任务 → **导入临时表**，SQL 使用下方 **15 段**（按出生日期分片，以 `;` 分隔一次提交）：

```sql
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('0648-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1969-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1970-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1976-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1977-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1980-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1981-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1982-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1983-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1986-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1987-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1989-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1990-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1992-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1993-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1995-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1996-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('1998-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('1999-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('2002-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('2003-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('2013-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('2014-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('2016-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('2017-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('2019-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('2020-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('2024-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM pa_pat_mast_transfer WHERE EXP_STR='Y' AND BIRTH_DATETIME >= TO_DATE('2025-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND BIRTH_DATETIME <= TO_DATE('9992-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
```

**临时表验收**：

- 「临时表进度」中本批次进程均已结束  
- 失败条数为 0，或已处理完错误明细  
- `SELECT COUNT(*) FROM bsp_ihd_pa_pat;` 与源库可导出行数基本一致（允许因过滤条件略有差异）

### 4.2 导入正式表

| 项 | 值 |
|----|-----|
| 分片策略 | 按临时表 ID，约 **100000** 条 / 进程 |
| 并发 | 约 **16** 个进程 |
| 预计耗时 | 约 **8** 小时 |

操作：数据迁移平台 → 选择患者档案任务 → **导入正式表**（按 ID 范围或界面默认分片）。

**正式表验收**：

- 「正式表进度」全部结束  
- 失败明细已导出并反馈产品组处理（若有）  
- 抽查患者档案在 HIS 业务界面可查询  

---

## 5. 导入患者病案号

> 建议在患者档案**正式导入完成并通过抽查**后再导入病案号。

### 5.1 导入临时表

| 项 | 值 |
|----|-----|
| 中间表 | `bsp_ihd_ma_mr_medicareno` |
| 数据量 | 约 44 万 |
| 并发 | 7个进程 |
| 预计耗时 | 约 2 分钟 |

```sql
SELECT * FROM bsp_ihd_ma_mr_medicareno WHERE LAST_ADM_DATE <= TO_DATE('2008-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM bsp_ihd_ma_mr_medicareno WHERE LAST_ADM_DATE >= TO_DATE('2009-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND LAST_ADM_DATE <= TO_DATE('2011-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM bsp_ihd_ma_mr_medicareno WHERE LAST_ADM_DATE >= TO_DATE('2012-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND LAST_ADM_DATE <= TO_DATE('2014-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM bsp_ihd_ma_mr_medicareno WHERE LAST_ADM_DATE >= TO_DATE('2015-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND LAST_ADM_DATE <= TO_DATE('2017-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM bsp_ihd_ma_mr_medicareno WHERE LAST_ADM_DATE >= TO_DATE('2018-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND LAST_ADM_DATE <= TO_DATE('2020-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM bsp_ihd_ma_mr_medicareno WHERE LAST_ADM_DATE >= TO_DATE('2021-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss') AND LAST_ADM_DATE <= TO_DATE('2023-12-31 00:00:00','yyyy-mm-dd hh24:mi:ss');
SELECT * FROM bsp_ihd_ma_mr_medicareno WHERE LAST_ADM_DATE >= TO_DATE('2024-01-01 00:00:00','yyyy-mm-dd hh24:mi:ss');
```

> **注意**：上述 SQL 在「导入临时表」时由 IHD 通过 JDBC 在**源库**执行。请确认源库中该表/视图名称与字段 `LAST_ADM_DATE` 实际存在；若源表名与 HIS 中间表同名，以现场源库对象为准。

### 5.2 导入正式表

| 项 | 值 |
|----|-----|
| 分片策略 | 约 **50000** 条 / 进程 |
| 并发 | 约 **8** 个进程 |
| 预计耗时 | 约 **0.5** 小时 |

完成后在「正式表进度」确认全部结束，并抽查病案号业务数据。

---

## 6. 导入门诊预约记录

**注意：需要有门诊预约记录的患者档案视图，这样每次只增量导入少部分患者就行可以了，不用每次导入150W全量患者。**

| 项 | 值 |
|----|-----|
| 中间表 | `bsp_ihd_paadm_op_appointment` |
| 数据量 | 约 8000 条 |
| 预计耗时 | 可忽略（数据量小） |

操作要点：

1. 确认中间表已空（或仅本批次数据）  
2. 导入临时表（SQL 以现场任务配置/源表为准，数据量小可不强制分片）  
3. 导入正式表  
4. 抽查门诊预约记录  

---

## 7. 过程监控与异常处理

### 7.1 监控入口

| 场景 | 入口 |
|------|------|
| 临时表进度 / 失败明细 | 临时表进度界面（可查看进程号、线程名称） |
| 正式表进度 / 失败明细 | 正式表进度界面 |
| 导出错误 | 各进度页、明细页「导出」 |

### 7.2 常见异常

| 现象 | 建议处理 |
|------|----------|
| 长时间「进行中」且运行时间不再增加 | 看进程号对应线程；查应用日志是否停在「读源库」或「写临时表」；必要时「结束进程」后排查源库锁/目标库锁，再重导失败分片 |
| 字段对照错误 | 到「字段映射」补全后，清空该业务中间表相关数据再重导 |
| 数据对照失败 | 到「字典对照」补码后重导失败数据 |
| 产品组接口报错（如院区 ID） | 确认界面已选院区、导入接口地址正确；将【产品组】错误反馈对应产品组 |
| 多进程打满资源 | 确认 K8s 为约 8 核 8G；可适当减少并发分片数 |

### 7.3 重导原则

1. 先处理错误原因，再重导。  
2. 临时表重导前，确认是否需要 truncate 对应中间表，避免重复数据。  
3. 正式表重导前，与产品组确认是否允许重复写入或需先清业务数据。  

---

## 8. 总时间与排班建议

| 阶段 | 预计 |
|------|------|
| 清库 + 清中间表 + 清日志 + 环境确认 | 视现场，建议预留 0.5～1 小时 |
| 患者档案（临时 + 正式） | 约 1.5 + 8 ≈ **9.5 小时**（正式表为主） |
| 病案号（临时 + 正式） | 约 2 分钟 + 0.5 小时 |
| 门诊预约 | 可忽略 |
| **现场连续作业** | **约 9～11 小时**（含准备与验收缓冲） |

建议：患者档案正式导入安排在夜间或业务低峰；安排人员轮值查看「正式表进度」。

---

## 9. 完成后验收清单

- [ ] 三类中间表数据量与预期接近  
- [ ] 临时 / 正式进度无残留「进行中」进程  
- [ ] 关键错误明细已关闭或已登记跟踪  
- [ ] 患者档案、病案号、门诊预约在 HIS 业务侧抽查通过  
- [ ] 已通知业务方切换/验证，并保留本批次 `serialNo` / 日志备查  

---

## 附录：SQL 分片说明

- 界面导入临时表时，多段 SQL 用英文分号 `;` 分隔，后端会按段启动多个异步进程。  
- 分片条件（出生日期、末次入院日期等）用于控制单进程数据量，避免单线程过大或过不均。  
- 若某分片失败，可单独重跑该段 SQL，无需整批重来（注意中间表是否已有该段数据）。  
