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

### 6. Implement the gRPC server entrypoint
```go
// ./cmd/server/grpc-server.go

// ...
categoryDb := database.NewCategory(db)
categoryService := service.NewCategoryService(*categoryDb)

grpcServer := grpc.NewServer()
pb.RegisterCategoryServiceServer(grpcServer, categoryService)

// Just for debugging purposes. Not recommended for production
reflection.Register(grpcServer)

lis, err := net.Listen("tcp", ":50051")
if err != nil {
panic(err)
}

if err := grpcServer.Serve(lis); err != nil {
panic(err)
}

// ...

```

### 7. Create database structure
For this example, I used a sqlite file database. The database structure is simple, with only one table called `categories`.

```bash
$ sqlite3 database.db
```

```sql
CREATE TABLE categories (id string, name string, description string);
```

### 8. Run the server
```bash
$ go run cmd/server/grpc-server.go
```

### 9. Use client

For this example, I use [Evans CLI](https://github.com/ktr0731/evans) to interact with the gRPC server.

#### Run Evans CLI
```bash
$ evans -r repl --host localhost --port 50051
```

```bash
# This command will show the following output
  ______
 |  ____|
 | |__    __   __   __ _   _ __    ___
 |  __|   \ \ / /  / _. | | '_ \  / __|
 | |____   \ V /  | (_| | | | | | \__ \
 |______|   \_/    \__,_| |_| |_| |___/

 more expressive universal gRPC client
```

#### Select package if necessary
```bash
localhost:50051> package pb
```

#### Select the service
```bash
localhost:50051> service CategoryService
```

#### Call the methods: CreateCategory

```bash
localhost:50051> call CreateCategory
```

> The input attributes will be requested. Fill them in and press `Enter`.

```bash
name (TYPE_STRING) => Tech
description (TYPE_STRING) => Test category to use my gRPC server
```

> The output will be displayed on the screen.

```bash
{
  "description": "My category test",
  "id": "ea0410a5-38b1-4f5b-86e4-e0009e8751b5",
  "name": "Tech"
}
```

#### Call the methods: ListCategories

```bash
pb.CategoryService@localhost:50051> call ListCategories
{
  "categories": [
    {
      "description": "My category test",
      "id": "ea0410a5-38b1-4f5b-86e4-e0009e8751b5",
      "name": "Tech"
    },
    {
      "description": "My second category",
      "id": "2507ad6a-ae00-4bed-8f29-387ccb419011",
      "name": "Marketing"
    }
  ]
}
```

#### Call the methods: GetCategory

```bash
pb.CategoryService@localhost:50051> call GetCategory
id (TYPE_STRING) => 2507ad6a-ae00-4bed-8f29-387ccb419011
{
  "description": "My second category",
  "id": "2507ad6a-ae00-4bed-8f29-387ccb419011",
  "name": "Marketing"
}
```