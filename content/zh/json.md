---
title: JSON Codec
position: 8
category: Examples
---

> 源代码: https://github.com/yomorun/yomo/tree/master/example/json

这个例子展示了如何在 YoMo 中使用 JSON Codec。

1. 在 [source](/source) 中，使用 `json.Marshal(data)` 来通过 JSON 编码数据。

```go
// Encode data via JSON.
sendingBuf, _ := json.Marshal(data)

// send data to YoMo-Zipper via QUIC stream.
_, err := stream.Write(sendingBuf)
```

2. 在 [stream-fn](/stream-fn) 中，使用 `Unmarshal` 操作符通过 JSON 解码数据，然后使用 `Marshal` 操作符将数据编码返回到流。

```go
func Handler(rxstream rx.Stream) rx.Stream {
	stream := rxstream.
		Unmarshal(json.Unmarshal, func() interface{} { return &NoiseData{} }).
		Map(computePeek).
		Marshal(json.Marshal)

	return stream
}
```

## 如何运行这个例子

### 1. 安装 YoMo CLI

请访问 [YoMo 入门](https://github.com/yomorun/yomo#1-install-cli)了解详情。

### 2. 运行 [YoMo-Zipper](/zipper)

```bash
yomo serve -c ./zipper/workflow.yaml
```

### 3. 运行 [Stream-Function](/stream-fn)

```bash
yomo run ./stream-fn/app.go -n Noise
```

### 4. 运行 [YoMo-Source](/source)

```bash
go run ./source/main.go
```
