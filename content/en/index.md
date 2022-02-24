---
title: YoMo
position: 1
category: Overview
---

YoMo is an open-source Streaming Serverless Framework for building low-latency edge computing applications. Built atop QUIC transport protocol and functional reactive programming interface, makes build geo-distributed system quickly. Used in Metaverse, real-time web applications, AR/VR, IoT etc.

## Getting Started

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

## Demo

![yomo-arch](/yomo-arch-v0.7.png)

## 🎯 Focuses on computing at the edge

YoMo is best for:

- Making latency-sensitive applications.
- Dealing with high network latency and packet loss.
- Handling continuous high-frequency data with stream processing.
- Building complex systems with streaming serverless architecture.

## Contributing

First off, thank you for considering making contributions. It's people like you that make YoMo better. There are many ways in which you can participate in this project, for example:

- File a [bug report](https://github.com/yomorun/yomo/issues/new?assignees=&labels=bug&template=bug_report.md&title=%5BBUG%5D). Be sure to include information like what version of YoMo you are using, what your operating system is, and steps to recreate the bug.
- Suggest a new feature.
- Read our [contributing guidelines](https://github.com/yomorun/yomo/blob/master/CONTRIBUTING.md) to learn about what types of contributions we are looking for.
- We have also adopted a [code of conduct](https://github.com/yomorun/yomo/blob/master/CODE_OF_CONDUCT.md) that we expect project participants to adhere to.

## Feedback

If you have any questions, please feel free to come to our [discussion board](https://github.com/yomorun/yomo/discussions). Any feedback would be greatly appreciated!

## License

[Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)

## Thanks

[YoMo](https://github.com/yomorun/yomo) ❤️ [Vercel](https://vercel.com/?utm_source=yomorun&utm_campaign=oss) This website is

[![Vercel Logo](https://docs.yomo.run/vercel.svg)](https://vercel.com/?utm_source=yomorun&utm_campaign=oss)
