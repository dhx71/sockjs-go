[![Build Status](https://api.travis-ci.org/dhx71/sockjs-go.svg?branch=master)](https://travis-ci.org/dhx71/sockjs-go) 
[![GoDoc](https://godoc.org/github.com/dhx71/sockjs-go/v3/sockjs?status.svg)](https://pkg.go.dev/github.com/dhx71/sockjs-go/v3/sockjs?tab=doc) 
[![Coverage Status](https://coveralls.io/repos/github/dhx71/sockjs-go/badge.svg?branch=master)](https://coveralls.io/github/dhx71/sockjs-go?branch=master)

What is SockJS?
=

SockJS is a JavaScript library (for browsers) that provides a WebSocket-like
object. SockJS gives you a coherent, cross-browser, Javascript API
which creates a low latency, full duplex, cross-domain communication
channel between the browser and the web server, with WebSockets or without.
This necessitates the use of a server, which this is one version of, for GO.


SockJS-Go server library
=

SockJS-Go is a [SockJS](https://github.com/sockjs/sockjs-client) server library written in Go.

For latest **v3** version of `sockjs-go` use:

    github.com/dhx71/sockjs-go/v3/sockjs

For **v2** version of `sockjs-go` use:

    gopkg.in/dhx71/sockjs-go.v2/sockjs

Using version **v1** is not recommended (DEPRECATED)

    gopkg.in/dhx71/sockjs-go.v1/sockjs

Note: using `github.com/dhx71/sockjs-go/sockjs` is not recommended. It exists for backwards compatibility reasons and is not maintained. 

Versioning
-

SockJS-Go project adopted [gopkg.in](http://gopkg.in) approach for versioning. SockJS-Go library details can be found [here](https://gopkg.in/dhx71/sockjs-go.v2/sockjs)

With the introduction of go modules a new version `v3` is developed and maintained in the `master` and has new import part `github.com/dhx71/sockjs-go/v3/sockjs`. 

Example
-

A simple echo sockjs server:


```go
package main

import (
	"log"
	"net/http"

	"github.com/dhx71/sockjs-go/v3/sockjs"
)

func main() {
	handler := sockjs.NewHandler("/echo", sockjs.DefaultOptions, echoHandler)
	log.Fatal(http.ListenAndServe(":8081", handler))
}

func echoHandler(session sockjs.Session) {
	for {
		if msg, err := session.Recv(); err == nil {
			session.Send(msg)
			continue
		}
		break
	}
}
```


SockJS Protocol Tests Status
-
SockJS defines a set of [protocol tests](https://github.com/sockjs/sockjs-protocol) to quarantee a server compatibility with sockjs client library and various browsers. SockJS-Go server library aims to provide full compatibility, however there are couple of tests that don't and probably will never pass due to reasons explained in table below:


| Failing Test | Explanation |
| -------------| ------------|
| **XhrPolling.test_transport** | does not pass due to a feature in net/http that does not send content-type header in case of StatusNoContent response code (even if explicitly set in the code), [details](https://code.google.com/p/go/source/detail?r=902dc062bff8) |
| **WebSocket.** |  Sockjs Go version supports RFC 6455, draft protocols hixie-76, hybi-10 are not supported |
| **JSONEncoding** | As menioned in [browser quirks](https://github.com/sockjs/sockjs-client#browser-quirks) section: "it's advisable to use only valid characters. Using invalid characters is a bit slower, and may not work with SockJS servers that have a proper Unicode support." Go lang has a proper Unicode support |
| **RawWebsocket.** | The sockjs protocol tests use old WebSocket client library (hybi-10) that does not support RFC 6455 properly |

WebSocket
-
As mentioned above sockjs-go library is compatible with RFC 6455. That means the browsers not supporting RFC 6455 are not supported properly. There are no plans to support draft versions of WebSocket protocol. The WebSocket support is based on [Gorilla web toolkit](http://www.gorillatoolkit.org/pkg/websocket) implementation of WebSocket.

For detailed information about browser versions supporting RFC 6455 see this [wiki page](http://en.wikipedia.org/wiki/WebSocket#Browser_support).
