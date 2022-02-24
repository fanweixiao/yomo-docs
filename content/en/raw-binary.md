---
title: Unix Pipeline over Cloud
position: 7
category: Examples
---

### Example: Unix Pipeline Over Cloud

> Source code: https://github.com/yomorun/yomo/tree/master/example/1-pipeline

![unix pipeline](https://camo.githubusercontent.com/503f1530b37edae07670bfc02bbd7eb34b406fc2027a59849f6741521d612373/68747470733a2f2f646f63732e796f6d6f2e72756e2f312e352f7468652d6c696e75782d70726f6772616d6d696e672d696e746572666163652e706e67)

#### 1. Start YoMo Server

```bash
yomo serve -c workflow.yaml
```

#### 2. Start the Streaming Function to observe data

```bash
yomo run -n rand serverless/rand.go
```

![yomo example 1](https://camo.githubusercontent.com/1c43d77fdad1e58c5890f03bc1d99ee89e78ac190b25f28aa2ee0958c62b15b8/68747470733a2f2f646f63732e796f6d6f2e72756e2f312e352f322d73666e322e706e67)

#### 3. Start the Source to generate random data and send to Zipper:

```bash
cat /dev/urandom | go run source/pipe.go
```

![yomo example 1](https://camo.githubusercontent.com/19722e58e82b8acc2f5d5bde36c28d52a0584ed4488ef12734e343ec67ed9c68/68747470733a2f2f646f63732e796f6d6f2e72756e2f312e352f332d736f757263652e706e67)
