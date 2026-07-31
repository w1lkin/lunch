# 中午吃什么（Lunch）

纯前端单机决策小游戏：帮纠结的你随机决定今天吃什么。

## 单机版特性

- **纯静态**：`index.html`（主游戏）+ `generate-share-card.html`（分享卡工具），零依赖、无构建步骤。
- **无需联网**：随机逻辑全部在浏览器本地运行。
- **数据本地**：可自定义菜单，保存在本机 `localStorage`。
- **分享卡**：独立工具生成分享图（二维码需联网）。

## 本地运行

```sh
cd lunch
python3 -m http.server 8000
# 浏览器打开 http://localhost:8000
```

> 分享卡片二维码依赖在线服务，请用本地服务器方式打开，不要直接 `file://` 打开。

## 文件结构

```
lunch/
├── index.html                    # 主游戏
└── generate-share-card.html      # 分享卡片生成工具
```

## 部署

已部署示例：`lunch-1t7.pages.dev`（Cloudflare Pages）。

## 版本

当前分支：`release/1.0.0`
