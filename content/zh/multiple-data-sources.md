---
title: Multiple Data Sources
position: 9
category: Examples
---

> 源代码: https://github.com/yomorun/yomo/tree/master/example/multi-data-source

## 客户的要求

我们的客户需要在一个从多个数据源产生高频率数据的环境中进行计算。只有当所有来源的数据都到达时，才会进行计算。计算后，计算结果被发送到下一个处理单元，整个过程重复进行。

传统上，在从多个数据源收集异构数据的情况下，开发人员面临着与多线程、并发、竞赛、锁定、缓存等相关的几个问题。因此，开发人员不是在抽象和实现，而是花时间去修复问题。YoMo 解决了这些问题。

```go
var convert = func(v []byte) (interface{}, error) {
	return y3.ToFloat32(v)
}

var zipper = func(_ context.Context, ia interface{}, ib interface{}) (interface{}, error) {
	result := ia.(float32) + ib.(float32)
	return fmt.Sprintf("⚡️ Sum(%s: %f, %s: %f) => Result: %f", "data A", ia.(float32), "data B", ib.(float32), result), nil
}

// 处理两个事件流，并在数据到达时计算出总和
func Handler(rxstream rx.Stream) rx.Stream {
	streamA := rxstream.Subscribe(0x11).OnObserve(convert)
	streamB := rxstream.Subscribe(0x12).OnObserve(convert)

	// Rx Zip 运算符: http://reactivex.io/documentation/operators/zip.html
	stream := streamA.ZipFromIterable(streamB, zipper).StdOut().Encode(0x13)
	return stream
}

```

## 代码结构

+ `source-data-a`: 模拟数据源 A，发送随机的 Float32 数字 [yomo.run/source](https://docs.yomo.run/source)
+ `source-data-b`: 模拟数据源 B，发送随机的 Float32 数字 [yomo.run/source](https://docs.yomo.run/source)
+ `stream-fn` (以前的flow)。结合模拟数据源 A 和 B 进行计算 [yomo.run/stream-fn](https://docs.yomo.run/stream-fn)
+ `zipper`: 设置一个工作流，接收多个源并完成合并计算 [yomo.run/zipper](https://docs.yomo.run/zipper)

## 实施

### 1. 安装 CLI

```bash
$ go install github.com/yomorun/cli/yomo@latest
```

### 2. 启动 `YoMo-Zipper` 来组织流处理工作流

```bash
$ cd ./example/multi-data-source/zipper

$ yomo serve

ℹ️   Found 1 stream functions in YoMo-Zipper config
ℹ️   Stream Function 1: training
ℹ️   Running YoMo Zipper...
2021/03/01 19:05:55 ✅ Listening on 0.0.0.0:9000

```

### 3. 启动 `stream-fn` 来进行流计算

> **注意**: `-n` 标志代表流函数 (stream function) 的名称，应该与 YoMo-Zipper 配置 (workflow.yaml) 中的特定函数相匹配。

```bash
$ cd ./example/multi-data-source/stream-fn

$ yomo run -n training

ℹ️   YoMo Stream Function file: example/multi-data-source/stream-fn/app.go
⌛  Create YoMo Stream Function instance...
ℹ️   Starting YoMo Stream Function instance with Name: Noise. Host: localhost. Port: 9000.
⌛  YoMo Stream Function building...
✅  Success! YoMo Stream Function build.
ℹ️   YoMo Stream Function is running...
2021/03/01 19:05:55 Connecting to YoMo-Zipper localhost:9000 ...
2021/03/01 19:05:55 ✅ Connected to YoMo-Zipper localhost:9000

```

### 4. 运行 `source-data-a`

```bash
$ cd ./example/multi-data-source/source-data-a

$ go run main.go

2021/03/01 17:35:04 ✅ Connected to YoMo-Zipper localhost:9000
2021/03/01 17:35:05 ✅ Emit 123.41881 to YoMo-Zipper

```

### 5. 运行 `source-data-b`

```bash
$ cd ./example/multi-data-source/source-data-b

$ go run main.go

2021/03/01 17:35:04 ✅ Connected to YoMo-Zipper localhost:9000
2021/03/01 17:35:05 ✅ Emit 36.92933 to YoMo-Zipper

```

### 6. `stream-fn` 将有一个持续的输出流

```bash
[StdOut]:  ⚡️ Sum(data A: 89.820206, data B: 1651.740967) => Result: 1741.561157
[StdOut]:  ⚡️ Sum(data A: 17.577374, data B: 619.293457) => Result: 636.870850
[StdOut]:  ⚡️ Sum(data A: 114.736366, data B: 964.614075) => Result: 1079.350464
```

现在，尝试用 `Ctrl-C` 停掉 `source-data-a`，过一段时间再启动，看看 `stream-fn` 的输出情况如何。

### 7. Congrats!

这个问题已经以一种比以前更简单的方式解决了! 

查找[更多 YoMo 使用案例](https://github.com/yomorun/yomo)
