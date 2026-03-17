# 接口设计 (API Design)

本文档定义了系统的核心 API 接口。

## 认证接口 (Authentication)

| 路径 | 方法 | 说明 |
| :--- | :--- | :--- |
| `/api/auth/[...nextauth]` | ALL | NextAuth 认证流 (GitHub/Email) |

## 交易与订单 (Trade & Orders)

### 1. 创建结算会话
- **路径**: `/api/checkout`
- **方法**: `POST`
- **认证**: 需要 Session 或 ApiKey
- **参数**: `product_id`, `amount`, `currency`
- **返回**: `session_id`, `order_no`

### 2. 结算回调
- **路径**: `/api/settlement-notify`
- **方法**: `POST`
- **参数**: `provider` (query param, e.g., stripe)
- **认证**: 基于供应商签名的校验
- **功能**: 统一接收不同支付商的回调，处理订单状态更新与积分发放。

## 用户与积分 (User & Credits)

### 1. 获取用户信息
- **路径**: `/api/get-user-info`
- **方法**: `POST`
- **认证**: 需要 Session

### 2. 获取用户积分
- **路径**: `/api/get-user-credits`
- **方法**: `POST`
- **参数**: `user_uuid` (可选，默认当前用户)
- **返回**: `left_credits`, `is_pro`

## 内容与反馈 (Content & Feedback)

### 1. 提交反馈
- **路径**: `/api/add-feedback`
- **方法**: `POST`
- **参数**: `content`, `rating`

### 2. 文章列表 (内部/Admin)
- **路径**: `/api/admin/posts`
- **方法**: `GET/POST`

### 3. 系统统计指标 (Admin Metrics)
- **路径**: `/api/admin/metrics`
- **方法**: `POST`
- **返回**: 聚合的用户、订单、GMV 及积分统计数据。

## 安全性约束
1. **认证校验**: 除公共接口外，所有 `/api/*` 需校验 Session 或 ApiKey。
2. **频率限制**: API 接口需实现 Rate Limiting。
3. **错误处理**: 统一返回结构 `{ "success": boolean, "message": string, "data": any }`。
