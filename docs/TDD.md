# SilverGuard 组织级 TDD

## 1. 测试目标

组织级测试设计关注的是跨仓主线是否成立：

- 账号鉴权
- 同账号设备归属
- companion 同步链路
- family API 读模型
- 应用 `desired state` / `command queue`
- 娱乐管控 actual / desired / pending
- 多端家属端交付

## 2. 测试层次

| 层次 | 当前仓库 | 现状 |
| --- | --- | --- |
| 后端 API 自动化 | `backend` | 已有 Feature 主线测试 |
| Android 设备端单测 | `companion` | 仅少量 JVM 单测 |
| Android 家属端单测 | `guardian` | 有少量 JVM 单测 |
| Web / Tauri 单测 | `frontend` | 未见 |
| 构建验证 | 4 个业务仓库 | 已有 CI |
| 跨仓端到端联调 | 全系统 | 仍以人工联调为主 |

## 3. 当前自动化覆盖矩阵

| 能力 | backend | companion | guardian | frontend |
| --- | --- | --- | --- | --- |
| auth / refresh | 有 | 无 | 间接有 | 无 |
| 设备归属 | 有 | 无 | 无 | 无 |
| sync payload 兼容 | 有 | 无 | 无 | 无 |
| family API 兼容 | 有 | 无 | 有少量 | 无 |
| 应用 desired state | 有 | 无 | 有少量 validator/fallback | 无 |
| 娱乐管控 | 有 | 无 | 无 | 无 |
| 构建产物 | 有 CI | 有 CI | 有 CI | 有 CI |

## 4. 需求到测试关注点映射

| 跨仓需求 | 推荐测试关注点 |
| --- | --- |
| 同账号设备归属 | `companion` 认领设备后，`guardian` / `frontend` 仅能看到本账号设备 |
| family 登录 | `guardian` / `frontend` 使用相同账号体系正常登录 |
| sync 主线 | `companion` 同步后，`backend` 原始查询与 family API 都可见新状态 |
| 应用停用 / 启用 | 家属端写 desired state -> companion 拉取 -> family API pending 清除 |
| 时长限制 | `SET_LIMIT` 的 desired / actual / pending 一致 |
| 安装 / 卸载 | 家属端提交 command -> companion 执行 -> 状态轮询完成 |
| 娱乐管控 | family 写 desired -> companion 落地 -> family 读 actual/desired/pending |
| 多端一致性 | `guardian` 与 `frontend` 对同一 family DTO 的解释一致 |

## 5. CI 现状

### 5.1 已有

- `backend`：PHP lint + `route:list` + PHPUnit
- `companion`：assembleRelease
- `guardian`：assembleRelease
- `frontend`：Web / Windows / Android 构建

### 5.2 当前缺口

- `companion` 单测未纳入 CI
- `guardian` 单测未纳入 CI
- `frontend` 无业务自动化测试
- 无跨仓端到端测试

## 6. 推荐回归清单

每次改动以下跨仓契约时，至少做一次端到端联调：

- `backend/routes/api.php`
- `backend/app/Http/Controllers/Api/V1/FamilyDeviceController.php`
- `backend/app/Services/DeviceAppCommandService.php`
- `backend/app/Services/DeviceEntertainmentControlService.php`
- `companion/data/remote/BackendApiService.kt`
- `companion/data/repository/BackendSyncRepository.kt`
- `guardian/data/remote/family/*`
- `guardian/data/repository/family/FamilyRemoteRepository.kt`
- `frontend/src/services/family-api.ts`
- `frontend/src/types/family.ts`

## 7. 当前测试空白

- companion `fall_events` 与 backend 的契约
- guardian / frontend 的 UI 层交互回归
- frontend 的路由、session、fallback 测试
- `backend` 原始调试接口长期权限策略
- 多端地图与降级路径

## 8. 建议补足项

- 建立一份跨仓 smoke test 手册
- 为 `guardian` 与 `frontend` 建立同构 DTO 契约测试
- 为 `frontend` 增加 session/family-api 单测
- 把 `companion` 与 `guardian` 的 JVM 单测接入 CI
- 在 `backend` 增加 stale command claim 与娱乐管控边界测试

## 9. 待确认

- companion 本地模块中哪些要纳入跨仓验证清单
