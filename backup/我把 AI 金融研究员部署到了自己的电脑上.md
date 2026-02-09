最近发现一个有意思的开源项目 [Dexter](https://github.com/dexterai/dexter)，它是一个**自主金融研究 Agent**——你问它一个金融问题，它会自动拆解任务、调用市场数据 API、交叉验证信息，最后给你一份研究报告。

听起来很酷，实际部署下来也确实学到不少东西。记录一下过程和踩的坑。

## Dexter 是什么

简单说，它是一个会"思考"的金融研究员。

传统方式查财务数据：你得知道去哪查、怎么筛、怎么对比。

Dexter 的方式：你直接问"NVIDIA 和微软谁的利润率更高"，它会：
1. 拆解问题：需要查两家公司的毛利率、营业利润率、净利率
2. 调用 API：分别获取 NVDA 和 MSFT 的财务数据
3. 对比分析：生成结构化对比结果
4. 自我验证：确认数据来源和时效性

底层用的是 LangChain 的 Agent 框架，支持多步推理和工具调用。

## 技术栈

- **运行时**: Bun（比 Node.js 快，但坑也不少）
- **AI 框架**: LangChain
- **终端 UI**: Ink（React 语法写命令行界面）
- **数据源**: Financial Datasets API、Exa Search

## 部署步骤

### 1. 克隆仓库

```bash
git clone https://github.com/dexterai/dexter.git
cd dexter
bun install
```

### 2. 配置环境变量

创建 `.env` 文件：

```bash
# 必需：OpenAI API
OPENAI_API_KEY=sk-xxx

# 必需：金融数据 API（免费额度支持 AAPL/NVDA/MSFT）
FINANCIAL_DATASETS_API_KEY=xxx

# 可选：网络搜索（加密货币查询需要）
EXASEARCH_API_KEY=xxx
```

API Key 获取：
- OpenAI: [platform.openai.com](https://platform.openai.com)
- Financial Datasets: [financialdatasets.ai](https://financialdatasets.ai) （注册送免费额度）
- Exa: [exa.ai](https://exa.ai) （1000次/月免费）

### 3. 关闭 LangSmith 追踪

这是第一个坑。默认配置开启了 LangSmith 追踪，但如果没配置 LangSmith 账号会报 403 错误。

在 `.env` 里加一行：

```bash
LANGCHAIN_TRACING_V2=false
```

## 踩坑记录

### 坑 1: 交互式界面不适合自动化

Dexter 默认是交互式终端界面，用 Ink 做的 React 组件。很酷，但不方便程序化调用。

**解决方案**：写一个非交互式入口脚本 `run-query.ts`：

```typescript
import { runQuery } from './src/agent';

const query = process.argv.slice(2).join(' ');
if (!query) {
  console.error('Usage: bun run run-query.ts "your question"');
  process.exit(1);
}

runQuery(query).then(result => {
  console.log(JSON.stringify(result, null, 2));
});
```

这样就能在命令行直接调用：

```bash
bun run run-query.ts "Apple 最新一季的营收是多少"
```

### 坑 2: 加密货币查询失败

问 BTC 价格报 `Invalid API key`。

**原因**：加密货币数据不走 Financial Datasets API，而是通过 Exa 搜索网页获取。

**解决**：配置 `EXASEARCH_API_KEY` 后解决。

### 坑 3: 中文问题

Dexter 的 prompt 是英文的，中文问题处理得不太好。

**我的方案**：问之前翻译成英文，答案再翻译回中文。虽然多一步，但准确率高很多。

## 实测效果

### 测试 1: Apple 营收查询

问：What is Apple's latest quarterly revenue?

答：$143.756 billion (FY2026 Q1, ending December 27, 2025)

数据来源准确，还标注了财年和截止日期。

### 测试 2: 利润率对比

问：Compare the profit margins between NVIDIA and Microsoft

答：

| 公司 | 毛利率 | 营业利润率 | 净利率 |
|------|--------|-----------|--------|
| NVIDIA | 73.4% | 66.7% | 56.0% |
| Microsoft | 68.0% | 60.3% | 47.3% |

NVIDIA 在各项利润率上都领先，主要受益于 AI 芯片的高定价权。

### 测试 3: BTC 价格

问：What's the current price of Bitcoin?

答：$69,571.57 (-2.20% in 24h)

## 我的使用方式

我把 Dexter 集成到了我的 AI 助手流程里：

1. 我用中文提问
2. AI 助手翻译成英文，调用 Dexter
3. 拿到结果后翻译回中文返回给我

这样既利用了 Dexter 的专业金融分析能力，又保持了中文交互的便利。

## 总结

Dexter 适合：
- 需要快速查询美股财务数据
- 想做公司间对比分析
- 不想手动去各个网站扒数据

不适合：
- A 股数据（不支持）
- 实时盯盘（更新有延迟）
- 中文问答（需要额外处理）

免费额度对于个人研究足够用了。如果你也对 AI + 金融感兴趣，可以试试。

---

*相关链接：*
- [Dexter GitHub](https://github.com/dexterai/dexter)
- [Financial Datasets API](https://financialdatasets.ai)
- [Exa Search API](https://exa.ai)