# Computer Use (Windows) for Antigravity

为 **Google Antigravity** 打造的仿 OpenAI Codex **Computer Use** 桌面操作插件。无需手动点开独立桌面控制程序，直接在 Antigravity 对话框内让 AI 观察并操控 Windows 原生桌面应用。

---

## 核心功能

- **对话框直接驱动桌面应用（类 Codex 工具模型）**
  - `computer_apps`：列出当前桌面窗口、查看活动窗口、强制置顶聚焦目标软件（满足 Windows Active Desktop 要求）或启动新应用。
  - `computer_inspect`：通过 Windows UI Automation（UIA）自动提取界面上的按钮、输入框、菜单与标签页，并在截图上叠加 **Set-of-Mark（SoM）编号标注**，大幅提高点击准确率。
  - `computer_use`：按顺序批量执行 `move`、`click`、`double_click`、`triple_click`、`drag`、`scroll`、`type`、`keypress`、`wait`、`screenshot`，并在执行后自动回传最新画面供 AI 校验。
  - `computer_settings`：内嵌交互式设置面板（支持对话内联、辅助侧栏 `aux_pane` 与全局侧栏入口）。

- **蓝绿色（Teal）四阶段实时视觉反馈**
  操作期间在物理屏幕上显示可鼠标穿透、不抢焦点的透明提示层（向大模型回传截图时会自动瞬时隐藏，不会污染 AI 视觉判断）：
  1. **AI 操控电脑界面提示**（操作前预警，默认 `1.5s`）：显示顶部胶囊、倒计时进度条与虚线“目标”准星，留出反应时间。
  2. **开始操作**（执行中）：鼠标形态临时切换为高对比蓝绿色 AI 指针/文本光标，沿平滑轨迹滑动并显示光晕尾迹与点击扩散波纹。
  3. **结束操作**（操作后保持，默认 `4s`）：切换为整秒倒计时与进度条（`4s → 3s → 2s → 1s`），清晰告知操作已完成。
  4. **界面消失**：倒计时结束自动淡出覆盖层，并立即还原系统默认鼠标指针。

- **Codex 风格安全护栏与白名单**
  - 通过 `PreToolUse` Hook（[hooks.json](hooks.json)）在工具执行前拦截检查：
  - **Control 总开关**：可一键开启或暂停所有桌面控制权限。
  - **Always-allowed apps（白名单）**：白名单内应用（默认含 `notepad.exe`、`calc.exe`、`CalculatorApp.exe`、`ApplicationFrameHost.exe`、`mspaint.exe`）直接放行；未列入的应用首次操作会触发 Antigravity 原生审批弹窗。
  - **Blocked apps（受保护应用黑名单）**：默认拦截 `1Password.exe`、`Bitwarden.exe`、`KeePass.exe`、`Taskmgr.exe`、`regedit.exe` 等敏感进程。
  - **敏感快捷键确认 & 物理急停**：发送 `Alt+F4`、`Win+R`、`Win+X`、`Ctrl+Alt+Del` 前二次确认；任何时候将鼠标移至屏幕**左上角 `(0, 0)`** 均可立即触发 `FAILSAFE_ABORT` 急停并还原鼠标指针。

- **零手动安装依赖**
  插件发布包内置已打包的 MCP Server 与界面资源（`dist/`），底层复用 Windows 自带 `.NET Framework 4.x` (`csc.exe`) 自动编译本地驱动，最终用户**无需安装 Python、也无需运行 `npm install` 或 `pip install`**。

---

## 系统要求

- **操作系统**：Windows 10 / Windows 11（64 位，系统自带 `.NET Framework 4.x`）
- **运行环境**：已安装 **Node.js >= 18**（且命令行可执行 `node`）
- **宿主软件**：Google Antigravity

---

## 安装方式

### 方式一：下载 Release 压缩包一键安装（推荐普通用户）

1. 下载发布包 `antigravity-computer-use-win-v0.1.0.zip` 并解压到任意目录（例如解压后得到 `computer-use\` 文件夹）。
2. 在解压后的 `computer-use` 目录中打开 PowerShell，运行一键安装脚本：

```powershell
powershell -ExecutionPolicy Bypass -File .\install.ps1
```

> 默认会全局安装到 `%USERPROFILE%\.gemini\config\plugins\computer-use`。
> 如果只想在**单个项目/工作区**中启用，可指定 `-Scope Workspace`：
> ```powershell
> powershell -ExecutionPolicy Bypass -File .\install.ps1 -Scope Workspace -WorkspacePath "D:\你的项目目录"
> ```

### 方式二：使用 Antigravity CLI（`agy`）直接安装

如果你本机终端已配置 `agy` 命令，可直接从 GitHub 仓库或本地目录安装：

```bash
# 从 GitHub 仓库安装
agy plugin install https://github.com/<你的用户名>/<仓库名>

# 或在源码目录本地安装
agy plugin install .
```

---

## 启用与快速上手

1. 安装完成后，打开 **Antigravity**，进入左侧边栏 **Customizations → Plugins**，确认 **Computer Use (Windows)** 处于开启状态（新开对话或等待下一轮对话即可生效）。
2. 在对话框中直接用自然语言调用：
   - **打开设置面板**：`打开 Computer Use 设置面板`
     - 可在界面中一键开关总控制、切换审批模式、点击 **Scan open windows** 快速把运行中的软件加入 `Always-allowed apps`、调节操作前预警与操作后保持时长，或点击 **Capture preview** 测试截屏。
   - **查看桌面窗口与控件**：`查看当前打开的桌面窗口` / `检查当前前景窗口的界面控件`
   - **自动化操作软件**：`打开记事本，帮我写一段今日待办清单并截屏确认`

---

## 设置项说明

在 `Computer Use` 设置面板中可随时调整以下参数（改动自动持久化保存，无需重启）：

| 设置项 | 说明 | 默认值 |
| :--- | :--- | :--- |
| **Any App Control** | Computer Use 总开关 | 开启 |
| **Control Scope** | 控制范围：`Any App (with prompts)` 或 `Always-Allowed Apps Only` | `Any App (with prompts)` |
| **Approval Mode** | 审批模式：`Ask for untrusted apps` / `Always ask` / `Auto-allow` | `Ask for untrusted apps` |
| **Always-allowed apps** | 免审批白名单进程列表 | `notepad.exe` 等 |
| **Blocked / Protected apps** | 禁止截屏与操作的受保护进程列表 | 密码管理器、任务管理器等 |
| **Coordinate Space** | 坐标空间：`Normalized (0–1000)` / `Image Pixels` / `Screen Pixels` | `Normalized (0–1000)` |
| **Capture Scope** | 截屏范围：当前活动窗口所在屏 / 主屏 / 全部显示器 | `Active Window Monitor` |
| **UI Element Tags (SoM)** | Set-of-Mark 控件编号叠加策略 | `Auto` |
| **Live Desktop Visual HUD** | 蓝绿色发光边框、顶部状态胶囊与点击波纹 | 开启 |
| **Agent Cursor Appearance Morph** | 操作期间自动切换为蓝绿色 AI 鼠标指针并平滑移动 | 开启 |
| **Pre-Action Lead-In Countdown** | 操作前预警倒计时与目标准星提示时长 | `1.5s` (`2s` 提示) |
| **Post-Action Hold Duration** | 操作结束后保留提示框与倒计时时长 | `4s` |
| **Top-Left Corner Failsafe** | 鼠标移至屏幕左上角 `(0, 0)` 立即中断操作 | 开启 |
| **Confirm Dangerous Shortcuts** | 拦截 `Alt+F4`、`Win+R`、`Win+X`、`Ctrl+Alt+Del` 并弹窗确认 | 开启 |

---

## 注意事项与排障

1. **目标软件需在前景可见桌面（Active Desktop）**：与 Codex Windows 版一致，目标应用需处于当前可见的 Windows 桌面，不可锁屏或在隐藏的远程桌面会话中运行。
2. **输入法兼容性**：文本输入底层采用 Win32 Unicode `SendInput`，即使系统处于中文拼音输入法状态也能直接写入中英文与标点。
3. **如何紧急中止**：操作前预警或操作进行中，只需将鼠标快速移至屏幕**左上角 `(0, 0)`** 即可立刻终止执行并恢复默认光标。
4. **如果在命令终端沙箱内测试**：部分 IDE/Agent 内置终端沙箱运行在隔离桌面下，无法截取真实桌面画面；请直接在 Antigravity 对话框中调用，或在系统普通 PowerShell 窗口中验证。

---

## 卸载方式

在插件源码或解压目录运行：

```powershell
powershell -ExecutionPolicy Bypass -File .\uninstall.ps1
```

如需同时删除保存的设置与截图缓存，追加 `-PurgeData`：

```powershell
powershell -ExecutionPolicy Bypass -File .\uninstall.ps1 -PurgeData
```

---

## 开发者构建与打包（仅二次开发需要）

```powershell
npm install
npm run build     # 打包 dist/server.mjs、dist/hook.mjs、dist/app.html
npm test          # 校验清单、Hook 安全策略与 MCP JSON-RPC 接口
npm run package   # 构建、测试并输出 release/antigravity-computer-use-win-v*.zip
```

> 发布到 GitHub 时建议保留 `dist/` 目录一并提交，这样用户通过 `agy plugin install <仓库地址>` 安装时即可免构建直接运行。
