# 中午吃什么（Lunch）

纯前端决策小游戏：帮纠结的你随机决定今天吃什么。

## 特性

- **纯静态**：`index.html`（主游戏）+ `generate-share-card.html`（分享卡工具），零依赖、无构建步骤。
- **无需联网**：随机逻辑全部在浏览器本地运行。
- **数据本地**：自定义菜单保存在本机 `localStorage`。
- **移动端适配**：针对触屏与微信 webview 优化。
- **分享卡片**：独立工具 `generate-share-card.html` 生成分享图（二维码需联网）。

## 本地运行

```sh
cd lunch
python3 -m http.server 8000
# 浏览器打开 http://localhost:8000
```

> 分享卡片的二维码依赖 `api.qrserver.com`，必须经 `http(s)` 来源加载，请用本地服务器方式打开，不要直接 `file://` 打开。

## 文件结构

```
lunch/
├── index.html                    # 主游戏
└── generate-share-card.html      # 分享卡片生成工具
```

## 部署

已部署至 Cloudflare Pages：`lunch-1t7.pages.dev`
