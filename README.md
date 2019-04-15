![micro](https://raw.githubusercontent.com/txn2/micro/master/mast.jpg)

WIP: micro is a gin-gonic wrapper for TXN2 components.

Example:

```go
package main

import (
	"flag"

	"github.com/gin-gonic/gin"
	"github.com/txn2/ack"
	"github.com/txn2/micro"
)

func main() {

	test := flag.Bool("test", true, "A test flag")

	server := micro.NewServer()

	if *test {
		server.Router.GET("/test", func(c *gin.Context) {
			ak := ack.Gin(c)

			res, err := server.Client.Http.Get("http://" + server.Cfg.Ip + ":" + server.Cfg.Port + "/healthz")
			if err != nil {
				ak.GinErrorAbort(500, "ClientError", err.Error())
				return
			}

			// should get 401 Unauthorized from healthz
			ak.SetPayloadType("Message")
			ak.GinSend("Got " + res.Status + " from healthz.")
		})
	}

	server.Run()
}
```
