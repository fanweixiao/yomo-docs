---
title: Multiple Keys
position: 10
category: Examples
---

> Source code: https://github.com/yomorun/yomo/tree/master/example/multi-keys

This example represents how to observe multiple keys and zip the data in batch for calculation.

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

## Code structure

- `source`: sending sequential numbers in 5 different keys [/source](/source)
- `flow`: combine multiple numbers from 5 keys for calculation [/stream-fn](/stream-fn)
- `zipper`: setup a workflow that receives multiple keys and completes the merge calculation [/zipper](/zipper)

## How to run the example

### 1. Install YoMo CLI

Please visit [YoMo Getting Started](https://github.com/yomorun/yomo#1-install-cli) for details.

### 2. Run [yomo-zipper](/zipper)

```bash
yomo serve -c ./zipper/workflow.yaml

2021/05/20 15:34:23 Found 1 flows in zipper config
2021/05/20 15:34:23 Flow 1: training
2021/05/20 15:34:23 Found 0 sinks in zipper config
2021/05/20 15:34:23 Running YoMo workflow...
2021/05/20 15:34:23 ✅ Listening on 0.0.0.0:9000
```

### 3. Run [yomo-flow](/stream-fn)

```bash
yomo run ./stream-fn/app.go -n training

2021/05/20 15:35:22 Building the Serverless Function File...
2021/05/20 15:35:25 Connecting to zipper localhost:9000 ...
2021/05/20 15:35:25 ✅ Connected to zipper localhost:9000
2021/05/20 15:35:25 Running the Serverless Function.
```

### 4. Run [yomo-source](/source)

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

### Results

#### yomo-flow

The terminal of `yomo-flow` will print the calculation result after mergeing 5 numbers from 5 different keys.

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
