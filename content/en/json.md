---
title: JSON Codec
position: 8
category: Examples
---

> Source code: https://github.com/yomorun/yomo/tree/master/example/json

This example represents how to use JSON Codec in YoMo.

1. In [source](/source), use `json.Marshal(data)` to encode the data via JSON.

```go
// Encode data via JSON.
sendingBuf, _ := json.Marshal(data)

// send data to YoMo-Zipper via QUIC stream.
_, err := stream.Write(sendingBuf)
```

2. In [stream-fn](/stream-fn), use `Unmarshal` operator to decode the data via JSON, and then use `Marshal` operator to encode the data back to the stream.

```go
func Handler(rxstream rx.Stream) rx.Stream {
	stream := rxstream.
		Unmarshal(json.Unmarshal, func() interface{} { return &NoiseData{} }).
		Map(computePeek).
		Marshal(json.Marshal)

	return stream
}
```

## How to run the example

### 1. Install YoMo CLI

Please visit [YoMo Getting Started](https://github.com/yomorun/yomo#1-install-cli) for details.

### 2. Run [YoMo-Zipper](/zipper)

```bash
yomo serve -c ./zipper/workflow.yaml
```

### 3. Run [Stream-Function](/stream-fn)

```bash
yomo run ./stream-fn/app.go -n Noise
```

### 4. Run [YoMo-Source](/source)

```bash
go run ./source/main.go
```
