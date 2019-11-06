---
title: "Golang"
---

## Learn Golang
1. Learn Golang from the official tutorial site: [Tour of Go](https://tour.golang.org/).

## Code Style
1. Code interpretability trumps everything else.
1. Please conform to the Golang code style established in the following articles. 
    + [How To Write Go Code](https://golang.org/doc/code.html).
    + [Effective Go](https://golang.org/doc/effective_go.html).
    + [Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments).

## Naming    
+ The convention in Go is to use mixedCase rather than underscores to write multiword names.
+ Packages are given lower case, single-word names; there should be no need for underscores or mixedCase. 
+ Naming guide:
    | Type                       | Internal           | Exported                       |
    | ---------------------------|:-------------------| :------------------------------|
    | Packages	                 |                    | lowercase                      |
    | Variables 	             | mixedCase          |	MixedCase                      |
    | Function/Method Names	     | mixedCase          |	MixedCase                      |
    | Function/Method Parameters | mixedCase          |	                               |
    | Structs                    | mixedCase          |	MixedCase                      |
    | Exceptions	             |                    |	MixedCase                      |
    | Constants	                 | mixedCase          | MixedCase                      |

## Code Documentation - GoDoc
1. GoDoc extracts and generates documentation for Go programs.
1. It runs as a web server and presents the documentation as a web page.
1. Usage:
    ```bash
    $ godoc [flag]

    [flag]
    -goroot=$GOROOT  : Go root directory
    -http=addr       : HTTP service address (e.g., '127.0.0.1:6060' or just ':6060')
    ```
1. Example:    
    ```bash
    $ godoc -http=:6060
    ```
1. By default, godoc looks at the packages it finds via `$GOROOT` and `$GOPATH` (if set). This behavior can be altered by providing an alternative `$GOROOT` with the `-goroot` flag. 