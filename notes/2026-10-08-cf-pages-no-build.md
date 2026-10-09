# Cloudflare Pages 无构建直出的意外之喜

> 2026-10 实战记录 · 主题：静态站部署

## 背景

把一个 Vue 工程仓库整个替换成单文件纯静态 `index.html`（REST API 推送，commit `1ef768b`），
本来按官方文档预期要去 Dashboard 改构建命令、输出目录，结果——

## 发现

推送后 Cloudflare Pages **自动构建成功**，线上直接是新版。翻配置才发现该项目本来就是
「Build command 留空 + 输出目录 `/`」的无构建直出型配置。

## 两个经验

1. **纯静态项目尽量选无构建直出**：仓库内容 = 部署产物，构建配置永远不用改，管道里少一个会坏的环节
2. **判部署成功别靠猜**：GitHub 侧查 `check-runs`（name = Cloudflare Pages）能直接拿到
   status / conclusion / preview URL，比刷新 Dashboard 快得多

## 短评

「推成功即部署完成」这个直觉在无构建配置下是完全成立的，而且是最省心的架构。
代价是放弃了构建期转换（压缩、打包），但对工具类小站完全够用。
