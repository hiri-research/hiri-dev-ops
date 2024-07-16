# 自动化Timeline设计文档(qt?)

## 概述

"自动化Timeline"是一个基于网页的工具，用于实时显示和管理作业轮次及其子系统的信息。它提供了直观的时间线视图，支持实时更新和历史数据查询。

## 页面布局

### 主视图
- 顶部导航栏：包含页面标题和用户信息
- 筛选区域：位于内容区域顶部
- 时间线视图：显示作业轮次和子系统信息
- 分页控件：位于时间线底部

### 筛选区域
- 日期范围选择器
- 子系统筛选下拉菜单
- "应用筛选"按钮
- "重置筛选"按钮

### 时间线项目
每个时间线项目应显示：
- 轮次时间戳
- 轮次ID
- 子系统状态概览（使用图标或颜色编码）
- "展开详情"按钮

## 功能规范

### 实时更新
- 页面打开时默认显示最新的10个轮次
- 使用WebSocket接收并显示新完成的子系统信息
- 提供手动刷新按钮

### 历史数据查询
- 用户可选择特定日期范围进行查询
- 支持按子系统筛选
- 查询结果应分页显示，每页显示10个轮次

### 详细信息展示
- 点击"展开详情"按钮显示该轮次的所有子系统详细信息
- 详细信息包括：子系统名称、状态、完成时间、执行结果

### 性能优化
- 实现虚拟滚动以处理大量轮次数据
- 使用缓存减少对后端的请求

## 技术考虑

### 前端
- 框架：Vue.js
- UI组件库：Element Plus
- 状态管理：Vuex
- 时间线组件：自定义或使用如vue-timeline的库

### 后端
- 框架：Django
- 实时通信：Django Channels (WebSocket)
- 数据库：PostgreSQL

### API设计
- GET /api/rounds/: 获取轮次列表
- GET /api/rounds/{round_id}/: 获取特定轮次详情
- WebSocket连接：/ws/timeline/: 接收实时更新

## 数据模型

### 轮次 (Round)
```python
class Round:
    id: int
    timestamp: datetime
    status: str  # 'completed', 'in_progress', 'failed'
```

### 子系统 (Subsystem)
```python
class Subsystem:
    id: int
    round_id: int
    name: str
    status: str  # 'completed', 'in_progress', 'failed'
    completion_time: datetime
    result: str
```

## 安全性
- 实现用户认证和授权
- 使用HTTPS加密所有通信
- 实施适当的输入验证和消毒

## 未来扩展
- 添加详细的性能分析视图
- 实现自定义告警设置
- 集成with其他运维工具

## TODO项
- 明确定义"轮次"的概念
- 确定并列出所有相关的子系统
- 设计子系统状态和结果的标准化格式