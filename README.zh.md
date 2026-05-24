# ai-stats

> 一款终端仪表盘，用于追踪各 AI 编程工具的使用统计。

[English](README.md)

聚合 **Claude Code**、**OpenCode** 和 **Codex CLI** 的会话数、Token 用量、缓存效率、费用估算及扩展清单，一屏呈现。无需账号，不上报遥测，仅读取本地数据文件。

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
             AI CODING TOOLS STATISTICS
                  Generated 2025-05-24 14:32
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 OVERVIEW

  Total Sessions:      1,284
  Total Tokens:    9,471,032

🔧 TOOL BREAKDOWN

  Tool            Sessions               Tokens                 Share
  ──────────────────────────────────────────────────────────────────
  ◆ Claude Code        842          7,821,004  ████████████████░░░░  82%
  ◈ OpenCode           312          1,241,887  ████░░░░░░░░░░░░░░░░  13%
  ◇ Codex CLI          130            408,141  █░░░░░░░░░░░░░░░░░░░   4%
```


![](./screenshot.png)

---

## 支持的工具

| 工具 | 数据来源 | 格式 |
|------|----------|------|
| [Claude Code](https://claude.ai/code) | `~/.claude/stats-cache.json` | JSON |
| [OpenCode](https://github.com/sst/opencode) | `~/.local/share/opencode/opencode.db` | SQLite |
| [Codex CLI](https://github.com/openai/codex) | `~/.codex/state_5.sqlite` | SQLite |

仅显示存在数据文件的工具，未安装的工具会被静默跳过。

---

## 依赖要求

| 依赖 | 是否必须 | 用途 |
|------|----------|------|
| Bash 3.x+ | 是 | 脚本运行时 |
| `sqlite3` | 是 | 读取 OpenCode 和 Codex 数据库 |
| `jq` | 否 | Claude Code 的按模型明细、日活数据和 MCP 服务器列表 |

安装依赖：

```bash
# macOS
brew install sqlite jq

# Arch Linux
sudo pacman -S sqlite jq

# Debian / Ubuntu
sudo apt install sqlite3 jq

# Fedora / RHEL
sudo dnf install sqlite jq
```

---

## 安装

```bash
# 下载脚本
curl -O https://raw.githubusercontent.com/yourname/ai-stats/main/ai-stats

# 添加执行权限
chmod +x ai-stats

# 运行
./ai-stats
```

或者放到 `$PATH` 中全局使用：

```bash
mv ai-stats /usr/local/bin/ai-stats
ai-stats
```

---

## 使用方法

```
ai-stats [命令] [选项]
```

### 命令

| 命令 | 缩写 | 说明 |
|------|------|------|
| `dashboard` | `d` | 完整概览 + 所有工具详情（默认） |
| `claude` | `c` | 仅显示 Claude Code 统计 |
| `opencode` | `o` | 仅显示 OpenCode 统计 |
| `codex` | `x` | 仅显示 Codex CLI 统计 |
| `compare` | `cmp` | 多工具横向对比 |
| `export` | `e` | 导出所有数据为 JSON |
| `help` | `h` | 显示帮助信息 |

### 选项

| 选项 | 说明 |
|------|------|
| `-o, --output FILE` | 指定 `export` 命令的输出路径 |
| `--no-color` | 禁用 ANSI 颜色输出（适合管道或日志场景） |
| `--width NUM` | 手动指定终端宽度 |

### 示例

```bash
# 默认仪表盘（所有工具）
ai-stats

# 仅查看 Claude Code
ai-stats claude

# 横向对比所有工具
ai-stats compare

# 导出带时间戳的统计文件
ai-stats export

# 导出到指定路径
ai-stats export -o ~/reports/ai-usage.json

# 禁用颜色（便于管道处理）
ai-stats --no-color claude | tee claude-stats.txt
```

---

## 各视图说明

### 仪表盘（Dashboard）

汇总所有已检测到的工具：总会话数、总 Token 数，以及各工具 Token 占比的比例条形图。

### Claude Code（`claude`）

- 会话数与消息数
- 输入 / 输出 Token 总量
- 缓存读取与写入 Token
- 按模型分组的 Token 明细（需要 `jq`）
- 近 7 天活动：每日会话数、消息数、工具调用次数（需要 `jq`）
- `claude_desktop_config.json` 中配置的 MCP 服务器（需要 `jq`）
- 已安装插件及每个插件的 Skill 数量

### OpenCode（`opencode`）

- 会话数与消息数
- 输入 / 输出 / 推理 Token 总量
- 缓存读取与写入 Token
- 费用估算
- 按模型分组的 Token 和会话明细
- 近 7 天活动：每日会话数、Token 数、费用
- `opencode.json` 中配置的 MCP 服务器与 Skill 路径

### Codex CLI（`codex`）

- 总线程数、活跃线程数和已归档线程数
- 总 Token 用量
- 来源分布（CLI vs VS Code）
- 按模型分组的 Token 明细
- 近 7 天活动：每日线程数、Token 数
- 已启用插件及每个插件的 MCP 服务器

### 横向对比（`compare`）

一张表展示所有工具的会话数、输入 Token、输出 Token 和缓存命中数。

### 导出（`export`）

生成一个包含所有可用工具原始数据的 JSON 文件：

```json
{
  "generated_at": "2025-05-24T06:32:00Z",
  "tools": {
    "claude_code": { ... },
    "opencode": { ... },
    "codex": { ... }
  }
}
```

---

## 数据来源

脚本**仅读取本地文件**，不发起任何网络请求。

| 工具 | 文件路径 | 备注 |
|------|----------|------|
| Claude Code 统计 | `~/.claude/stats-cache.json` | 由 Claude Code 自动写入 |
| Claude MCP 配置 | `~/Library/Application Support/Claude/claude_desktop_config.json` | macOS |
| Claude MCP 配置 | `~/.config/Claude/claude_desktop_config.json` | Linux（XDG） |
| Claude 插件列表 | `~/.claude/plugins/installed_plugins.json` | 插件注册表 |
| OpenCode 数据库 | `~/.local/share/opencode/opencode.db` | SQLite，由 OpenCode 写入 |
| OpenCode 配置 | `~/.config/opencode/opencode.json` | MCP 与 Skill 配置 |
| Codex 数据库 | `~/.codex/state_5.sqlite` | SQLite，由 Codex CLI 写入 |
| Codex 配置 | `~/.codex/config.toml` | 插件配置 |

---

## 添加新工具

脚本采用简单的注册表模式，添加新工具只需以下步骤：

1. **注册工具** — 在脚本顶部的四个数组中追加条目：

   ```bash
   TOOL_NAMES=("claude" "opencode" "codex" "mytool")
   TOOL_DISPLAYS=("Claude Code" "OpenCode" "Codex CLI" "My Tool")
   TOOL_PATHS=("~/.claude/stats-cache.json" "..." "..." "~/.mytool/data.db")
   TOOL_TYPES=("json" "sqlite" "sqlite" "sqlite")
   ```

2. **实现统计函数** — 按照 `{tool_name}_stats` 命名规范编写：

   ```bash
   mytool_stats() {
       local db_path
       db_path=$(expand_path "~/.mytool/data.db")
       # 查询并打印统计数据
   }
   ```

3. **实现日活函数** — `{tool_name}_daily` 用于近 7 天活动视图。

4. **实现扩展函数** — `{tool_name}_extensions` 用于展示 MCP 服务器和插件信息。

5. **实现展示函数** — `show_mytool` 调用 `tool_banner`，再依次调用以上三个函数。

6. **接入主流程** — 在 `show_dashboard` 和 `main` 的 `case` 语句中添加对应分支。

---

## 兼容性

- macOS 13+（主要目标平台，已测试）
- Linux（完整支持 — Arch、Debian、Ubuntu、Fedora 及同类发行版）
- 兼容 Bash 3.2+ — 未使用关联数组或 `mapfile`
- 数字格式化有纯 bash 回退方案，不依赖 `numfmt`
- 未安装 `jq` 时，Claude Code JSON 通过 `grep`/`awk` 解析（仅会话数和 Token 总量；按模型明细需要 `jq`）

---

## 许可证

MIT
