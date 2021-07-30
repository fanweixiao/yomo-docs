---
title: Raw Binary
position: 7
category: Examples
---

### Example: Transfer binary data over YoMo

> Source code: https://github.com/yomorun/yomo/tree/master/example/raw-binary

#### 1. Start YoMo Server

```bash
yomo serve -c zipper.yaml
```

#### 2. Hook the streaming serverless function to YoMo

```bash
yomo run -n cc func.go
```

#### 3. Generating binary data

```bash
go run source.go
```
