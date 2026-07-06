# Shadowrocket 网络规则

一份自用的 Shadowrocket 规则配置，主配置以轻量分流为主：境外与 AI 服务走代理，国内、局域网、Apple / iCloud 等常用服务优先直连。导入配置后添加自己的节点或订阅即可使用。

## 当前重点

- `custom-rules.conf` 是推荐使用的主配置
  - 使用腾讯 DNSPod / 阿里 DNS 的 DoH 与普通 DNS
  - 保留局域网、银行、Apple Captive Portal 等常见直连跳过项
  - 默认屏蔽代理连接中的 QUIC，降低 UDP/443 在复杂网络下的不稳定影响
  - 启用 Google 中国域名防跳转重写
- `AI.list` 为自维护 AI 服务规则
  - 覆盖 ChatGPT / OpenAI / Sora
  - 覆盖 Apple Intelligence
  - 覆盖 Claude / Anthropic
  - 覆盖 Microsoft Copilot、GitHub Copilot
  - 覆盖 Gemini / Google AI、Grok、OpenRouter、Perplexity
- `lazy.conf` 保留为带完整注释的懒人配置版本
  - 适合查看 Shadowrocket 参数说明
  - 规则面更宽，包含 Netflix、Disney+、HBO、游戏平台等更多分流

## 文件说明

| 文件 | 用途 | 推荐场景 |
| --- | --- | --- |
| `custom-rules.conf` | 主配置文件 | 日常直接导入使用 |
| `AI.list` | AI 服务规则集 | 被主配置引用，也可单独引用 |
| `lazy.conf` | 注释版完整配置 | 学习参数或需要更全娱乐/游戏规则时使用 |

## 默认策略

| 服务 | 默认策略 |
| --- | --- |
| AI 服务 | `PROXY` |
| YouTube / Telegram / X / Facebook / Spotify / PayPal / Amazon / Notion | `PROXY` |
| Steam / GitHub / Microsoft / Google | `PROXY` |
| Apple / iCloud | `DIRECT` |
| Bilibili / 微信 / 小红书 / 网易云音乐 / 战网 | `DIRECT` |
| 香港券商 | `DIRECT` |
| 抖音 | `DIRECT` |
| TikTok | `PROXY` |
| 国内服务与中国 IP | `DIRECT` |
| 漏网之鱼 | `PROXY` |

## 快速开始

1. 复制主配置 Raw 链接：

   ```text
   https://raw.githubusercontent.com/KleinBrey/net-rules/refs/heads/main/custom-rules.conf
   ```

2. 或者直接扫描二维码导入：

   ![扫码导入 Shadowrocket 配置](https://api.qrserver.com/v1/create-qr-code/?size=220x220&margin=12&data=https%3A%2F%2Fraw.githubusercontent.com%2FKleinBrey%2Fnet-rules%2Frefs%2Fheads%2Fmain%2Fcustom-rules.conf)

3. 打开 Shadowrocket，进入 `配置`。
4. 点击右上角 `+`，粘贴链接并下载。
5. 点击已下载的配置，设为使用中。
6. 回到首页添加自己的节点或订阅。
7. 执行连通性测试，选择可用节点连接。

如果只想引用 AI 规则集，可使用：

```text
https://raw.githubusercontent.com/KleinBrey/net-rules/refs/heads/main/AI.list
```

## 分流规则

规则从上到下依次匹配，前面的规则优先级更高。

| 优先级 | 服务 | 默认策略 |
| --- | --- | --- |
| 1 | AI 服务 | `PROXY` |
| 2 | YouTube / Telegram / X / Facebook 等境外服务 | `PROXY` |
| 3 | GitHub / Microsoft / Google | `PROXY` |
| 4 | Apple / iCloud | `DIRECT` |
| 5 | Bilibili / 微信 / 小红书 / 网易云音乐等国内服务 | `DIRECT` |
| 6 | 香港券商 | `DIRECT` |
| 7 | 抖音 | `DIRECT` |
| 8 | TikTok | `PROXY` |
| 9 | Global 规则集 | `PROXY` |
| 10 | China 规则集 | `DIRECT` |
| 11 | 局域网 | `DIRECT` |
| 12 | GEOIP CN | `DIRECT` |
| 13 | FINAL 兜底 | `PROXY` |

## 规则集来源

- `AI.list`：本仓库自维护 AI 规则
- `blackmatrix7/ios_rule_script`：主要服务分流规则来源
- `LingJingMaster/Shadowrocket-Rules`：香港券商规则来源

## 其他特性

- DNS：默认使用 DNSPod / AliDNS 的 DoH，并保留 `223.5.5.5`、`119.29.29.29` 普通 DNS
- 备用 DNS：回退到系统 DNS
- DNS 劫持：拦截常见 Google DNS `8.8.8.8:53`、`8.8.4.4:53`
- QUIC 屏蔽：对代理连接屏蔽 UDP/443，促使连接回退到 HTTP/2 或 HTTP/1.1
- Apple / iCloud：指定使用系统 DNS，尽量保持直连稳定性
- Google 防跳转：`google.cn` / `g.cn` 自动跳转到 `google.com`
- MITM：仅配置 `*.google.cn`

## 注意事项

- 本仓库不包含节点，请在 Shadowrocket 中自行添加节点或订阅。
- `custom-rules.conf` 没有策略组，规则直接使用 `PROXY` / `DIRECT`，适合简单使用。
- 如需更细的策略组、地区节点选择或自动测速，可在此配置基础上自行扩展。
- 如需 HTTPS 解密功能，请在 Shadowrocket 中生成并安装 CA 证书。
- 引用的外部规则集可能会随上游更新而变化。

## License

MIT
