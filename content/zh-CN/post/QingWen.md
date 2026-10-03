---
title: 'QingWen'
banner: "/Pho/06.webp"
cover: "/Pho/06.webp"
date: 2026-09-20
categories:
  - Agent
tags:
  - 项目
  - 学习
  - 记录
  - 开发
  - AI
toc: true
---

## QingWen1.0
```
# 启动后端
conda activate agent
cd D:\pycharm\PythonProject2\QIngWen
uvicorn server:app --reload --port 8000

# Python静态服务器
conda activate agent
cd D:\pycharm\PythonProject2\QIngWen
python -m http.server 5500

```


## 回答的还是很搞笑的需要在做一下迭代


## QingWen启动与测试
### 启动
```bash
#切换环境
conda activate agent
#启动服务
uvicorn server:app --reload --port 8000
#进入
redis-cli
```
### 测试（感觉有点懵需要恶补FastAPI了）
>post /chat -> Try it out  -> Request body {"message": "我想减肥"} -> Execute -> 下方 Response body 复制返回的 session_id
-> Request body {"session_id": "刚才复制的那串","message": "172，68公斤，28岁，男的"} -> Execute -> Request body 
{"session_id": "同一串","message": "一周练3天，不吃辣"} -> 下方 Response body stage 变成 "done"
 
## Redis常用命令查询方式

