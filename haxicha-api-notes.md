# haxicha.com 接口抓取说明

本文记录我从 `https://haxicha.com/` 和 `https://haxicha.com/wallet/search?address=...` 页面抓到的可用接口与页面字段，方便后续参考实现你自己的 API。

## 1. 站内页面

### 1.1 查询页

- 地址：`GET https://haxicha.com/wallet/search?address={wallet_address}`
- 作用：展示某个地址的资产摘要、交易列表、统计信息。
- 备注：页面本身是服务端渲染的，部分摘要字段直接写在 HTML 里，不完全依赖 JSON API。

### 1.2 交易详情页

- 地址：`GET https://haxicha.com/transaction/{hash}`
- 作用：站内交易详情页，交易列表中的 hash 会跳转到这里。

## 2. 抓到的 JSON API

### 2.1 获取交易记录

- 接口：`GET /api/wallet/transactions`
- 完整地址：`https://haxicha.com/api/wallet/transactions`

#### 查询参数

- `address`：钱包地址，必填
- `token`：币种，当前页面默认 `USDT`
- `direction`：筛选方向，页面默认 `全部`
- `min_amount`：最小金额，可选
- `max_amount`：最大金额，可选
- `start_date`：开始日期，格式 `YYYY-MM-DD`，可选
- `end_date`：结束日期，格式 `YYYY-MM-DD`，可选
- `page`：页码，默认 `1`
- `_t`：时间戳防缓存参数

#### 返回示例

```json
{
  "code": 1,
  "msg": "成功",
  "data": {
    "transactionsList": [
      {
        "hash": "52f1d8699a311763de582e79df81ab9ede6a82d7285a0b72f8b4c5e8828b00f8",
        "from_address": "TPvN1UnH9TcDXHxtTz1Gr7RD7UUsiwj4dG",
        "to_address": "TDU1i8f5WrQnmYCfXMcjKTyiQAWHrEEw3J",
        "amount": 31.918008,
        "token": "USDT",
        "timestamp": 1782925569,
        "block_hash": "",
        "block_no": 84085269,
        "token_decimals": "6",
        "confirmations": 1,
        "type": "in",
        "formatted_time": "2026-07-02 01:06:09"
      }
    ],
    "pagination": {
      "current_page": 1,
      "has_more": false
    }
  }
}
```

#### 字段说明

- `code=1` 表示成功
- `transactionsList` 是交易数组
- `pagination.current_page` 当前页
- `pagination.has_more` 是否还有下一页

### 2.2 获取统计数据

- 接口：`GET /api/wallet/statistics`
- 完整地址：`https://haxicha.com/api/wallet/statistics`

#### 查询参数

- `address`：钱包地址，必填
- `token`：币种，当前页面默认 `USDT`
- `start_date`：开始日期，可选
- `end_date`：结束日期，可选
- `min_amount`：最小金额，可选
- `max_amount`：最大金额，可选

#### 返回示例

```json
{
  "success": true,
  "message": "获取统计数据成功",
  "data": {
    "income": {
      "count": 1,
      "amount": 31.918008
    },
    "expense": {
      "count": 0,
      "amount": 0
    }
  }
}
```

#### 字段说明

- `success=true` 表示成功
- `income.count` 收入笔数
- `income.amount` 收入总额
- `expense.count` 支出笔数
- `expense.amount` 支出总额

## 3. 页面渲染字段

以下字段在查询页 HTML 中直接渲染出来：

- `钱包地址`
- `TRX数量`
- `USDT数量`
- `能量`
- `账户状态`

我实测到的地址 `TDU1i8f5WrQnmYCfXMcjKTyiQAWHrEEw3J` 的页面值是：

- `TRX数量`：`0.000000`
- `USDT数量`：`31.92`
- `能量`：`0`
- `账户状态`：`未激活`

## 4. 适合你自己 API 的仿制思路

如果你后面要自己写一套 API，可以按这个结构设计：

- `GET /api/wallet/transactions`
- `GET /api/wallet/statistics`
- `GET /wallet/search?address=...`

建议保持返回格式稳定：

- 列表接口统一返回 `code + data + pagination`
- 统计接口统一返回 `success + data`
- 地址摘要字段单独做一个 DTO，避免和交易列表耦合

## 5. 备注

- 当前抓到的站内 JSON 接口只有这两个核心接口。
- 页面上的摘要卡片不是独立 JSON API 输出，而是服务端直接渲染。
- 如果要继续深挖，可以再抓 `transaction/{hash}` 的详情页结构。
