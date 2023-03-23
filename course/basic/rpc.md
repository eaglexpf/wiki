---
title: net/rpc基础学习
description: 
published: true
date: 2023-03-23T08:04:58.993Z
tags: 
editor: markdown
dateCreated: 2023-03-23T08:04:58.993Z
---

# 目录结构
```
├── cmd
│   ├── client.go
│   └── server.go
├── pkg
│   ├── DemoService
│   │   ├── DemoClient.go
│   │   └── DemoService.go
│   └── util
│       └── server.go
```
# 服务端示例

### 新建pkg/util/server.go文件
```go
package util

import (
	"net"
	"net/rpc"
)

func RegisterServer() *Server {
	return &Server{}
}

type Server struct {
}

func (s Server) Register(serviceName string, svc interface{}) error {
	return rpc.RegisterName(serviceName, svc)
}

func (s Server) Run(network string, address string) {
	listen, err := net.Listen(network, address)
	if err != nil {
		panic(err)
	}
	for {
		conn, err := listen.Accept()
		if err != nil {
			panic(err)
		}
		go rpc.ServeConn(conn)
	}
}
```
### 新建pkg/DemoService/DemoService.go文件
```go
package DemoService

import (
	"hapi-sidecar/pkg/util"
)

func RegisterServer(s *util.Server) error {
	service := new(DemoService)
	return s.Register(ServiceName, service)
}

const ServiceName = "DemoService"

type DemoService struct {
}

func (s DemoService) Hello(request string, reply *string) error {
	*reply = "Hello " + request
	return nil
}

func (s DemoService) Test(request string, reply *string) error {
	*reply = "Test " + request
	return nil
}
```
### 新建cmd/server文件
```go
package main

import (
	"hapi-sidecar/pkg/DemoService"
	"hapi-sidecar/pkg/util"
)

func main() {
	server := util.RegisterServer()
	err := DemoService.RegisterServer(server)
	if err != nil {
		panic(err)
	}
	server.Run("tcp", ":1234")
}
```

# 客户端示例
### 新建pkg/DemoService/DemoClient文件
```go
package DemoService

import "net/rpc"

func DialClient(network string, address string) (*Client, error) {
	c, err := rpc.Dial(network, address)
	if err != nil {
		return nil, err
	}
	return &Client{
		Client: c,
	}, nil
}

type Client struct {
	*rpc.Client
}

func (c *Client) Hello(request string, reply *string) error {
	return c.Client.Call(ServiceName+".Hello", request, reply)
}

func (c *Client) Test(request string, reply *string) error {
	return c.Client.Call(ServiceName+".Test", request, reply)
}

```
### 新建cmd/client文件
```go
package main

import (
	"fmt"
	"hapi-sidecar/pkg/DemoService"
)

func main() {
	client, err := DemoService.DialClient("tcp", ":1234")
	if err != nil {
		panic(err)
	}
	var reply string
	err = client.Hello("Roc.xu", &reply)
	if err != nil {
		panic(err)
	}
	fmt.Println(reply)
	err = client.Test("Roc.xu", &reply)
	if err != nil {
		panic(err)
	}
	fmt.Println(reply)
}

```