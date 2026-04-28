# 用 MCP 让 AI 助手直接操作 Obsidian

今天花了半天时间，把 Obsidian 接入了我的 AI 工作流。现在 AI 助手可以直接读写我的笔记库，不用再手动复制粘贴了。

## 为什么要这么做

我有一套 AI Agent 系统在后台运行：
- **探索者**：每天早上 7 点自动收集资讯
- **写手**：根据探索者的报告生成内容初稿

之前这些产出都存在 `~/.openclaw/workspace/outputs/` 里，用命令行查看。但这样有几个问题：

1. **不直观**：纯文本，没有双链、图谱这些笔记工具的优势
2. **不好同步**：想在手机上看得折腾半天
3. **AI 操作不便**：每次都要写完整路径

Obsidian + MCP 的组合解决了这些问题。

## MCP 是什么

MCP（Model Context Protocol）是 Anthropic 推出的协议，让 AI 模型能够调用外部工具。简单说就是给 AI 装插件。

我用的是 [openclaw](https://github.com/openclaw/openclaw)，它支持通过 MCP 扩展能力。这次用的 Obsidian MCP Server 是 `@jianruidutong/obsidian-mcp`，提供了 25 个操作笔记的工具。

## 配置步骤

### 1. 创建 Vault

首先在一个正常路径创建 Vault（不要用隐藏目录）：

```bash
mkdir -p ~/Documents/ObsidianVault
```

为什么强调"正常路径"？因为我最初想用 `~/.openclaw/workspace/outputs/`，结果踩了坑——很多 MCP Server 出于安全考虑会拒绝访问隐藏目录（以 `.` 开头的路径）。

### 2. 配置 MCP Server

在 `~/.openclaw/config/mcporter.json` 里添加：

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx -y @jianruidutong/obsidian-mcp",
      "env": {
        "OBSIDIAN_VAULT_PATH": "/Users/你的用户名/Documents/ObsidianVault"
      }
    }
  }
}
```

重启 OpenClaw 后就能用了。

### 3. 验证安装

```bash
# 列出所有笔记
mcporter call obsidian.list_notes

# 读取某个笔记
mcporter call obsidian.read_note path=README.md

# 创建笔记
mcporter call obsidian.create_note path=test.md content="Hello World"
```

## 插件配置

光能读写还不够，Obsidian 的精髓在于插件生态。我装了这几个：

### Dataview（必装）

用 SQL-like 语法查询笔记。比如统计本周写了多少篇文章：

```dataview
TABLE file.ctime as "创建时间"
FROM "writer"
WHERE file.ctime >= date(today) - dur(7 days)
SORT file.ctime DESC
```

安装：设置 → 第三方插件 → 社区插件市场 → 搜索 "Dataview"

### Calendar

日历视图看每日笔记，很直观。

### Git

自动备份到 GitHub。配置：

```
自动备份间隔：10 分钟
自动拉取间隔：10 分钟
提交信息：vault backup: {{date}}
```

### Remotely Save

多端同步。我用的坚果云 WebDAV：

```
服务类型：WebDAV
地址：https://dav.jianguoyun.com/dav/
用户名：你的坚果云账号
密码：应用专用密码（在坚果云后台生成）
```

## 目录结构设计

```
ObsidianVault/
├── _templates/           # 模板
│   ├── 小红书模板.md
│   ├── 公众号模板.md
│   └── 探索报告模板.md
├── explorer/             # 探索者产出
│   └── 2026-02-10.md
├── writer/               # 写手产出
│   ├── xiaohongshu/
│   ├── gongzhonghao/
│   └── blog/
├── x-articles/           # X/Twitter 文章分析
└── Dashboard.md          # 数据看板
```

## Dashboard 示例

用 Dataview 做了一个数据看板：

```markdown
## 📊 文章统计

```dataview
TABLE length(rows) as "数量"
FROM "writer" OR "explorer"
GROUP BY file.folder
```

## 📝 最近更新

```dataview
TABLE file.mtime as "更新时间"
SORT file.mtime DESC
LIMIT 10
```
```

打开这个文件就能看到实时统计。

## 踩坑记录

### 坑 1: 隐藏目录被拒绝

最初想用 `~/.openclaw/workspace/outputs/` 作为 Vault，配置后发现 MCP Server 报安全错误。

**原因**：`obsidian-mcp` 默认拒绝访问 `.` 开头的路径。

**解决**：老老实实迁移到 `~/Documents/ObsidianVault/`。

### 坑 2: 软链接也不行

想用软链接绕过隐藏目录限制：

```bash
ln -s ~/.openclaw/workspace/outputs ~/obsidian-vault
```

结果 MCP Server 会检测软链接的实际指向，还是被拒绝了。

**教训**：安全限制是认真的，别想绕过去。

### 坑 3: 坚果云 WebDAV 权限

用 Remotely Save 连坚果云，提示认证失败。

**原因**：坚果云不能用登录密码，要用"应用专用密码"。

**解决**：坚果云官网 → 账户信息 → 安全选项 → 添加应用密码

## AI 现在能做什么

配置完成后，我的 AI 助手可以：

```
我：帮我看看今天探索者发现了什么

AI：（调用 obsidian.read_note）今天探索者收集了 3 条资讯...

我：根据第二条写一篇小红书

AI：（调用 obsidian.create_note）已创建 writer/xiaohongshu/xxx.md
```

不用再手动复制粘贴，也不用告诉 AI 文件的完整路径。

## 总结

这套配置的核心价值：

1. **AI 直接操作笔记**：通过 MCP 协议，省去手动搬运
2. **多端同步**：坚果云 WebDAV + Git 双保险
3. **Obsidian 生态**：双链、图谱、Dataview 查询

如果你也在用 AI 助手生成内容，强烈建议试试这个方案。

---

*相关链接：*
- [OpenClaw](https://github.com/openclaw/openclaw) - 我用的 AI 助手框架
- [obsidian-mcp](https://www.npmjs.com/package/@jianruidutong/obsidian-mcp) - Obsidian MCP Server
- [Dataview](https://github.com/blacksmithgu/obsidian-dataview) - Obsidian 查询插件
- [Remotely Save](https://github.com/remotely-save/remotely-save) - 多端同步插件
