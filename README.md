# jr-delay — 实时延误数据静态镜像

`jr.suki.ing` 主站的实时延误数据源（`delays.json` 等）纯静态镜像，部署为
Cloudflare Workers Static Assets「仅资产」模式：**无 Worker 脚本，任何请求
（含 404）都不触发 Worker 执行 → 零调用、零计费**（免费档 10 万请求/天）。

## 数据来源

由 [ooep/delay](https://github.com/ooep/delay) 的烘焙 workflow（`*/10`）同步：

- `assets/data/delays.json` — 晚点板主表（含 `by_suki_key` 直查表）
- `assets/data/sukijr.json` — 一对一简表
- `assets/data/lines.json` — 线路桥缓存（烘焙侧使用）
- `assets/data/keikyu-positions.json` — 京急原始位置（烘焙中间产物）
- `assets/data/suki/` — 主站时刻表每周快照（烘焙输入缓存）

## 部署

push main 即触发 `.github/workflows/deploy.yml`：

```bash
CLOUDFLARE_API_TOKEN=... npx wrangler@4 deploy --config static-worker/wrangler.toml
```

线上 URL：<https://jr-delay.suki.ing/data/delays.json>

## 运维

- 仓库需要 GitHub Secret：`CF_API_TOKEN`（缺失时 workflow 跳过部署并告警）。
- wrangler 按内容哈希去重上传：数据未变化时秒级跳过，不产生部署成本。
