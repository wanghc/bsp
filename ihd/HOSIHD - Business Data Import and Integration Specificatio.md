# IHD 产品组业务数据导入对接规范

| 项目 | 说明 |
|------|------|
| 适用对象 | 各产品组研发、架构、实施 |
| 文档目的 | 统一正式表导入对接方式，**防止循环依赖**，指导业务数据迁移接入 |
| 核心原则 | **IHD 通过前端配置的 REST 地址调用产品组；产品组不要在 api 层引用 `hisihd-api` / 继承 Feign** |
| 关联代码 | `IhdDataSaveClient`、`IhdSaveRequest`、`ImportDataServiceImpl#sendJsonRequest` |
| 编写背景 | 病案号等迁移场景中出现 `mrm-api` 依赖 `hisihd-api`、Feign 继承 `IhdDataSaveClient` 引发依赖方向争议 |

---

## 1. 正确依赖方向（必读）

### 1.1 推荐链路

```text
源库 / Excel
    ↓
IHD 迁移服务（hisihd）读临时表、做字段/字典对照
    ↓  HTTP REST（业务配置里的 importClass URL）
产品组业务服务（如 mrm / opar / ipar / 医生站）落正式表
    ↓
产品组返回每条成功/失败（id / status / errorMsg）
    ↓
IHD 记正式导入进度与失败明细日志
（临时表 status / new_id：现状由产品组更新，见 §3.4）
```

### 1.2 依赖方向红线

| 允许 | 禁止 |
|------|------|
| IHD **core-biz** 用 `RestTemplate` 调产品组 **Controller HTTP** | 产品组 **`xxx-api`** 依赖 **`hisihd-api`** |
| 产品组 **biz / controller** 实现与前台一致的导入落库逻辑 | 产品组 Feign 接口 **`extends IhdDataSaveClient`** |
| 产品组按需引用 **DTO 约定**（或自建等价 JSON 结构） | api 依赖 api、形成编译期环或「迁移工具被业务反向依赖」 |
| IHD 配置产品组完整 REST URL | 指望 IHD 编译期注入各产品组 Feign Client |

**一句话：**  
迁移工具应调用业务；**不应**让业务模块（尤其 api）反向依赖迁移工具的 Feign 契约。

### 1.3 为何禁止 Feign 继承 `IhdDataSaveClient`

历史写法类似：

```java
@FeignClient(name = "mrm-mediway-server", path = "...")
public interface BspIhdInPatmrnoClient extends IhdDataSaveClient<BspIhdMaMrInpatmrnoPO> {
    @PostMapping("save")
    BaseResponse<List<...>> save(@RequestBody IhdSaveRequest<...> request);
}
```

问题：

1. 产品组 `xxx-api` 的 pom 会引入 `hisihd-api` / `hisihd-biz-model` → **依赖倒置**（业务依赖迁移工具）。
2. 多产品组都这样做时，`hisihd-api` 被大量业务模块编译依赖，耦合面爆炸（runner 依赖树中可见）。
3. IHD 正式导入实际走的是 **`RestTemplate` + 配置 URL**，并不需要这些 Feign；Feign 继承是多余且有害的。
4. `IhdDataSaveClient` 上的 `@FeignClient` / `@PostMapping` 已注释，**本身就不是给产品组继承用的运行契约**。

`IhdDataSaveClient` 仅可视为「方法签名示意」，**不要作为产品组 api 的父接口**。

---

## 2. 标准对接方式：前端配置 REST

### 2.1 在 IHD 业务配置中填写

正式导入时，IHD 读取业务配置：

| 配置项 | 含义 | 示例 |
|--------|------|------|
| `importClass` | 产品组 **完整 HTTP URL**（POST） | `http://{网关或服务}/xxx/context/mrm/api/trans/ihd/save` |

实施在 IHD 前台「业务配置」中维护，**改地址不必改 IHD 代码、不必让产品组发 Feign 包给 IHD**。

### 2.2 IHD 侧实际调用方式

```text
ImportDataServiceImpl
  → 组包 JSON（configObj + ihdMiddleTablePos）
  → restTemplateIhd.exchange(importClass, POST, ...)
  → 解析 BaseResponse<List<BaseResponseTempDTO>>
```

请求头携带：`Content-Type: application/json`，`access-token: <当前令牌>`。

---

## 3. 产品组需要提供什么

### 3.1 提供 Controller（与前台同逻辑）

推荐做法（医生站等已验证路径）：

- 提供 **HTTP Controller**，逻辑与业务前台导入/保存一致（或复用同一 Service）。
- **不要**为 IHD 单独再维护一套「仅 Feign 可用」的 api 继承链。
- 若历史上 api 里已有 `extends IhdDataSaveClient`：**删除继承**，改为普通 Controller；api 模块 **去掉对 `hisihd-api` 的依赖**。

示例形态（示意）：

```java
@RestController
@RequestMapping("/mrm/api/trans/ihd")  // 与配置到 IHD 的 URL 路径一致
public class XxxIhdImportController {

    @PostMapping("/save")
    public BaseResponse<List<BaseResponseTempDTO>> save(@RequestBody Map<String, Object> body) {
        // 1. 解析 configObj、ihdMiddleTablePos
        // 2. 落正式表（与前台相同 Service）
        // 3. 按条回写结果：id / status / errorMsg
        // 4. 中间表 status 由 IHD 根据返回结果处理，或按双方约定由产品组更新——见 3.3
        return BaseResponse.success(resultList);
    }
}
```

### 3.2 请求体约定（与 `IhdSaveRequest` 对齐）

IHD 发出的 JSON 结构等价于：

```json
{
  "configObj": {
    "业务参数code": "业务参数value"
  },
  "ihdMiddleTablePos": [
    {
      "id": 123,
      "temp_id": 123,
      "...中间表字段（已字典对照后的值）...": "..."
    }
  ]
}
```

说明：

- `configObj`：来自 IHD「业务参数配置」，产品组用于默认科室、默认类型等。
- `ihdMiddleTablePos`：一批中间表行；含主键 `id`（回写结果时必须能对应）。
- 字段名以中间表 / 对照后驼峰或下划线为准，**双方联调时固定样例 JSON**。

参考类型（仅作契约说明，**产品组勿强制依赖 jar**）：

- `com.mediway.his.ihd.biz.model.dto.IhdSaveRequest`
- 中间表基类字段：`newId`、`status`（`1` 成功 / 非 `1` 未成功）等见 `IhdMiddleTablePO`

### 3.3 响应体约定（IHD 强依赖）

IHD 按如下结构解析：

```json
{
  "success": true,
  "code": "200",
  "msg": "ok",
  "data": [
    {
      "id": "123",
      "status": "1",
      "errorMsg": ""
    },
    {
      "id": "124",
      "status": "0",
      "errorMsg": "证件号已存在"
    }
  ]
}
```

| 字段 | 要求 |
|------|------|
| 外层 `BaseResponse` | `success=true` 表示本批接口可解析；业务行失败放在 `data` 行内 |
| `data[].id` | 对应中间表行 id，**必填** |
| `data[].status` | `"1"` = 该行导入成功；其它 = 失败 |
| `data[].errorMsg` | 失败原因，便于 IHD 写正式导入明细 |

参考：`BaseResponseTempDTO`（`id` / `status` / `errorMsg`）。

### 3.4 中间表 status 由谁更新（现状 vs 目标）

**现状（以当前 `ImportDataServiceImpl#saveDataByFormal` 为准）：**

产品组 HTTP 返回后，IHD **只做**：

- 统计成功/失败条数
- 失败行写入正式导入明细日志 `bs_bsp_ihd_formal_itm_log`

**不会**根据返回结果去 `UPDATE` 临时表的 `status` / `new_id`。

因此现场能把 `status` 置为 `'1'`、避免重复导入的，实际是 **产品组在落库成功时自行更新中间表**（这也是部分组曾想引用 `hisihd-api` 的原因之一）。

| 步骤 | 现状责任方 |
|------|------------|
| 读临时表、对照、调产品组 | **IHD** |
| 写正式业务表 | **产品组** |
| 更新临时表 `status` / `new_id` | **产品组** |
| 正式导入主表/明细日志 | **IHD** |

**目标约定（规范推荐，待 IHD 补齐回写后切换）：**

| 步骤 | 目标责任方 |
|------|------------|
| 读临时表、对照、调产品组 | **IHD** |
| 写正式业务表 | **产品组** |
| 根据返回 `data[].id/status` 更新临时表 `status`/`new_id`、记正式导入日志 | **IHD** |

切换前：产品组可继续更新中间表，但 **不要** 为此让 `xxx-api` 依赖 `hisihd-api`；直接用本库 SQL/`JdbcTemplate` 更新约定中间表即可。  
切换后：产品组只返回结果，中间表状态统一由 IHD 回写。

---

## 4. 产品组落地检查清单

接入一个新业务正式导入前，产品组自检：

- [ ] 已提供 **POST Controller**，URL 可配置进 IHD `importClass`
- [ ] 请求/响应符合第 3 节约定，并用 Postman/前台同等包体联调通过
- [ ] **`xxx-api` 的 pom 未依赖 `hisihd-api`**
- [ ] 无 `extends IhdDataSaveClient` 的 Feign

---

## 5. 反例与正例

### 5.1 反例（禁止）

```text
mrm-api
  ├── depends on hisihd-api
  ├── Feign: XxxClient extends IhdDataSaveClient
  └── 声称「给 IHD 导入用」

问题：依赖方向反了；且 IHD 根本不走该 Feign。
```

### 5.2 正例（推荐）

```text
mrm-biz / mrm Controller
  └── POST /.../ihd/save   （与前台同 Service）

hisihd 业务配置
  └── importClass = https://网关/.../ihd/save

hisihd-core-biz
  └── RestTemplate POST → 上述 URL
```

