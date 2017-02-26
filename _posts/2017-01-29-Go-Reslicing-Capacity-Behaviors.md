---
layout: post
title: "Go Resliceing Capacity Behaviors"
date: 2017-01-29
---

In Go, Slice is a flexiable data type for you to keep an array of data, that you don't need to worry about the shortage of capacity. Well, you still need to worry about the cost of capacity when your data shrinks. 

One of the comman pitfall is that, if you initiate a slice with a capacity, like s1 (cap == 3), then if you reslice s1 using index, and assign it to a variable s2, the capacity won't change, according to many blogs, like [50 shades of GO](http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang/index.html#slice_hidden_data). (*This blog is highly recommanded if you haven't read it. It covers almost everything you need to know about golang gotchas.*)

We may take a deeper look at the behavior with consideration of where the slice pointer is, when talking about capacity.
Try the following code for instance: 

```go
package main

import (
	"fmt"
)

func main() {
	s1 := []int{1, 2, 3, 4, 5}
	fmt.Println(len(s1), cap(s1), s1, &s1[0], &s1[1], &s1[2]) //print: 5 5 [1 2 3 4 5] 0x104401a0 0x104401a4 0x104401a8 0x104401ac 0x104401b0
	s2 := s1[:2]
	fmt.Println(len(s2), cap(s2), s2, &s2[0]) //print: 2 5 [1 2] 0x104401a0
	s3 := s1[3:]
	fmt.Println(len(s3), cap(s3), s3, &s3[0]) //print: 2 2 [4 5] 0x104401ac
	s4 := s1[1:3]
	fmt.Println(len(s4), cap(s4), s4, &s4[0]) //print: 2 4 [2 3] 0x104401a4

}
```

Note that s2 is still have a hidden capacity as we expected. However, s3 and s4 already shrink the capacity itself. And only removed the beginning portion of the allocated capacity, since the pointer moved towards the end.

Not sure if going forward the language would add the auto shrink feature or not, at the time writing, this is the behavior of `go version go1.7.4 darwin/amd64`.
