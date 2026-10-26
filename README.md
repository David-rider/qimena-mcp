# Qimena MCP Server · 天衍奇门 MCP 服务

Free, hosted [Model Context Protocol](https://modelcontextprotocol.io) server for **Qi Men Dun Jia (奇门遁甲)** and **BaZi Four Pillars (八字)** calculations, from [Qimena](https://qimena.com).

- **Endpoint:** `https://qimena.com/mcp`
- **Transport:** Streamable HTTP (stateless)
- **Auth:** none, no API key
- **Data:** nothing you send is stored
- **Cost:** free

The tools are deterministic: the same engine as the qimena.com website, with no AI model behind them. Your assistant does the interpreting; the server does the calendar and chart maths.

This repository holds documentation only. The server runs at qimena.com.

[中文说明](#中文说明)

---

## Tools

| Tool | What it returns |
| --- | --- |
| `qimen_chart` | An hour-based Qi Men Dun Jia chart for a moment and place: four pillars, ju, Chief Star and Door, and for each of the nine palaces the deity, star, door, heaven/earth/hidden stems, void, horse star, punishment and door pressure, plus patterns, palace scores and lucky directions. |
| `bazi_pillars` | Four Pillars for a birth moment. Converts clock time to true solar time using that date's zone rules (daylight saving included) and the longitude. The year follows Li Chun; the day changes at 23:00 true solar time. Returns the pillars, Day Master and five-element counts. |
| `best_times` | Casts a chart for every double-hour of the next 1 to 7 days and ranks them for a goal (general, career, wealth, love), using the person's Day Master. Returns the top windows with time range, score and lucky direction. |
| `solar_terms` | The 24 solar terms of any year from 1900 to 2100, with UTC and Beijing-time instants (within about a minute of the Hong Kong Observatory). |

### Parameters

**qimen_chart**
- `datetime`: local time `"YYYY-MM-DDTHH:mm"` in `timezone`. Omit for now.
- `timezone`: IANA zone such as `"Asia/Shanghai"` or `"America/New_York"`. Default `Asia/Shanghai`.
- `longitude`: degrees, east positive. Default: the zone's standard meridian.
- `focus`: `general`, `career`, `wealth` or `love`. Default `general`.

**bazi_pillars**
- `birth_datetime` (required): `"YYYY-MM-DDTHH:mm"`, as on the birth certificate.
- `timezone` (required): IANA zone of the birthplace.
- `longitude`: birthplace longitude, east positive.

**best_times**
- `birth_date` (required): `"YYYY-MM-DD"`.
- `focus`: `general`, `career`, `wealth` or `love`.
- `longitude`: where the person is now. Default 120.
- `days`: 1 to 7. Default 7.
- `top`: 1 to 10. Default 5.

**solar_terms**
- `year` (required): 1900 to 2100.

## Connect

**Claude Code**

```bash
claude mcp add --transport http qimena https://qimena.com/mcp
```

**Claude Desktop** (`claude_desktop_config.json`, through `mcp-remote`)

```json
{
  "mcpServers": {
    "qimena": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://qimena.com/mcp"]
    }
  }
}
```

**Cursor** (`~/.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "qimena": { "url": "https://qimena.com/mcp" }
  }
}
```

**VS Code** (`.vscode/mcp.json`)

```json
{
  "servers": {
    "qimena": { "type": "http", "url": "https://qimena.com/mcp" }
  }
}
```

Any other client that speaks Streamable HTTP can use the URL directly.

## Try it

Ask your assistant things like:

- "Cast a Qi Men chart for now in New York and tell me the best direction for a business meeting."
- "What are my BaZi pillars? I was born 1990-05-17 08:30 in Shanghai."
- "Born 1985-11-02. Which double-hours in the next three days are best for signing a contract?"
- "When is Li Chun in 2027?"

Or call it directly:

```bash
curl -s https://qimena.com/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"solar_terms","arguments":{"year":2027}}}'
```

## Limits and notes

- About 60 requests a minute per IP address.
- Calculation rules and how they were checked: [Qi Men calculation rules](https://qimena.com/en/wiki/qimen-calculation-rules/).
- More about the server: [Qimena MCP Server](https://qimena.com/en/wiki/qimena-mcp/).
- Results are for cultural and reference use, not professional advice.

## Contact

support@qimena.com · [qimena.com](https://qimena.com)

---

## 中文说明

天衍奇门（[qimena.com](https://qimena.com)）提供的免费 MCP 服务，让 AI 助手可以直接调用**奇门遁甲**和**八字**排盘。

- **地址：** `https://qimena.com/mcp`
- **连接方式：** Streamable HTTP（无状态）
- **不需要密钥**，免费使用，不保存您发送的任何内容。

工具都是确定性计算，和 qimena.com 网站用的是同一套排盘引擎，背后没有 AI 模型：排盘和历法由服务器算，解读由您的 AI 助手完成。本仓库只放说明文档，服务运行在 qimena.com。

### 四个工具

| 工具 | 作用 |
| --- | --- |
| `qimen_chart` | 按时间和地点起时家奇门盘：四柱、局数、值符值使，九宫的八神、九星、八门、天盘地盘暗干、空亡、驿马、击刑、门迫，以及格局、宫位评分和吉方。 |
| `bazi_pillars` | 按出生时间排八字：用出生当天的时区规则（含夏令时）和经度换算真太阳时；年柱以立春为界，23:00（真太阳时）换日。返回四柱、日主和五行数量。 |
| `best_times` | 把未来 1–7 天的每个时辰都起一盘，按目标（综合、事业、财运、感情）和本人日主排序，返回最好的几个时段、评分和当时的吉方。 |
| `solar_terms` | 1900–2100 年任一年的二十四节气时刻（UTC 和北京时间，与香港天文台相差约一分钟以内）。 |

### 连接

Claude Code：

```bash
claude mcp add --transport http qimena https://qimena.com/mcp
```

其他客户端的配置见上面英文部分的 Connect（Claude Desktop、Cursor、VS Code）。

### 可以这样问

- “用纽约现在的时间起一盘奇门，看看商务见面往哪个方向好。”
- “我 1990-05-17 08:30 生于上海，八字是什么？”
- “我生于 1985-11-02，未来三天哪个时辰适合签合同？”
- “2027 年立春是哪天几点？”

### 说明

- 每个 IP 每分钟约 60 次请求。
- 排盘规则与验证：[奇门排盘规则](https://qimena.com/wiki/qimen-calculation-rules/)
- 服务介绍：[天衍奇门 MCP 服务](https://qimena.com/wiki/qimena-mcp/)
- 结果仅供文化研究与参考，不构成专业建议。

联系：support@qimena.com
