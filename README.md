你好！
很冒昧用这样的方式来和你沟通，如有打扰请忽略我的提交哈。我是光年实验室（gnlab.com）的HR，在招Golang开发工程师，我们是一个技术型团队，技术氛围非常好。全职和兼职都可以，不过最好是全职，工作地点杭州。
我们公司是做流量增长的，Golang负责开发SAAS平台的应用，我们做的很多应用是全新的，工作非常有挑战也很有意思，是国内很多大厂的顾问。
如果有兴趣的话加我微信：13515810775  ，也可以访问 https://gnlab.com/，联系客服转发给HR。
# jq

[![GoDoc](https://godoc.org/github.com/savaki/jq?status.svg)](https://godoc.org/github.com/savaki/jq)
[![Build Status](https://snap-ci.com/savaki/jq/branch/master/build_image)](https://snap-ci.com/savaki/jq/branch/master)

A high performance Golang implementation of the incredibly useful jq command line tool.

Rather than marshalling json elements into go instances, jq opts to manipulate the json elements as raw []byte.  This
is especially useful for apps that need to handle dynamic json data.

Using jq consists of creation an ```Op``` and then calling ```Apply``` on the ```Op``` to transform one []byte into the 
desired []byte.  Ops may be chained together to form a transformation chain similar to how the command line jq works.   

## Installation

```
go get github.com/savaki/jq
```

## Example

```go
package main

import (
	"fmt"

	"github.com/savaki/jq"
)

func main() {
	op, _ := jq.Parse(".hello")           // create an Op
	data := []byte(`{"hello":"world"}`)   // sample input
	value, _ := op.Apply(data)            // value == '"world"'
	fmt.Println(string(value))
}
```

## Syntax

The initial goal is to support all the selectors the original jq command line supports.

| syntax | meaning|
| :--- | :--- |
| . |  unchanged input |
| .foo |  value at key |
| .foo.bar |  value at nested key |
| .[0] | value at specified element of array | 
| .[0:1] | array of specified elements of array, inclusive |
| .foo.[0] | nested value |

## Examples

### Data
```json
{
  "string": "a",
  "number": 1.23,
  "simple": ["a", "b", "c"],
  "mixed": [
    "a",
    1,
    {"hello":"world"}
  ],
  "object": {
    "first": "joe",
    "array": [1,2,3]
  }
}
```

| syntax | value |
| :--- | :--- |
| .string | "a" |
| .number| 1.23 |
| .simple | ["a", "b", "c"] |
| .simple.[0] | "a" |
| .simple.[0:1] | ["a","b"] |
| .mixed.[1] | 1
| .object.first | "joe" |
| .object.array.[2] | 3 |

## Performance

```
BenchmarkAny-8         	20000000	        80.8 ns/op	       0 B/op	       0 allocs/op
BenchmarkArray-8       	20000000	       108 ns/op	       0 B/op	       0 allocs/op
BenchmarkFindIndex-8   	10000000	       125 ns/op	       0 B/op	       0 allocs/op
BenchmarkFindKey-8     	10000000	       125 ns/op	       0 B/op	       0 allocs/op
BenchmarkFindRange-8   	10000000	       186 ns/op	      16 B/op	       1 allocs/op
BenchmarkNumber-8      	50000000	        28.9 ns/op	       0 B/op	       0 allocs/op
BenchmarkObject-8      	20000000	        98.5 ns/op	       0 B/op	       0 allocs/op
BenchmarkString-8      	30000000	        40.4 ns/op	       0 B/op	       0 allocs/op
```

