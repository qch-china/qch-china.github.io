---
title: "Golang Slice的过滤/聚合/映射函数"
date: 2023-10-12T16:05:12+08:00
description: "Golang Slice的过滤/聚合/映射函数"
tags: [golang]
categories: "程序开发"
comment : false
---

## 一：前言
自从Golang 1.18支持泛型以来，编写泛型算法就变得方便了

下面的代码展示了如何实现Golang Slice的过滤/聚合/映射函数。代码很简单，就不多做解释了


## 二：函数与调用方式
```go
package main

import (
	"fmt"
)

// SliceFilter 数组过滤
func SliceFilter[T any](list []T, filterFun func(T) bool) []T {
	index := 0
	for _, item := range list {
		ok := filterFun(item)
		if ok {
			list[index] = item
			index++
		}
	}
	return list[:index]
}

// SliceReduce 数组聚合
func SliceReduce[T any, M any](list []T, reduceFun func(M, T) M, initValue M) M {
	curr := initValue
	for _, item := range list {
		curr = reduceFun(curr, item)
	}
	return curr
}

// SliceMap 数组遍历执行mapFun，然后生成新的数组
func SliceMap[T any, R any](list []T, mapFun func(T) R) []R {
	result := make([]R, len(list))
	for index, item := range list {
		result[index] = mapFun(item)
	}
	return result
}

func main() {
	// 求出数组中的偶数
	filterSlice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
	filterResult := SliceFilter(filterSlice, func(num int) bool {
		return num%2 == 0
	})
	fmt.Printf("SliceFilter: %v\n", filterResult)

	// 数组求和
	reduceSlice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
	reduceResult := SliceReduce(reduceSlice, func(curr int, num int) int {
		return curr + num
	}, 0)
	fmt.Printf("reduceResult: %v\n", reduceResult)

	// 数组元素*2
	mapSlice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
	mapResult := SliceMap(mapSlice, func(num int) int {
		return num * 2
	})
	fmt.Printf("mapResult: %v\n", mapResult)
}


```

## 三：备注
本函数的实现没有考虑处理函数中出错的可能性，如果处理函数需要返回error的话请自行加上（业务上这种情况比较少见，加入error处理反而会添加负担，所以就没有在这个版本中加入）
