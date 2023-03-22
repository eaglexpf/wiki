---
title: http服务
description: 
published: true
date: 2023-03-22T09:38:26.831Z
tags: 
editor: markdown
dateCreated: 2023-03-22T09:38:26.831Z
---

# HTTP服务

启动一个基础http服务器
```go
package main

import (
    "fmt"
    "net/http"
)

func IndexHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintln(w, "hello world")
}

func main() {
    http.HandleFunc("/", IndexHandler)
    http.ListenAndServe("127.0.0.0:8000", nil)
}
```
