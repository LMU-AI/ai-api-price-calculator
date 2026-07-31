# AI API Token 价格对比计算器

**在线使用：[calc.lmu.ai](https://calc.lmu.ai/)**

将 Claude、OpenAI、Gemini 等各平台 API 中转站的复杂计费统一换算为 **¥/百万 token**，一眼看清真实成本。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
![Single HTML File](https://img.shields.io/badge/single%20file-HTML-orange)
![No Backend](https://img.shields.io/badge/backend-none-green)

---

## 为什么需要这个工具

对比 Claude API 中转站价格时，有三个常见的坑：

- **换算后价差超 6 倍**：各平台优惠方式不同（低汇率 / 美元价打折 / 叠加倍率），有的等效汇率低到 ¥1.2/$，有的高达 ¥7/$，直接看美元标价根本没法比
- **Prompt Cache 费用被忽略**：Cache 费用占 Claude Code 实际成本的 50%+，多数比价表不包含这一项
- **充值前不知道月费**：各平台单价不同、汇率不同，很难预估真实的月支出

这个工具把所有变量统一换算，填入月用量后直接显示月费估算。

---

## 功能

- **统一换算**：所有平台价格换算为 ¥/百万 token，消除汇率差异
- **完整计费项**：输入、输出、Cache 读取、Cache 写入（5m / 1h）全部纳入
- **加权均价**：可调输入/输出权重比（默认 30/70，符合 Claude Code 实际场景）
- **月费估算**：填入月用量后新增月费估算列
- **多平台支持**：内置8个主流中转站数据，支持添加自定义平台
- **数据导出**：一键复制 Markdown 表格 / 下载 CSV
- **多语言**：支持中文、English、日本語、한국어、Español、Français、Deutsch、Português
- **本地持久化**：数据存 localStorage，不上传任何内容

---

## 在线使用

**👉 [https://calc.lmu.ai/](https://calc.lmu.ai/)**

无需安装，打开即用。

---

## 本地运行

直接用浏览器打开 `index.html`，无需任何构建步骤或服务器。

```bash
git clone https://github.com/LMU-AI/ai-api-price-calculator.git
cd ai-api-price-calculator
open index.html   # macOS
# 或直接双击文件
```

---

## 自部署

单文件 HTML，放到任何静态文件托管即可：

```bash
# Nginx / Caddy / Apache：直接放入 web root
# GitHub Pages：push 到 gh-pages 分支
# Vercel / Netlify：直接拖拽上传
```

---

## 内置平台数据（2026年7月）

| 平台 | 汇率/折扣 | 输入 ¥/M | 输出 ¥/M | 缓存读 ¥/M | 加权均价 ¥/M |
|------|----------|---------|---------|-----------|------------|
| [灵眸AI（按量）](https://api.lmuai.com/g/vJaWWr4T) | ×1.2* | 6.00 | 30.00 | 0.60 | **22.80** |
| [poloapi](https://aihubmix.com/?aff=hC5M) | 7.0 | 32.90 | 163.10 | 3.29 | 124.04 |
| [laozhang.ai](https://api.laozhang.ai/register/?aff_code=ZlNM) | 7.0 | 35.00 | 175.00 | 3.50 | 133.00 |
| [apiyi](https://api.apiyi.com/) | 7.0 | 35.00 | 175.00 | 3.50 | 133.00 |
| [AIHubMix](https://api.apiyi.com/register/?aff_code=QsuZ) | 7.1 | 35.50 | 177.50 | 3.55 | 134.90 |
| [OpenRouter](https://openrouter.ai/) | 7.27 | 36.35 | 181.75 | 3.64 | 138.13 |
| ~~神马中转API~~ | — | — | — | — | ⛔ 已停服 |

> 加权均价按输入 30% / 输出 70% 计算，基于 claude-opus-4-8 定价（官方 $5/$25）。平台价格随时可能变化，以官网实时公示为准。
>
> \* 灵眸AI 为人民币直接定价：人民币单价 = 官方美元单价 × 1.2（表中 1.2 为等效汇率，非充值汇率）。表为按量充值档（¥10 起）；另有月套餐档约官方价 1.4 折，加权约 ¥18.0/M。神马中转API 于 2026 年 7 月停止注册和充值，数据移除仅作说明。

---

## 姐妹工具

- [relay-pricing-decode](https://github.com/LMU-AI/relay-pricing-decode)：判断中转平台¥价格记账口径（1元=1美元虚拟记账 vs 真实汇率换算），本工具的价格数字是否可信可以先用它验证
- [relay-latency-check](https://github.com/LMU-AI/relay-latency-check)：实测中转平台首字节延迟 TTFT，价格便宜不代表体感好
- [check-claude-api](https://github.com/LMU-AI/check-claude-api)：检测接口是不是真官方协议、Cache 是否真支持

---

## 技术说明

- 单文件 HTML，约 1000 行
- 无依赖，无构建工具，无框架
- 所有计算在浏览器本地完成
- 数据持久化使用 localStorage

---

## 贡献

发现平台数据有误或已过时，欢迎提 Issue 或 PR 更新。

添加新平台：在 `DEFAULT_PLATFORMS` 数组中增加一条记录即可，字段说明：

```javascript
{
  id: 9,           // 唯一 ID
  name: '平台名称',
  url: 'https://...',   // 官网或注册链接
  currency: 'USD',      // 'USD' 或 'CNY'
  rate: 7.0,            // 内部汇率（currency 为 USD 时生效）
  input: 5,             // 输入价格（$/M 或 ¥/M）
  output: 25,           // 输出价格
  cacheRead: 0.5,       // Cache 读取价格
  cacheWrite5: 6.25,    // Cache 写入（5分钟）
  cacheWrite1h: 10,     // Cache 写入（1小时）
}
```

---

## License

MIT
