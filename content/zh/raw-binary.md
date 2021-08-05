---
title: Raw Binary
position: 7
category: Examples
---

### 例子: 通过 YoMo 传输二进制数据

> 源代码: https://github.com/yomorun/yomo/tree/master/example/raw-binary

#### 1. 启动 YoMo 服务器

```bash
yomo serve -c zipper.yaml
```

#### 2. 将 streaming serverless function 与 YoMo 连接

```bash
yomo run -n cc func.go
```

#### 3. 生成二进制数据

```bash
go run source.go
```
