---
title: 'Globex项目拆解（一）'
banner: "/Pho/04.webp"
cover: "/Pho/04.webp"
date: 2026-09-05
categories:
  - 项目
tags:
  - Agent
  - 语言交互
  - 拆解
toc: true
---

## 项目启动
```
# 工程根目录指包含 pyproject.toml 和 frontend/ 的那一层
cd ~/Desktop/globex-agent/globex-agent-main

# 启动后端
uv run python -m uvicorn app.presentation.server:app \
  --host 127.0.0.1 \
  --port 8000 \
  --workers 1

# 启动前端npm --prefix frontend run dev -- \
  --host 127.0.0.1 \
  --port 5173 \
  --strictPort
```