---
title: Zipper
position: 3
category: Overview
---

`zipper` 负责连接由 `source` 产生的连续数据流和[流函数 (stream functions)](/stream-fn)，这些流函数对数据流进行操作。
要为 `zipper` 定义工作流配置 (workflow configuration)，只需要创建一个 `.yaml` 文件 (参见下面的示例)。

YoMo 使用 [QUIC 传输协议](https://en.wikipedia.org/wiki/QUIC)，
大大提高了数据传输的速度，
并支持通过 [y3 codec](https://github.com/yomorun/y3-codec-golang) 对不同数据类型的流进行编码/解码。

![zipper](/zipper/zipper.png)

## 配置并运行 `zipper`

### `workflow.yaml`

您可以在 `.yaml` 文件中为 `zipper` 定义工作流配置。
例如，这是一个简单的实时噪声监测系统的工作流:

```yaml
# zipper/workflow.yaml
name: Service
host: localhost
port: 9000
functions:
  - name: Noise
  - name: MockDB
```

- `name`: `zipper` 的名称。
- `host` 和 `port`: 定义`zipper` 将监听的 `host:port`。
- `functions`: 流函数列表。在这个例子中，`Noise` 函数将监控噪音水平的变化，并在达到某个阈值时打印出警告信息。`MockDB` 将处理完的数据保存到数据库中。

要了解有关 yaml 的更多信息，可以阅读 [Learn X in Y minutes, where X=yaml](https://learnxinyminutes.com/docs/yaml/)。

### 运行 [示例程序](https://github.com/yomorun/yomo/tree/next/example/basic)

#### 1. 安装 YoMo CLI

请参考 [说明](https://docs.yomo.run/)。

#### 2. 运行 `zipper`

```bash
yomo serve -c ./zipper/workflow.yaml

ℹ️   Found 1 stream functions in zipper config
ℹ️   Stream Function 1: Noise
ℹ️   Running YoMo Zipper...
```

#### 3. 运行 `stream-fn`

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
#### 4. 运行 `stream-fn-db`

```bash
go run ./stream-fn-db/app.go -n MockDB

2021/05/20 14:10:29 ✅ Connected to zipper localhost:9000
2021/05/20 14:10:29 Running the Serverless Function.
```

#### 5. 运行 `source`

```bash
go run ./source/main.go

2021/05/20 14:11:00 Connecting to zipper localhost:9000 ...
2021/05/20 14:11:00 ✅ Connected to zipper localhost:9000
2021/05/20 14:11:00 ✅ Emit {99.11785 1621491060031 localhost} to zipper
2021/05/20 14:11:00 ✅ Emit {145.5075 1621491060131 localhost} to zipper
2021/05/20 14:11:00 ✅ Emit {118.27067 1621491060233 localhost} to zipper
2021/05/20 14:11:00 ✅ Emit {56.369446 1621491060335 localhost} to zipper
```

#### 6. 结果

`stream-fn` (即 `Noise`) 监控噪音水平的变化，并在达到某个阈值时打印出警告消息:

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

`stream-fn-db` (即 `MockDB`) 将数据保存到 FaunaDB:

```bash
save `18.71246` to FaunaDB
save `1.0713108` to FaunaDB
save `16.458117` to FaunaDB
save `12.397432` to FaunaDB
save `15.227814` to FaunaDB
save `14.787642` to FaunaDB
save `17.85902` to FaunaDB
```
