# 学习通课程自动化完成

一个可安装到 AI Agent 的技能：通过 **Windows MCP 控制 Edge**，按当前页面依次处理学习通 / 超星课程的视频、课件和章节习题，并核实任务点完成状态。

核心流程保持精简，异常时才读取排障参考；优先使用局部截图，减少重复提取页面和重复加载上下文。**尚未进行 token 消耗或运行速度的性能基准测试。**

## 使用前准备

- Windows 电脑和 Microsoft Edge。
- 支持 `SKILL.md` 的 AI Agent。
- Agent 已连接可用的 Windows MCP；**安装本技能不会自动安装或连接 MCP**。
- 已由用户登录的课程页面，以及明确的课程和操作范围。

## 下载和安装

- [下载最新技能安装包](https://github.com/2974357638/chaoxing-course-completion/releases/latest/download/chaoxing-course-completion.zip)
- [备用：下载 main 分支源码 ZIP](https://github.com/2974357638/chaoxing-course-completion/archive/refs/heads/main.zip)

解压后，将**整个 `chaoxing-course-completion` 文件夹**放入你的 Agent 实际读取的技能目录。源码 ZIP 的解压目录可能带有 `-main` 后缀，请先重命名。最终层级应为：

```text
<Agent 技能目录>/
└── chaoxing-course-completion/
    ├── SKILL.md
    ├── agents/openai.yaml
    └── references/windows-mcp.md
```

不要多嵌套一层同名文件夹，也不要只复制 `SKILL.md`。

### Codex / ChatGPT 桌面版目录

本项目验证的桌面环境从 `~/.codex/skills/` 读取个人技能；Windows 中通常对应 `%USERPROFILE%\.codex\skills\`。当前[官方文档](https://learn.chatgpt.com/docs/build-skills)列出的用户级目录为 `~/.agents/skills/`。不同版本和运行环境可能采用不同位置，**请以你的 Agent 实际读取的目录为准**，不要仅凭文件复制成功判断已被识别。

项目级 `.agents/skills/` 只在对应项目范围内生效。希望在普通聊天或其他项目中使用时，应安装到实际生效的个人技能目录。

安装后新建聊天，在技能选择器中确认名称；若列表未刷新，完全退出应用后重新打开。

### 可选：用 Git 安装

以下 PowerShell 示例适用于**已确认使用 `~/.codex/skills/` 的环境**，需要预先安装 Git。若目标文件夹已存在，命令会停止，不覆盖已有技能。

```powershell
$skillRoot = Join-Path $env:USERPROFILE '.codex\skills'
$skillTarget = Join-Path $skillRoot 'chaoxing-course-completion'
if (Test-Path -LiteralPath $skillTarget) {
    throw '目标文件夹已存在，请先检查已有技能。'
}
New-Item -ItemType Directory -Path $skillRoot -Force | Out-Null
git clone --branch main https://github.com/2974357638/chaoxing-course-completion.git $skillTarget
```

## 调用

在 ChatGPT 桌面界面输入 `@`，搜索并选中 **学习通课程自动化完成**，然后说明课程和操作范围，例如：

> 使用学习通课程自动化完成，通过 Windows MCP 检查 Edge 当前课程，继续未完成的视频任务点。

在支持 `$` 技能调用的 Codex CLI / IDE 中，可输入：

```text
使用 $chaoxing-course-completion 检查当前 Edge 课程，并在我指定的范围内依次推进。
```

## 行为与限制

- 通过正常页面 UI 操作，使用播放器实际提供的倍速选项；以明确完成标记核实结果。
- 不伪造学习记录，不使用脚本注入或后台接口修改课程进度。
- 用户接管电脑或要求停止时让出控制；仅阅读、安装技能不会自动接管浏览器。
- 网站布局、完成条件和 Windows MCP 工具定义可能变化，需要依据当前页面调整；不保证所有课程都采用相同规则。
- 本仓库不包含课程账号、Cookie、个人学习记录或课程内容。

## 许可证

[MIT License](LICENSE)。
