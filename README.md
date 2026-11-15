# Shadowrocket Rules

个人 Shadowrocket 配置，基于 [LingJingMaster/Shadowrocket-Rules](https://github.com/LingJingMaster/Shadowrocket-Rules) 修改。

## 订阅地址

```
https://raw.githubusercontent.com/hanboyd/Shadowrocket-Rules/main/HB-Shadowrocket.conf
```

## 主要调整

- **Google AI 与普通 Google 分离** — Gemini、AI Studio、NotebookLM、DeepMind 等优先走 🤖 AI 服务（美国稳定节点），普通 Google 仍走日本节点
- **AI 规则扩展** — 补充 OpenAI 相关域名（oaistatsig、cdn.openaimerge、workos 等）
- **AI / 银行 / 券商稳定出口** — 使用 `select` 手动选择，避免 `url-test` 自动切换 IP
- **YouTube 规则集前置** — `Google.list` 含 `DOMAIN-KEYWORD,google`，若排在前面会把 `googlevideo.com`（油管视频流）划入谷歌服务组，导致油管视频与 API 走错出口
- **补齐 Google 新 AI 端点** — `aiplatform` / `cloudcode-pa` / `gems-pa` / `generativelanguage-pa` / `aisuite-pa` 等 `googleapis.com` 子域原本会被 Google 规则集通配吞掉
- **Global 规则集成对引用** — `Global.list` 只含 IP/UA/关键词规则（头部 `TOTAL 35323` 是全量统计），35k 域名主体在 `Global_Domain.list`，两者必须同时使用，否则「🌍 非中国」几乎是空壳
- **区域正则词边界** — 裸 `US`/`TW` 会误命中 `PLUS`/`BUS`/`RUS`/`AUS`/`MUST`/`NETWORK`；改为 `(?:^|[^A-Za-z])US(?:[^A-Za-z]|$)` 并补齐 `川日`/`关西`/`圣何塞`/`硅谷`/`Phoenix` 等别名
- **其他节点排除订阅信息伪节点** — 「剩余流量」「套餐到期」「Traffic」「Expiry」「在线客服」等条目不再污染测速池
- **业务组首选统一为 🚀 节点选择** — 区域自动组降为次选，只订阅部分地区节点的用户也不会遇到空策略组
- **移除无必要内容** — 删除 Google Rewrite / MITM、T-Mobile Wi-Fi Calling 规则
- **大陆流量强制直连** — 同时引用 `ChinaMax_Domain.list` 域名集和 `ChinaMax.list` IP/其他规则，国内域名与中国大陆 IP 命中后直接使用 `DIRECT`，不经过可记忆选择的策略组
- **国内 HTTPDNS 直连** — 微信、京东、阿里、哔哩哔哩、美团、网易、百度等国内应用的 HTTPDNS 入口直接放行，避免拒绝后等待超时和二次解析
- **分流 DNS** — 直连域名使用 AliDNS / DNSPod 国内 DoH（`dns-direct-system = false`），备用 DNS 同样使用加密 DoH，避免主 DoH 失败时明文回退系统 DNS；代理域名由代理服务器端解析
- **启用 IPv6、优先 IPv4** — 保留双栈网络能力，减少开启 Shadowrocket 后与系统原生网络路径的差异，同时避免强制优先 IPv6
- **Tailscale 网段进隧道** — `tun-excluded-routes` 不再包含 `100.64.0.0/10`，改用 `tun-included-routes` + `IP-CIDR,100.64.0.0/10,TAILSCALE`，避免 Tailscale 流量被绕出隧道
- **保留** — Apple 直连、Blackmatrix7 公共规则集

## 规则引用

本配置引用的外部规则集：

| 来源 | 用途 |
|---|---|
| [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script) | Google、YouTube、Telegram、GitHub、Microsoft 等公共规则 |
| [LingJingMaster/Shadowrocket-Rules](https://github.com/LingJingMaster/Shadowrocket-Rules) | AI、Apple、HSBC、银行、券商规则 |

## 测试文件

- `Shadowrocket-original.conf` — 上游原版配置（A/B 对照用）
- `Shadowrocket-v1~v4.conf` — 二分法定位中间版本

## 上游项目

- [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)
- [LingJingMaster/Shadowrocket-Rules](https://github.com/LingJingMaster/Shadowrocket-Rules)

## 最后更新

2026-11-15
