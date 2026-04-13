# SilverGuard 组织级 LLD

## 1. `.github` 仓库自身结构

```text
.github/
├─ docs/
│  ├─ PRD.md
│  ├─ HLD.md
│  ├─ LLD.md
│  └─ TDD.md
├─ profile/
│  └─ README.md
└─ README.md
```

## 2. 多仓库边界落点

### 2.1 `backend`

关键目录：

- `app/Http/Controllers/Api/V1`
- `app/Http/Middleware`
- `app/Services`
- `database/migrations`
- `routes/api.php`
- `tests/Feature/SilverGuardApiTest.php`

关键数据表：

- `companion_users`
- `devices`
- `sms_logs`
- `location_records`
- `installed_apps`
- `app_usages`
- `device_app_commands`
- `device_app_restrictions`
- `device_entertainment_settings`
- `device_entertainment_desired_states`

### 2.2 `companion`

关键目录：

- `app/src/main/kotlin/com/silverguard/companion/data`
- `app/src/main/kotlin/com/silverguard/companion/sync`
- `app/src/main/kotlin/com/silverguard/companion/worker`
- `app/src/main/kotlin/com/silverguard/companion/ui`
- `app/schemas`

### 2.3 `guardian`

关键目录：

- `app/src/guardianClient/kotlin/com/silverguard/data/remote/family`
- `app/src/guardianClient/kotlin/com/silverguard/data/repository/family`
- `app/src/guardianClient/kotlin/com/silverguard/ui/family`
- `app/src/testGuardianClient`

### 2.4 `frontend`

关键目录：

- `src/pages`
- `src/services`
- `src/router`
- `src/types`
- `src-tauri`
- `templates/android`
- `vendor` / `.vendor-src`

## 3. 统一接口与术语

### 3.1 统一术语

| 术语 | 当前含义 |
| --- | --- |
| 设备端 | `companion` |
| 家属端 | `guardian` 与 `frontend` |
| family API | `backend` 面向家属端的查询与控制接口 |
| 真实状态 | companion 实际上报状态 |
| 云端期望状态 / desired state | 家属端写入、等待 companion 落地的状态 |
| 命令队列 / command queue | 一次性动作，如安装、卸载 |
| 同账号设备归属 | `devices.owner_user_id` 决定设备可见范围 |

### 3.2 当前统一字段重点

跨仓文档需继续统一表达这些字段：

- `actualDisabled`
- `desiredDisabled`
- `disableStatePending`
- `actualRestrictionEnabled`
- `actualDailyLimitMinutes`
- `desiredRestrictionEnabled`
- `desiredDailyLimitMinutes`
- `restrictionStatePending`

## 4. 当前跨仓契约要点

### 4.1 账号模型

- companion 登录与 family 登录当前共用后端同一用户表
- family 不是独立用户体系

### 4.2 设备归属

- `companion` 首次同步时认领设备
- `backend` 用 `owner_user_id` 控制设备可见性
- `guardian` / `frontend` 只看当前账号设备

### 4.3 应用控制双模型

- `INSTALL` / `UNINSTALL`：command queue
- `ENABLE` / `DISABLE` / `SET_LIMIT`：desired state

### 4.4 娱乐管控双状态

- actual：设备端当前配置
- desired：家属端最新写入配置
- pending：两者尚未一致

## 5. CI 与交付落点

| 仓库 | workflow | 当前验证重点 |
| --- | --- | --- |
| `backend` | `.github/workflows/ci.yml` | `php-tests` + assets build |
| `companion` | `.github/workflows/android-ci.yml` | debug 验证构建 + signed release APK |
| `guardian` | `.github/workflows/android-ci.yml` | debug 验证构建 + signed release APK |
| `frontend` | `.github/workflows/ci.yml` | web build + 同 job 直推 dist 仓库 + Windows build + Android signed build |

## 6. 当前已知跨仓差异

- `companion` README 版本信息此前落后于 `VERSION`
- `frontend` README 之前把 CI 作业数写成 3，而 workflow 实际是 4
- `frontend` 桌面端名称与生成 Android 资源名称存在差异
- `companion` 已组装 `fall_events` 到 sync request，但 `backend` 当前未见消费逻辑
- `guardian` 模板里的 `GUARDIAN_API_KEY` 当前未见代码消费

## 7. 维护规则

- 先读业务仓库代码，再写 `.github` 总文档
- 组织级文档只复述已确认事实
- 无法从代码确认的信息必须写“待确认”
- 跨仓术语、字段名、叙述顺序要统一

## 8. 待确认

- `fall_events` 的长期去向
- `frontend` 中 `API_INGEST_KEY` / `GLM_API_KEY` 的定位
- 多端命名是否统一收口
