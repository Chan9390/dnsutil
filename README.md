[![Go Report Card](https://goreportcard.com/badge/github.com/lixiangzhong/dnsutil)](https://goreportcard.com/report/lixiangzhong/dnsutil)
[![](https://godoc.org/github.com/lixiangzhong/dnsutil?status.svg)](https://godoc.org/github.com/lixiangzhong/dnsutil)

# dnsutil
#### Golang DNS  dig功能库

```sh
go get github.com/lixiangzhong/dnsutil
```


```go
//Normal

package main

import (
	"fmt"
	"github.com/lixiangzhong/dnsutil"
)

func main() {
    var dig dnsutil.Dig 
    dig.SetDNS("8.8.8.8") //or ns.xxx.com 
    a, err := dig.A("google.com")  // dig google.com @8.8.8.8
    fmt.Println(a, err)
}
```


```go
//EDNS0ClientSubnet

package main

import (
	"fmt"
	"github.com/lixiangzhong/dnsutil"
)

func main() {
    var dig dnsutil.Dig
    dig.SetDNS("8.8.8.8") //or ns.xxx.com
    dig.SetEDNS0ClientSubnet("123.123.123.123")   //support edns0clientsubnet
    a, err := dig.A("google.com")  // dig google.com @8.8.8.8 +client=123.123.123.123
    fmt.Println(a, err)
}
```


```go
//Retry

package main

import (
	"fmt"
	"github.com/lixiangzhong/dnsutil"
)

func main() {
    var dig dnsutil.Dig
    dig.Retry=3 //retry  when write or read message return error . defualt 1
    dig.SetDNS("8.8.8.8") //or ns.xxx.com 
    a, err := dig.A("google.com")  // dig google.com @8.8.8.8
    fmt.Println(a, err)
}
```


```go
//dig +trace

package main

import (
	"fmt"
	"github.com/lixiangzhong/dnsutil"
)

func main() {
	domain := "google.com"
	var dig dnsutil.Dig
	rsps, err := dig.Trace(domain)  //dig +trace google.com
	if err != nil {
		fmt.Println(err)
		return
	}
	for _, rsp := range rsps {
		if rsp.Msg.Authoritative {
			for _, answer := range rsp.Msg.Answer {
				fmt.Println(answer)
			}
		}
		for _, ns := range rsp.Msg.Ns {
			fmt.Println(ns)
		}
		fmt.Println("\tReceived from", rsp.Server, rsp.ServerIP)
	}
}
```


```go
//检查是否被污染

package main

import (
	"fmt"
	"github.com/lixiangzhong/dnsutil"
)

func main() {
	polluted, err := dnsutil.IsPolluted("facebook.com")
	if err != nil {
		fmt.Println(err)
		return
	}
	if polluted {
		fmt.Println("被污染,你懂的")
	} else {
		fmt.Println("正常")
	}
}
```