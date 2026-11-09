# 来源、致谢与实测记录

本文件的作用：**让每一处说法都能追溯到出处**，并把"别人写的"和"我自己验证的"分开。

---

## 一、原作者与上游

| 项 | 内容 |
|---|---|
| 作者 | **Xia Chen**（GitHub: [@chenxiachan](https://github.com/chenxiachan)） |
| 上游仓库 | <https://github.com/chenxiachan/thoughtdag> |
| DSH 插件在仓库中的位置 | `dsh/` 子目录（npm 元数据 `repository.directory = dsh`） |
| npm 包 | [`dsh-thoughtdag`](https://www.npmjs.com/package/dsh-thoughtdag)，本文档对应版本 `0.5.28` |
| 许可证 | **MIT**（Copyright © 2026 Xia Chen） |
| 官方文档 | <https://chenxiachan.github.io/thoughtdag/docs/zh/> |
| 插件手册 | <https://chenxiachan.github.io/thoughtdag/docs/zh/guides/deepseek-harness> |
| 发布方式 | 由 GitHub Actions 以 OIDC trusted publishing 发布，带 SLSA provenance 证明与 sigstore 签名 |

**本仓库不是上游项目的官方文档，也不镜像其源码。** 本仓库只整理中文使用说明与实测记录；所有产品设计、功能命名与行为描述的权利归原作者。

---

## 二、每篇的事实出处

| 文档 | 主要来源 |
|---|---|
| 仓库首页 `README.md` | 各插件目录的 README 与 SOURCES 汇总 |
| `thoughtdag-dsh/README.md` | 上游 `README_ZH.md`；`docs/features_ZH.md`（功能索引）；`docs/zh/guides/deepseek-harness.md`（插件手册）；npm 元数据 |
| `01-安装与配置.md` | `docs/zh/guides/deepseek-harness.md`（版本要求、desktop 配置名、24 小时保护、重启）；npm 元数据；本机实测 |
| `02-核心概念与界面.md` | `docs/zh/guides/conversations.md`（节点、侧栏、三档缩放）；`docs/zh/guides/context-control.md`（四类连线、将发送、编译规则） |
| `03-实战-编辑上下文.md` | `docs/zh/guides/context-control.md`；`docs/zh/guides/conversations.md`；`docs/zh/guides/deepseek-harness.md`（运行方式字典） |
| `04-实战-会话镜像.md` | `docs/zh/guides/session-atlas.md`；`docs/zh/guides/deepseek-harness.md`；插件包 `README.md` |
| `05-按需-历史查询.md` | 插件包 `README.md`（why 层章节）；`docs/zh/guides/deepseek-harness.md`（工具表）；包内 `cordis.patch.yml` |
| `06-排错与常见问题.md` | `docs/zh/guides/deepseek-harness.md` 的常见问题表；插件包 `README.md` 的开发说明；本机实测 |
| `07-维护-更新与卸载.md` | `docs/zh/guides/deepseek-harness.md`（更新办法）；`docs/setup_ZH.md`（连接与数据）；插件包 `README.md` |

原始链接：

- <https://raw.githubusercontent.com/chenxiachan/thoughtdag/main/README_ZH.md>
- <https://raw.githubusercontent.com/chenxiachan/thoughtdag/main/docs/features_ZH.md>
- <https://raw.githubusercontent.com/chenxiachan/thoughtdag/main/docs/setup_ZH.md>
- `docs/zh/guides/` 下：`conversations.md`、`context-control.md`、`session-atlas.md`、`deepseek-harness.md`

---

## 三、本机实测记录

**环境**

| 项 | 值 |
|---|---|
| 日期 | 2026-11-10 |
| 系统 | Windows 10/11 桌面端 |
| Harness 版本 | `0.2.1-rc.5`（`@deepseek-ai/dsh-desktop`） |
| profile | `desktop`（由 `Win32_Process` 命令行确认） |
| 安装方式 | `dsh plugin --profile desktop add dsh-thoughtdag@0.5.28` |
| 安装结果 | `+ dsh-thoughtdag 0.5.28`，12.4s，exit code 0 |

**验证过的事实（🧪）**

1. 安装包与 npm 发布包哈希一致：
   本地 lockfile 记录的 `integrity` 为
   `sha512-aO78WOE9j9Hn4Mofp6f9zkPMrmx3WYIT/DtJVOXXwg/xQ+0M4TLSQiDD/0havdtqlL8giXNlndboP286/lrVIw==`，
   与 registry 上 `dist.integrity` 完全相同（pnpm 安装时会校验哈希）。
2. 包内文件 197 个，与 registry 元数据 `fileCount: 197` 一致；全部文件修改时间集中在安装那一秒内，安装后未被改写。
3. `node_modules\dsh-thoughtdag` 为**真实目录**（`Attributes` 无 `ReparsePoint`），版本 `0.5.28`。
4. `profiles\desktop\package.json` 的 `dependencies` 记录了 `0.5.28`，`dsh.profile.bundles` 含 `dsh-thoughtdag`。
5. 安装后遍历 46 个依赖，全部在位；所有非内置 bundle 均可解析。
6. 重启方式：`CloseMainWindow()` 被托盘拦截（进程仍在），改 `Stop-Process -Force` 后 `Start-Process` 重启成功，会话保留。
7. 重启后侧栏「插件」下方出现 **ThoughtDAG** 入口，点击后画布首页正常打开（以截图确认）。
8. 宿主 web 端口对 `/`、`/health`、`/plugins/...` 一律返回 **401**，说明鉴权先于路由——因此 HTTP 探测不能作为安装判据。
9. 用户层 `profiles\desktop\cordis.patch.yml` 的修改时间早于安装时间，安装未向其中写入条目；插件自带 patch 层负责注册 `thoughtdag` 条目。

**没有验证的部分（⚠️）**

- 画布内的实际模型生成、流式回答、版本切换
- 「将发送」预览的具体数字与展开效果
- Agent 对话地图的会话导入、增量追加、链尾续接
- why 四个工具与 `/why` 命令的实际调用
- 桌面版专用的"在 App / 终端打开""一键接入"等入口

这些内容在文档中均标注为 📄（文档描述），照做时请以你的实机为准。

---

## 四、商标与免责

- "DeepSeek"、"DeepSeek Harness"、"DSH" 相关名称与标志归其权利人所有；本文档仅作描述性使用。
- "ThoughtDAG" 名称与标志归原作者 Xia Chen 所有。
- 本仓库内容为**使用说明**，不构成对插件功能、稳定性或安全性的担保。安装第三方插件涉及在你机器上执行其代码，风险由使用者自行判断。
- 如发现本文档与上游描述不符，请以**上游原文与实际行为**为准，并欢迎提 Issue 修正。
