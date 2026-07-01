# 查U单页

基于 [haxicha-api-notes.md](W:\查Uapi\haxicha-api-notes.md) 做的 Cloudflare Pages 静态单页。

## 文件

- 页面入口：`public/index.html`

## 说明

- 交易接口：`https://haxicha.com/api/wallet/transactions`
- 统计接口：`https://haxicha.com/api/wallet/statistics`
- 这两个 JSON 接口允许跨域，所以静态页可以直接调用
- 原站 `TRX 数量 / USDT 数量 / 能量 / 账户状态` 来自服务端渲染 HTML，不是公开跨域 JSON；Pages 纯静态版不展示这部分摘要

## 部署

在 Cloudflare Pages 创建项目时：

1. 连接这个仓库或直接上传当前目录。
2. `Build command` 填 `exit 0`。
3. `Build output directory` 填 `public`。
4. 部署完成后即可直接访问。
