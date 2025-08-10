# 通知系统监控API文档

## 📊 新增监控功能概览

本次更新为校园交易系统的通知模块添加了完整的邮件和SignalR监控功能，提供企业级的监控能力。

## 🔔 新增API接口

### 1. 邮件发送统计监控
**接口:** `GET /api/notification/email-stats`
**功能:** 获取邮件发送成功率和详细统计
**参数:**
- `startTime` (可选): 开始时间 (格式: yyyy-MM-dd HH:mm:ss)
- `endTime` (可选): 结束时间 (格式: yyyy-MM-dd HH:mm:ss)

**响应示例:**
```json
{
  "success": true,
  "data": {
    "successRate": 92.5,
    "totalSent": 1250,
    "successful": 1156,
    "failed": 94,
    "pending": 15,
    "period": {
      "startTime": "2025-08-09 00:00:00",
      "endTime": "2025-08-10 23:59:59"
    }
  },
  "message": "邮件发送成功率: 92.50%"
}
```

### 2. SignalR发送统计监控
**接口:** `GET /api/notification/signalr-stats`
**功能:** 获取SignalR发送成功率和详细统计
**参数:** 同邮件统计接口

**响应示例:**
```json
{
  "success": true,
  "data": {
    "successRate": 96.8,
    "totalSent": 1580,
    "successful": 1529,
    "failed": 51,
    "pending": 8,
    "period": {
      "startTime": null,
      "endTime": null
    }
  },
  "message": "SignalR发送成功率: 96.80%"
}
```

### 3. 邮件失败原因分析
**接口:** `GET /api/notification/email-failure-analysis`
**功能:** 分析邮件发送失败的具体原因和时间趋势
**参数:**
- `topN` (可选): 返回前N个失败原因，默认10个

**响应示例:**
```json
{
  "success": true,
  "data": {
    "failureReasons": {
      "SMTP服务器连接超时": 25,
      "邮箱地址格式错误": 18,
      "收件人邮箱不存在": 12,
      "邮件内容被拒绝": 8
    },
    "hourlyTrend": {
      "0": { "Success": 45, "Failed": 2 },
      "1": { "Success": 38, "Failed": 1 },
      "8": { "Success": 156, "Failed": 8 },
      "9": { "Success": 198, "Failed": 12 }
    },
    "analysisTime": "2025-08-10T12:00:00Z"
  },
  "message": "获取邮件失败分析成功，共分析 4 种失败原因"
}
```

### 4. SignalR失败原因分析
**接口:** `GET /api/notification/signalr-failure-analysis`
**功能:** 分析SignalR发送失败的具体原因和时间趋势
**参数:** 同邮件失败分析

### 5. 通知系统健康监控
**接口:** `GET /api/notification/health`
**功能:** 获取通知系统的整体健康状况
**响应示例:**
```json
{
  "success": true,
  "data": {
    "overallHealth": 94.5,
    "healthStatus": "良好",
    "timestamp": "2025-08-10T12:00:00Z",
    "systems": {
      "notification": {
        "successRate": 93.2,
        "pending": 25,
        "failed": 15,
        "status": "健康"
      },
      "email": {
        "successRate": 92.5,
        "pending": 8,
        "failed": 12,
        "status": "健康"
      },
      "signalR": {
        "successRate": 96.8,
        "pending": 3,
        "failed": 5,
        "status": "健康"
      }
    }
  },
  "message": "通知系统整体状况: 良好"
}
```

### 6. 综合监控面板
**接口:** `GET /api/notification/monitoring-dashboard`
**功能:** 提供完整的监控面板数据，包含系统概览、成功率统计、队列状态、失败分析、趋势数据和系统建议

**响应示例:**
```json
{
  "success": true,
  "data": {
    "systemOverview": {
      "overallHealth": 94.5,
      "healthStatus": "良好",
      "timestamp": "2025-08-10T12:00:00Z",
      "totalNotifications": 2850,
      "totalEmails": 1250,
      "totalSignalR": 1580
    },
    "successRates": {
      "notification": 93.2,
      "email": 92.5,
      "signalR": 96.8
    },
    "queueStatus": {
      "notification": {
        "pending": 25,
        "success": 2658,
        "failed": 167,
        "total": 2850,
        "status": "正常"
      },
      "email": {...},
      "signalR": {...}
    },
    "failureAnalysis": {
      "email": {
        "topFailureReasons": {...},
        "totalFailures": 94
      },
      "signalR": {...}
    },
    "hourlyTrends": {
      "email": {...},
      "signalR": {...}
    },
    "recommendations": [
      "✅ 系统运行状态良好，继续保持当前配置"
    ]
  },
  "message": "通知系统监控面板获取成功，系统状态: 良好"
}
```

## 🎯 监控指标说明

### 成功率计算
- **通知成功率** = 成功发送的通知数 / 总通知数 × 100%
- **邮件成功率** = 成功发送的邮件数 / 总邮件数 × 100%
- **SignalR成功率** = 成功发送的SignalR消息数 / 总SignalR消息数 × 100%

### 健康状态评级
- **优秀**: 成功率 ≥ 95%
- **良好**: 成功率 ≥ 90%
- **一般**: 成功率 ≥ 80%
- **警告**: 成功率 ≥ 70%
- **严重**: 成功率 < 70%

### 队列状态判断
- **正常**: 积压量在正常范围内
- **警告**: 积压量较高或失败率偏高
- **积压**: 队列积压严重，需要处理

## 🔧 新增服务方法

### NotifiSenderService 新增方法
- `GetEmailStatsAsync()`: 获取邮件统计
- `GetSignalRStatsAsync()`: 获取SignalR统计
- `GetEmailStatsAsync(startTime, endTime)`: 获取时间范围内邮件统计
- `GetSignalRStatsAsync(startTime, endTime)`: 获取时间范围内SignalR统计
- `GetEmailFailureReasonsAsync(topN)`: 获取邮件失败原因统计
- `GetSignalRFailureReasonsAsync(topN)`: 获取SignalR失败原因统计
- `GetHourlyEmailTrendAsync(days)`: 获取邮件每小时趋势
- `GetHourlySignalRTrendAsync(days)`: 获取SignalR每小时趋势

### EmailService 新增方法
- `GetEmailStatsAsync(startTime, endTime)`: 时间范围邮件统计
- `GetEmailFailureReasonsAsync(topN)`: 邮件失败原因分析
- `GetHourlyEmailTrendAsync(days)`: 邮件发送趋势分析

### SignalRNotificationService 新增方法
- `GetSignalRStatsAsync(startTime, endTime)`: 时间范围SignalR统计
- `GetSignalRFailureReasonsAsync(topN)`: SignalR失败原因分析
- `GetHourlySignalRTrendAsync(days)`: SignalR发送趋势分析

## 📱 使用场景

### 1. 运维监控
- 实时监控通知系统健康状况
- 及时发现和处理系统异常
- 分析发送失败原因，优化系统配置

### 2. 性能分析
- 分析邮件和SignalR的发送成功率
- 监控队列积压情况
- 查看不同时间段的发送趋势

### 3. 故障排查
- 快速定位发送失败的具体原因
- 分析失败模式，制定解决方案
- 监控修复效果

### 4. 管理决策
- 基于数据制定系统优化策略
- 评估通知系统的服务质量
- 预测和预防潜在问题

## 🎉 功能亮点

1. **完整的成功率监控**: 支持实时和历史成功率统计
2. **深度失败分析**: 提供具体失败原因和趋势分析
3. **智能健康评估**: 自动评估系统健康状况并提供建议
4. **灵活的时间范围**: 支持自定义时间范围查询
5. **综合监控面板**: 一站式监控视图，方便运维管理
6. **企业级监控**: 提供专业的监控指标和建议系统

这套监控系统为校园交易平台的通知模块提供了全面的可观测性，确保通知服务的高可用性和可靠性。
