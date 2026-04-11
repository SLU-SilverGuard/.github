# SilverGuard 组织级 PRD

## 1. 文档目标

本文从组织级视角说明 SilverGuard 多仓库体系当前要解决的问题、真实职责分工、已打通的业务主线、非目标、依赖与风险。内容以工作区内 4 个业务仓库与 `.github` 仓库现状为准。

## 2. 系统定位

SilverGuard 不是单一应用，而是一组协同仓库：

- `backend`：Laravel 服务端中枢
- `companion`：Android 设备端
- `guardian`：Android 家属端
- `frontend`：Web / Windows / Tauri Android 家属端
- `.github`：组织级总览与文档导航

## 3. 要解决的问题

### 3.1 同账号设备归属

系统当前的主线不是“知道设备 ID 就能查看”，而是：

- 设备由 `companion` 登录并同步后归属到账号
- `guardian` / `frontend` 登录后只看到同账号设备
- `backend` 负责阻止不同账号争抢同一 `deviceId`

### 3.2 设备真实状态与云端期望状态并存

远程控制不能只看“命令发了没”，还要表达：

- 设备真实状态
- 云端期望状态
- 是否仍在等待下一次同步落地

当前跨仓主线里最典型的是应用管理与娱乐管控。

### 3.3 家属端需要多平台，而设备端必须保持 Android 原生能力

当前产品拆成两条家属端产品线：

- `guardian`：原生 Android 家属端
- `frontend`：Web / Windows / Tauri Android 家属端

设备端仍保留 Android 原生的强权限与本地守护能力。

## 4. 当前已打通的业务主线

### 4.1 companion -> backend -> guardian / frontend

当前跨仓主线可直接从代码确认的能力包括：

- companion 注册、登录、刷新 token
- family 登录、刷新 token
- 设备归属绑定与隔离
- 短信、位置、应用、应用使用、权限层级同步
- family 设备列表、消息、位置、应用查询
- 应用 `desired state`
- 应用命令队列
- 娱乐管控 actual / desired 状态

### 4.2 companion 本地但尚未完整进入跨仓主线的能力

`companion` 当前还包含：

- 陪伴聊天 / 故事 / 纪念日
- 健康
- 跌倒检测
- 报告与历史

这些能力并不都已进入 `backend` family API 主线。比如 `fall_events` 已在 companion sync request DTO 中出现，但当前后端代码未见对应消费能力。

## 5. 仓库职责矩阵

| 仓库 | 当前职责 | 运行形态 | 主要调用对象 |
| --- | --- | --- | --- |
| `backend` | 账号鉴权、设备归属、同步写入、family API、应用与娱乐管控云端状态 | PHP API 服务 | `companion`、`guardian`、`frontend` |
| `companion` | 本地守护、权限层级检测、自动同步、执行云端控制 | Android 原生 App | `backend` |
| `guardian` | Android 家属端设备查看与远程管理 | Android 原生 App | `backend` |
| `frontend` | Web / Windows / Tauri Android 家属端 | Web + Tauri | `backend` |
| `.github` | 组织主页与总文档 | GitHub 特殊仓库 | 维护者与外部访问者 |

## 6. 非目标

当前多仓库体系不以以下目标为前提：

- 单仓完成全部产品能力
- family 独立账号体系
- WebSocket / 推送式实时同步
- companion 本地全部能力都已经进入家属端主线
- 手写长期维护的 `frontend` Android 工程

## 7. 关键依赖

- `backend` 的 family API 稳定性
- `companion` 的同步频率与本地能力层级
- Android 权限 / Device Owner / Dhizuku
- Web / Windows / Android 各自构建链
- AMap key
- 各仓库独立 CI 与签名配置

## 8. 统一术语

本文档与各仓库文档统一使用以下术语：

- 设备端：`companion`
- 家属端：`guardian` 与 `frontend`
- 同账号设备归属：`devices.owner_user_id` 驱动的设备可见性规则
- family API：面向家属端的设备查询与控制 API
- 真实状态：设备端实际上报到后端的状态
- 云端期望状态 / desired state：家属端写入、等待设备端同步落地的状态
- 命令队列 / command queue：一次性动作，如安装、卸载
- 同步链路：设备端采集并上报到后端，再由家属端读取

## 9. 当前风险

- 部分 README 与实际代码现状不一致，需要持续校对
- companion 有本地能力未完全进入跨仓主线，容易被误写成“系统已打通”
- CI 覆盖程度在不同仓库差异明显，尤其 `frontend` 目前主要依赖构建验证
- 多端命名、图标同步、环境变量的实际用途仍有若干待确认项

## 10. 后续演进方向

- 继续收口跨仓统一术语
- 补齐各仓库 PRD/HLD/LLD/TDD
- 逐步把跨仓关键链路纳入自动化验证
- 明确 companion 本地模块哪些进入家属端主线，哪些保持本地能力

## 11. 待确认

- `fall_events` 是否进入后端主线
- `frontend` 中 `API_INGEST_KEY`、`GLM_API_KEY` 的长期定位
- `guardian` 的 `GUARDIAN_API_KEY` 是否仍需保留
