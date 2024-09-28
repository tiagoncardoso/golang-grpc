# Golang - gRPC API
This is a simple gRPC API written in Golang.

## Prerequisites

### 1 . Install Protocol Buffer Compiler
> Oficial Documentation: https://grpc.io/docs/protoc-installation/

### 2. Install gRPC plugins
```bash
$ go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
$ go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

### 3. Create proto file
> It's important to create a directory to store all proto files. In this case, I created a directory called `proto` in the root of the project. Also, in this files, it wil be created the messages and service structures.

```bash
```protobuf
// ./proto/course_category.proto

syntax = "proto3";
package pb;
option go_package = "internal/pb";

//...
``` 

### 4. Update Path so that the `protoc` compiler can find the plugins
```bash
$ export PATH="$PATH:$(go env GOPATH)/bin"
```

### 5. Generate Proto Buffers
```bash
$ protoc --go_out=. --go-grpc_out=. proto/course_category.proto
```