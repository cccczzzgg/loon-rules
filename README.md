# Loon 国内直连规则

国内直连，不走代理。给 Loon 用的远程规则。

## 文件

- `China-Direct.list` — 全量国内直连规则，2026-11-09 生成
  - DOMAIN-SUFFIX: 109,418
  - DOMAIN-KEYWORD: 13
  - IP-CIDR: 12,822
  - IP-CIDR6: 4,312
  - USER-AGENT: 65
  - 合计: 126,630 条
- `China-Direct.yaml` — 同一份规则的 Clash / Mihomo / FlClash 版（Rule Provider，domain，109,431 条 payload）
- `China-Direct-IP.yaml` — 同一份规则的中国 IP 段版（Rule Provider，ipcidr，17,134 条 payload）
- `WebRTC-Block.list` — WebRTC 防泄露规则，2026-11-14 生成（STUN 关键词域名 + STUN/TURN 端口共 12 条；Loon 导入时策略选 REJECT，顺序放在所有远程规则最上面）

## 来源（已合并去重）

- blackmatrix7/ios_rule_script — ChinaMax（2026-10-28）
- felixonmars/dnsmasq-china-list — accelerated-domains.china.conf
- ACL4SSR/ACL4SSR — ChinaDomain.list
- Loyalsoldier/geoip — 中国 IP 段（cn.txt）
- 手工补齐：微信 / QQ / 腾讯、京东、拼多多、美团、抖音、快手、小红书、知乎、B站、爱奇艺、优酷、网易云、银行、运营商、12306、政府 / 教育等常用国内服务

为什么之前那份 ChinaMax 感觉不全：它域名部分只有 51 条 DOMAIN-SUFFIX（主要靠 `cn` 一个后缀兜底）+ 12,556 条 IP 段。国内很多服务用的是 `.com` / `.net`（比如 `qq.com`、`jd.com`、`douyin.com`），不在那 51 条里，只能靠 IP 兜底，域名没命中就先走了代理。这份把那 10 万多个国内 `.com` / `.net` 域名都补上了。

## Loon 里怎么导入

1. Loon → 规则 → 右上角 `+`
2. 选择“从 URL 导入”（远程规则）
3. 粘贴：
   ```
   https://raw.githubusercontent.com/cccczzzgg/loon-rules/main/China-Direct.list
   ```
4. 策略选 `DIRECT`
5. 顺序：放在 YouTube / Telegram / Steam 等国外代理规则**之后**，兜底规则（FINAL）**之前**
6. 导入后把原来那份 `国内网站/IP合集 ChinaMax` 和 `GEOIP_CN` 关掉或删掉，避免重复（这份里已经包含中国 IP 段）

## 注意

- Loon 官方文档说支持百万级规则，20 万条 DOMAIN-SUFFIX 查询在 1ms 内，这份 12.6 万条不用担心性能。
- 域名规则没命中时，Loon 会解析 DNS 再匹配 IP 规则，所以即使某个域名漏了，只要服务器 IP 在中国，也会走 DIRECT。

## WebRTC 防泄露怎么导入

1. Loon → 规则 → 右上角 `+`
2. 选择“从 URL 导入”（远程规则）
3. 粘贴：
   ```
   https://raw.githubusercontent.com/cccczzzgg/loon-rules/main/WebRTC-Block.list
   ```
4. 策略选 `REJECT`
5. 顺序：拖到所有远程规则的**最上面**（必须在 China-Direct 之上）
6. 配套设置（在当前配置的 `[General]` 段加两行）：`disable-stun = true`、`udp-fallback-mode = REJECT`
