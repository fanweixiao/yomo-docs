---
title: Basic Example
position: 6
category: Examples
---

> 源代码: https://github.com/yomorun/yomo/tree/master/example/basic

这个例子展示了 YoMo 是如何使用噪声传感器的模拟数据的。

## 代码结构

+ `source`: 噪声传感器的模拟数据。[yomo.run/source](https://docs.yomo.run/source)
+ `stream-fn` (以前的 flow): 实时检测噪声污染并在达到阈值时打印警告信息。[yomo.run/stream-function](https://docs.yomo.run/stream-function)
+ `stream-fn-db` (以前的 sink): 展示物联网数据的持久性存储。
+ `zipper`: 协调一个工作流，从 `source` 接收数据，在`stream-fn`进行流计算 [yomo.run/zipper](https://docs.yomo.run/zipper)

## 如何运行这个例子

### 1. 安装 YoMo CLI

请访问 [YoMo 入门](https://github.com/yomorun/yomo#1-install-cli)了解详情。

### 2. 运行 [zipper](https://docs.yomo.run/zipper)

```bash
yomo serve -c ./zipper/workflow.yaml

ℹ️   Found 1 stream functions in zipper config
ℹ️   Stream Function 1: Noise
ℹ️   Running YoMo Zipper...
```

### 3. 运行 [stream-function](https://docs.yomo.run/stream-function)

```bash
yomo run ./stream-fn/app.go -n Noise

ℹ️  YoMo Stream Function file: example/basic/stream-fn/app.go
⌛  Create YoMo Stream Function instance...
ℹ️  Starting YoMo Stream Function instance with Name: Noise. Host: localhost. Port: 9000.
⌛  YoMo Stream Function building...
✅  Success! YoMo Stream Function build.
ℹ️  YoMo Stream Function is running...
2021/05/20 14:10:17 ✅ Connected to zipper localhost:9000
2021/05/20 14:10:17 Running the Stream Function.
```

### (可选) 通过 `Go CLI` 运行 `stream-function`

除了通过 `YoMo CLI` 运行 `stream-function`，你还可以通过 `Go CLI` 运行 `stream-function`，这需要在 `app.go` 中增加以下代码:

```go
func main() {
	cli, err := yomo.NewSource(yomo.WithName("Noise")).Connect("localhost", 9000)
	if err != nil {
		log.Print("❌ Connect to zipper failure: ", err)
		return
	}

	defer cli.Close()
	cli.Pipe(Handler)
}
```

你可以在 `stream-fn-via-go-cli` 文件夹中找到这个例子。

### 4. 运行 [stream-fn-db](https://docs.yomo.run/stream-function)

```bash
go run ./stream-fn-db/app.go -n MockDB

2021/05/20 14:10:29 ✅ Connected to zipper localhost:9000
2021/05/20 14:10:29 Running the Serverless Function.
```

### 5. 运行[yomo-source](https://docs.yomo.run/source)

```bash
go run ./source/main.go

2021/05/20 14:11:00 连接到zipper localhost:9000 ...
2021/05/20 14:11:00 ✅连接到zipper localhost:9000
2021/05/20 14:11:00 ✅ Emit {99.11785 1621491060031 localhost} to zipper
2021/05/20 14:11:00 ✅ Emit {145.5075 1621491060131 localhost} to zipper
2021/05/20 14:11:00 ✅ Emit {118.27067 1621491060233 localhost} to zipper
2021/05/20 14:11:00 ✅ Emit {56.369446 1621491060335 localhost} to zipper
```

### 结果

#### stream-function

`stream-function` 的终端将打印实时噪声分贝值，并在数值达到阈值时显示警告。

```bash
[localhost] 1621491060839 > value: 15.714272 ⚡️=1ms
[localhost] 1621491060942 > value: 14.961421 ⚡️=1ms
[localhost] 1621491061043 > value: 18.712460 ⚡️=1ms
❗ value: 18.712460 reaches the threshold 16! 𝚫=2.712460
[localhost] 1621491061146 > value: 1.071311 ⚡️=1ms
[localhost] 1621491061246 > value: 16.458117 ⚡️=1ms
❗ value: 16.458117 reaches the threshold 16! 𝚫=0.458117
🧩 average value in last 10000 ms: 10.918112!
```

#### stream-fn-db

`stream-fn-db` 的终端将打印保存数据到 DB 的信息。

```bash
save `18.71246` to FaunaDB
save `1.0713108` to FaunaDB
save `16.458117` to FaunaDB
save `12.397432` to FaunaDB
save `15.227814` to FaunaDB
save `14.787642` to FaunaDB
save `17.85902` to FaunaDB
```
