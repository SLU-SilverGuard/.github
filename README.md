# SilverGuard `.github`

这个仓库是 SilverGuard 的组织级总览仓库，用于维护：

- 组织主页文案
- 多仓库总文档
- 维护者导航入口

## 当前结构

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

## 文档导航

- [docs/PRD.md](docs/PRD.md)
- [docs/HLD.md](docs/HLD.md)
- [docs/LLD.md](docs/LLD.md)
- [docs/TDD.md](docs/TDD.md)
- [profile/README.md](profile/README.md)

## 仓库矩阵

| 仓库 | 定位 | 当前交付物 |
| --- | --- | --- |
| `backend` | Laravel 服务端中枢 | API 服务代码 |
| `companion` | Android 设备端 | release APK |
| `guardian` | Android 家属端 | release APK |
| `frontend` | Web / Windows / Tauri Android 家属端 | web dist / MSI / EXE / Android APK |
| `.github` | 组织主页与总文档 | 文档与 profile |

## 统一术语

- 设备端：`companion`
- 家属端：`guardian` 与 `frontend`
- family API：后端面向家属端的查询与控制接口
- 同账号设备归属：由 `devices.owner_user_id` 决定设备可见范围
- 真实状态：设备端实际上报到后端的状态
- 云端期望状态 / desired state：家属端写入、等待设备端落地的状态
- 命令队列 / command queue：一次性动作，如安装、卸载

## 维护约定

- 先读各业务仓库代码，再更新这里
- 这里只写跨仓已确认事实
- 无法从代码确认的信息统一标记“待确认”
- 业务仓库文档更新后，这里必须同步更新职责矩阵、主线描述与风险项

## 当前重点

- 保持 4 个业务仓库的术语一致
- 保持 family API、设备归属、应用控制双模型、娱乐管控叙述一致
- 把组织主页文案与维护文档区分开：`profile/README.md` 偏对外，根 `README.md` 偏维护
