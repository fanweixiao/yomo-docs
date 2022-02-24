---
title: Multiple Keys
position: 10
category: Examples
---

> 源代码: https://github.com/yomorun/yomo/tree/master/example/multi-keys

这个例子展示了如何观察多个 key，并将数据批量压缩以进行计算。

```go
func Handler(rxstream rx.RxStream) rx.RxStream {
	observers := []decoder.KeyObserveFunc{
		{
			Key:       0x10,
			OnObserve: convert,
		},
		{
			Key:       0x11,
			OnObserve: convert,
		},
		{
			Key:       0x12,
			OnObserve: convert,
		},
		{
			Key:       0x13,
			OnObserve: convert,
		},
		{
			Key:       0x14,
			OnObserve: convert,
		},
	}

	return rxstream.
		ZipMultiObservers(observers, zipper).
		StdOut().
		Encode(0x11)
}
```

## 代码结构

- `source`: 发送 5 个不同 key 的连续数字 [/source](/source)
- `flow`: 结合不同 key 的数字进行计算 [/stream-fn](/stream-fn)
- `zipper`: 设置一个工作流，接收多个 key 的数据并完成合并计算 [/zipper](/zipper)

## 如何运行这个例子

### 1. 安装 YoMo CLI

请访问 [YoMo 入门](https://github.com/yomorun/yomo#1-install-cli)了解详情。

### 2. 运行 [yomo-zipper](/zipper)

```bash
yomo serve -c ./zipper/workflow.yaml

2021/05/20 15:34:23 Found 1 flows in zipper config
2021/05/20 15:34:23 Flow 1: training
2021/05/20 15:34:23 Found 0 sinks in zipper config
2021/05/20 15:34:23 Running YoMo workflow...
2021/05/20 15:34:23 ✅ Listening on 0.0.0.0:9000
```

### 3. 运行 [yomo-flow](/stream-fn)

```bash
yomo run ./stream-fn/app.go -n training

2021/05/20 15:35:22 Building the Serverless Function File...
2021/05/20 15:35:25 Connecting to zipper localhost:9000 ...
2021/05/20 15:35:25 ✅ Connected to zipper localhost:9000
2021/05/20 15:35:25 Running the Serverless Function.
```

### 4. 运行[yomo-source](/source)

```bash
go run ./source/main.go

2021/05/20 15:39:04 Connecting to zipper localhost:9000 ...
2021/05/20 15:39:04 ✅ Connected to zipper localhost:9000
2021/05/20 15:39:04 Sent: 1
2021/05/20 15:39:04 Sent: 2
2021/05/20 15:39:04 Sent: 3
2021/05/20 15:39:05 Sent: 4
2021/05/20 15:39:05 Sent: 5
2021/05/20 15:39:05 Sent: 1
2021/05/20 15:39:05 Sent: 2
2021/05/20 15:39:05 Sent: 3
2021/05/20 15:39:05 Sent: 4
2021/05/20 15:39:05 Sent: 5
```

### 结果

#### yomo-flow

`yomo-flow` 的终端将打印出合并 5 个不同 key 的计算结果。

```bash
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
[StdOut]:  Sum ([1 2 3 4 5]), result: 15
```
