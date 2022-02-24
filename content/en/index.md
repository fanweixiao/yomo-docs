---
title: YoMo
position: 1
category: Overview
---

YoMo is an open-source Streaming Serverless Framework for building low-latency edge computing applications. Built atop QUIC transport protocol and functional reactive programming interface, makes build geo-distributed system quickly. Used in Metaverse, real-time web applications, AR/VR, IoT etc.

## 🌶 Features

- ⚡️ **Low-latency** Guaranteed by implementing atop of [QUIC](https://datatracker.ietf.org/wg/quic/documents/)
- 🔐 **Security** TLS v1.3 on every data packet by design
- 📱 **5G/WiFi-6** Reliable networking in Celluar/Wireless
- 🌎 **Geo-Distributed Edge Mesh** Edge-Mesh Native architecture makes your services close to end users
- 📸 **Event-First** Architecture leverages serverless service to be event driven and elastic
- 🦖 **Streaming Serverless** Write only a few lines of code to build applications and microservices
- 🚀 **Y3** a [faster than real-time codec](https://github.com/yomorun/y3-codec-golang)
- 📨 **Reactive** stream processing based on [Rx](http://reactivex.io/documentation/operators.html)

## 🚀 Getting Started

### Prerequisite

[Install Go](https://golang.org/doc/install)

### 1. Install CLI

#### Binary (Recommended)

```bash
$ curl -fsSL "https://bina.egoist.sh/yomorun/cli?name=yomo" | sh
```

#### Or build from source

```bash
$ go install github.com/yomorun/cli/yomo@latest
```

#### Verify Your YoMo CLI Installation

Use the following command to verify that the installation was successful:

```bash
$ yomo version
```

<div>
	<span><span style="font-weight:bold;">Quiz Time: </span>What do you see?</span>
	<br>
    <input type="radio" name="x">
	<label>YoMo CLI version: v0.1.7</label>
	<span style="color:lightgreen;"> - You're good to go!</span>
	<br>
	<input type="radio" name="x">
	<label>Something else</label>
	<span style="color:lightcoral;"> - Please make sure you have installed the latest version!</span>
	<br>
	<input type="radio" name="x" checked style="display:none;"></input>
</div>

#### Create a YoMo App

The `yomo init` command should set up everything for you.

```bash
$ yomo init yomo-app-demo

⌛  Initializing the Serverless app...
✅  Congratulations! You have initialized the serverless function successfully.
ℹ️   You can enjoy the YoMo Serverless via the command:
ℹ️   	DEV: 	yomo dev -n Noise yomo-app-demo/app.go
ℹ️   	PROD: 	First run source application, eg: go run example/source/main.go
		Second: yomo run -n yomo-app-demo yomo-app-demo/app.go

$ cd yomo-app-demo
```

YoMo CLI will automatically create an `app.go` with the following content:

```go
package main

import (
	"context"
	"encoding/json"
	"fmt"
	"time"

	"github.com/yomorun/yomo/rx"
)

// NoiseData represents the structure of data
type NoiseData struct {
	Noise float32 `json:"noise"` // Noise value
	Time  int64   `json:"time"`  // Timestamp (ms)
	From  string  `json:"from"`  // Source IP
}

var echo = func(_ context.Context, i interface{}) (interface{}, error) {
	value := i.(*NoiseData)
	value.Noise = value.Noise / 10
	rightNow := time.Now().UnixNano() / int64(time.Millisecond)
	fmt.Println(fmt.Sprintf("[%s] %d > value: %f ⚡️=%dms", value.From, value.Time, value.Noise, rightNow-value.Time))
	return value.Noise, nil
}

// Handler will handle data in Rx way
func Handler(rxstream rx.Stream) rx.Stream {
	stream := rxstream.
		Unmarshal(json.Unmarshal, func() interface{} { return &NoiseData{} }).
		Debounce(50).
		Map(echo).
		StdOut()

	return stream
}

func DataID() []byte {
	return []byte{0x33}
}
```

### 3. Build and Run

Run `yomo dev` from the terminal. you will see the following message:

```sh
$ yomo dev

ℹ️   YoMo serverless function file: app.go
⌛  Create YoMo serverless instance...
⌛  YoMo serverless function building...
✅  Success! YoMo serverless function build.
ℹ️   YoMo serverless function is running...
ℹ️   Run: /Users/xiaojianhong/Downloads/yomo-app-demo/sl.yomo
2021/06/07 12:00:06 Connecting to zipper dev.yomo.run:9000 ...
2021/06/07 12:00:07 ✅ Connected to zipper dev.yomo.run:9000
[10.10.79.50] 1623038407236 > value: 1.919251 ⚡️=-25ms
[StdOut]:  1.9192511
[10.10.79.50] 1623038407336 > value: 11.370256 ⚡️=-25ms
[StdOut]:  11.370256
[10.10.79.50] 1623038407436 > value: 8.672209 ⚡️=-25ms
[StdOut]:  8.672209
[10.10.79.50] 1623038407536 > value: 4.826996 ⚡️=-25ms
[StdOut]:  4.826996
[10.10.79.50] 1623038407636 > value: 16.201773 ⚡️=-25ms
[StdOut]:  16.201773
[10.10.79.50] 1623038407737 > value: 13.875483 ⚡️=-26ms
[StdOut]:  13.875483

```

## 🌟 Show case

- [Metaverse Workplace frontend - Next.js v12](https://github.com/yomorun/yomo-metaverse-workplace-nextjs)
- [Metaverse Workplace backend](https://github.com/yomorun/yomo-vhq-backend)

## 📚 Examples

All examples can be run by [Task](https://taskfile.dev), following the [Install Task](https://taskfile.dev/#/installation), execute `task -l` in this directory will list all the examples.

```bash
$ cd example
$ task -l |grep example

* example-basic: 			YoMo basic usage
* example-pipeline: 			Unix pipeline to cloud
* example-iopipe: 			IO Pipe
* example-multi-sfn: 			Multiple stream functions
* example-cascading-zipper: 		Cascading zippers
```

can run each example directly by `task example-basic`, `task example-cascading-zipper` and etc.

- [0-basic](https://github.com/yomorun/yomo/tree/master/example/0-basic): process the streams from IoT sound sensor.
- [1-pipeline](https://github.com/yomorun/yomo/tree/master/example/1-pipeline): read the local streams (f.e. `/dev/urandom`) and use [yomo-source](https://docs.yomo.run/source) to send the streams over cloud.
- [2-iopipe](https://github.com/yomorun/yomo/tree/master/example/2-iopipe): use `io.Copy()` to pipe the local streams (f.e. `/dev/urandom`) to [yomo-source](https://docs.yomo.run/source).
- [3-multi-sfn](https://github.com/yomorun/yomo/tree/master/example/3-multi-sfn): use 3 stream functions to process the streams in different cases.
  - stream-fn-1: calculate the sound value in real-time.
  - stream-fn-2: print the warning message when the sound value reaches a threshold.
  - stream-fn-3: calculate the average value in a sliding window.
- [4-cascading-zipper](https://github.com/yomorun/yomo/tree/master/example/4-cascading-zipper): [source](https://docs.yomo.run/source) connect to [zipper-1](https://docs.yomo.run/zipper), then [zipper-1](https://docs.yomo.run/zipper) will auto sync streams to the zippers in other regions.

## 🎯 Focuses on computing at the edge

YoMo is best for:

- Making latency-sensitive applications.
- Dealing with high network latency and packet loss.
- Handling continuous high-frequency data with stream processing.
- Building complex systems with streaming serverless architecture.

## 🦸 Contributing

First off, thank you for considering making contributions. It's people like you that make YoMo better. There are many ways in which you can participate in this project, for example:

- File a [bug report](https://github.com/yomorun/yomo/issues/new?assignees=&labels=bug&template=bug_report.md&title=%5BBUG%5D). Be sure to include information like what version of YoMo you are using, what your operating system is, and steps to recreate the bug.
- Suggest a new feature.
- Read our [contributing guidelines](https://github.com/yomorun/yomo/blob/master/CONTRIBUTING.md) to learn about what types of contributions we are looking for.
- We have also adopted a [code of conduct](https://github.com/yomorun/yomo/blob/master/CODE_OF_CONDUCT.md) that we expect project participants to adhere to.

## 🤹🏻‍♀️ Feedback

If you have any questions, please feel free to come to our [discussion board](https://github.com/yomorun/yomo/discussions). Any feedback would be greatly appreciated!

## License

[Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)

## Thanks

[YoMo](https://github.com/yomorun/yomo) ❤️ [Vercel](https://vercel.com/?utm_source=yomorun&utm_campaign=oss) This website is

[![Vercel Logo](/vercel.svg)](https://vercel.com/?utm_source=yomorun&utm_campaign=oss)
