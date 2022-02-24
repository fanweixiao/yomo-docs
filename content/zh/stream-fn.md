---
title: Stream Functions
position: 4
category: Overview
---

为了实时处理连续的高频数据，
YoMo 采用 [函数式响应式编程 (FRP)](https://en.wikipedia.org/wiki/Functional_reactive_programming)，让流计算变得前所未有的简单。
YoMo 使用 [QUIC 传输协议](https://en.wikipedia.org/wiki/QUIC)，
很大程度上提高了数据传输的速度，并将 QUIC 流映射到 Rx 流。
用户可以使用 [流操作符 (stream operators)](http://reactivex.io/documentation/operators.html) 来处理他们想要处理的流:

```go
func Handler(rxstream rx.Stream) rx.Stream {
	stream := rxstream.
		Subscribe(NoiseDataKey).
		Debounce(50).
		OnObserve(decode).
		Map(computePeek).
		SlidingWindowWithTime(SlidingWindowInMS, SlidingTimeInMS, slidingAvg).
		Encode(0x14)

	return stream
}
```

除了将原始数据转换为可用信息之外，
流函数 (stream functions) 还可以在网页上实时展示数据，将流保存到时序数据库，或将排好格式的数据打印到`StdOut`:

```go
func Handler(rxstream rx.Stream) rx.Stream {
	stream := rxstream.
		Subscribe(0x14).
		OnObserve(callback).
		Map(store)
	return stream
}
```

需要注意的几点:

- `zipper` 发送给 `stream function` 的数据流是 immutable 不可变的，`stream function` 只能 append 新数据，不可修改原始 stream。
- `stream function` 的 `Handler` 结尾没有使用 `Encode` operator 时，代表没有 return data，将不会添加新数据到原始 stream。

## 流操作符 (Stream Operators)

大多数 operators 将流作为 input 并返回一个新的流作为 output，
所以它们可以连接在一起。一些 operators 是由 ReactiveX 实现的:

#### [Map](http://reactivex.io/documentation/operators/map.html) - 对 Observable 发出的每个数据 apply 一个函数。

![Map](/stream-fn/map.png)

#### [TakeLast](http://reactivex.io/documentation/operators/takelast.html) - 获取 Observable 发出的最后 n 个数据。

![TakeLast](/stream-fn/takeLast.png)

一些 operators 是 YoMo 独有的:

- `AuditTime`
- `DefaultIfEmptyWithTime`
- `Encode`
- `OnObserve`
- `RawBytes`
- `SlidingWindowWithCount`
- `SlidingWindowWithTime`
- `StdOut`
- `Subscribe`
- `ZipMultiObservers`

## 例子

1. 一个简单的实时噪声监测系统。[链接](https://github.com/yomorun/yomo/tree/next/example/basic)
2. 同时使用多个流函数。[链接](https://github.com/yomorun/yomo/tree/next/example/multi-stream-fn)
