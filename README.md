# Shadowrocket 网络规则

一套面向日常使用的 Shadowrocket 分流配置。境外服务和 AI 服务默认走代理，国内服务、局域网、Apple / iCloud 及券商服务优先直连。

> 本仓库只提供配置和规则，不包含代理节点或订阅。

## 项目特点

- 轻量主配置：不内置策略组，直接使用 Shadowrocket 的 `PROXY` / `DIRECT` 策略。
- AI 服务覆盖：支持 OpenAI、Claude、Gemini、Copilot、Grok、Perplexity 等常用服务。
- 券商规则拆分：美国券商覆盖 IBKR、Schwab、Firstrade；香港券商覆盖富途 / Moomoo、长桥和老虎证券。
- 国内外分流：常用境外服务走代理，国内服务与中国大陆 IP 优先直连。
- 网络稳定性优化：使用 DNSPod / AliDNS，并对代理连接屏蔽 QUIC。
- Google 防跳转：将 `google.cn` 和 `g.cn` 重定向至 `google.com`。

## 文件结构

| 文件 | 说明 | 使用方式 |
| --- | --- | --- |
| [`custom-rules.conf`](./custom-rules.conf) | 推荐使用的主配置 | 直接导入 Shadowrocket |
| [`AI.list`](./AI.list) | 自维护 AI 服务规则集 | 已被主配置引用，也可单独引用 |
| [`US_Broker.list`](./US_Broker.list) | 自维护美国券商规则集 | IBKR、Schwab、Firstrade |
| [`HK_Broker.list`](./HK_Broker.list) | 自维护香港券商规则集 | 富途 / Moomoo、长桥、老虎证券 |
| [`lazy.conf`](./lazy.conf) | 带完整注释的扩展配置 | 用于查阅参数或需要更多娱乐、游戏规则时使用 |

## 快速开始

### 通过 URL 导入

复制主配置地址：

```text
https://raw.githubusercontent.com/KleinBrey/net-rules/refs/heads/main/custom-rules.conf
```

然后在 Shadowrocket 中依次操作：

1. 进入 **配置** 页面。
2. 点击右上角 **+**。
3. 粘贴主配置地址并下载。
4. 点击下载完成的配置，选择 **使用配置**。
5. 回到首页添加自己的节点或订阅。
6. 执行连通性测试并选择可用节点。
7. 将全局路由设置为 **配置**。

### 扫码导入

![扫码导入 Shadowrocket 配置](https://api.qrserver.com/v1/create-qr-code/?size=220x220&margin=12&data=https%3A%2F%2Fraw.githubusercontent.com%2FKleinBrey%2Fnet-rules%2Frefs%2Fheads%2Fmain%2Fcustom-rules.conf)

## 分流策略

规则按照配置文件中的顺序匹配；越靠前的规则优先级越高，域名规则优先于 IP 规则。

| 优先级 | 服务或范围 | 策略 |
| ---: | --- | :---: |
| 1 | AI 服务 | `PROXY` |
| 2 | YouTube、Telegram、X、Facebook、Spotify、PayPal、Amazon、Notion | `PROXY` |
| 3 | Steam、GitHub、Microsoft、Google | `PROXY` |
| 4 | Apple / iCloud | `DIRECT` |
| 5 | Bilibili、微信、小红书、网易云音乐 | `DIRECT` |
| 6 | 美国券商：IBKR、Schwab、Firstrade | `DIRECT` |
| 7 | 香港券商：富途 / Moomoo、长桥、老虎证券 | `DIRECT` |
| 8 | 战网、抖音 | `DIRECT` |
| 9 | TikTok | `PROXY` |
| 10 | Global 规则集 | `PROXY` |
| 11 | China 规则集 | `DIRECT` |
| 12 | 局域网 | `DIRECT` |
| 13 | 中国大陆 IP（`GEOIP,CN`） | `DIRECT` |
| 14 | 其余未匹配流量 | `PROXY` |

## 独立规则集

如果不使用主配置，也可以单独引用以下规则集。

### AI 服务

```text
https://raw.githubusercontent.com/KleinBrey/net-rules/refs/heads/main/AI.list
```

### 美国券商

```text
https://raw.githubusercontent.com/KleinBrey/net-rules/refs/heads/main/US_Broker.list
```

### 香港券商

```text
https://raw.githubusercontent.com/KleinBrey/net-rules/refs/heads/main/HK_Broker.list
```

引用时请选择 `RULE-SET` 类型，并根据需要设置 `PROXY` 或 `DIRECT` 策略。主配置默认将 AI 服务设为 `PROXY`，美国和香港券商服务设为 `DIRECT`。

## 主要配置

| 项目 | 当前设置 | 说明 |
| --- | --- | --- |
| DNS | DNSPod / AliDNS DoH 与普通 DNS | 减少对单一 DNS 服务的依赖 |
| 备用 DNS | 系统 DNS | 主 DNS 查询失败或超时时回退 |
| IPv6 | 开启，IPv4 优先 | 同时查询 A / AAAA 记录，不优先 IPv6 |
| DNS 劫持 | `8.8.8.8:53`、`8.8.4.4:53` | 接管使用 Google DNS 的硬编码查询 |
| QUIC | `all-proxy` | 代理流量阻断 UDP/443，促使连接回退到 HTTP/2 或 HTTP/1.1 |
| Apple / iCloud | 系统 DNS、直连 | 保持常用 Apple 服务的本地网络路径 |
| Google 重写 | `google.cn`、`g.cn` → `google.com` | 避免搜索域名跳转 |
| HTTPS 解密范围 | `*.google.cn` | 不对其他域名启用 MITM |

## 规则来源

- `AI.list`：本仓库维护。
- `US_Broker.list`：本仓库维护的美国券商规则。
- `HK_Broker.list`：本仓库维护的香港券商规则。
- [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)：常用服务、国内外域名及局域网规则。

## 更新与维护

- 修改 `AI.list`、`US_Broker.list` 或 `HK_Broker.list` 后，需要将文件提交并推送到 GitHub，远程 Raw 地址才会更新。
- 在 Shadowrocket 中点击对应配置并选择 **更新配置**，可获取最新主配置。
- 外部规则集会随上游项目更新，出现异常分流时可在 Shadowrocket 的请求日志中检查实际命中的规则。
- 新增规则时应优先使用精确的 `DOMAIN` 或 `DOMAIN-SUFFIX`；仅在域名结构不固定时使用 `DOMAIN-KEYWORD`。

## 注意事项

- 请自行添加合法、有效的节点或订阅。
- 主配置没有自定义策略组，所有 `PROXY` 规则使用 Shadowrocket 当前选中的节点。
- 自动测速、简单模式或回退功能可能切换代理节点；对出口 IP 敏感的服务建议固定节点。
- 如需使用 HTTPS 解密，请先在 Shadowrocket 中生成、安装并信任 CA 证书。

## License

[MIT](./LICENSE)
