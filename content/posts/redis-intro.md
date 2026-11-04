---
title: "Redis 入门指南"
date: 2026-11-04T12:20:00+08:00
draft: false
tags: ["Redis", "中间件", "缓存", "数据库"]
categories: ["中间件", "技术"]
description: "Redis 基础知识与常用命令"
---

## 什么是 Redis？

Redis 是一个开源的内存数据库，常用于：

- ⚡ **缓存** - 加速数据读取
- 📊 **消息队列** - 异步任务处理
- 🔐 **会话存储** - 用户登录状态
- 📈 **排行榜** - 实时排名系统

## 安装 Redis

```bash
# macOS
brew install redis

# 启动服务
brew services start redis

# 连接 Redis
redis-cli
```

## 常用命令

### 字符串操作

```bash
SET key "value"
GET key
INCR counter
```

### Hash 操作

```bash
HSET user name "Zephyr"
HSET user age 25
HGETALL user
```

### List 操作

```bash
LPUSH queue "task1"
RPUSH queue "task2"
LPOP queue
```

## 在代码中使用

```python
import redis

# 连接 Redis
r = redis.Redis(host='localhost', port=6379, db=0)

# 设置缓存
r.set('user:1', '{"name": "Zephyr"}', ex=3600)

# 获取缓存
user = r.get('user:1')
print(user)
```

## 参考链接

- [Redis 官方文档](https://redis.io/docs/)
- [Redis 命令参考](https://redis.io/commands/)
