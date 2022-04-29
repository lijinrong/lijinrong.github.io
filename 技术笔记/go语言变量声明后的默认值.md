---
title: go 语言变量声明后的默认值
categories:
  - golang
tags:
  - golang
---

在 go 语言中，任何类型在声明后没有赋值的情况下，都对应一个零值。
整形如 int8、byte、int16、uint、uintprt 等，默认值为 0。
浮点类型如 float32、float64，默认值为 0。
布尔类型 bool 的默认值为 false。
复数类型如 complex64、complex128，默认值为 0+0i。
字符串 string 的默认值为”“。
错误类型 error 的默认值为 nil。
对于一些复合类型，如指针、切片、字典、通道、接口，默认值为 nil。而数组的默认值要根据其数据类型来确定。例如：var a [4]int，其默认值为[0 0 0 0]。
