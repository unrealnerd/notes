## go Notes

- go Commands
    - running a go `go run main.go`
    - adding package `go get -u -v github.com/packagename`
- go doesn take responsibility of implicitly converting types and get blamed on loss of data :)
- multiple args `func sum(nums ...int) {` use `range` to retreive the args inside the func 
- go needs no vm like java and .net and is procedural and statically typed unlike oop
- wire needs this line `//+build wireinject` with *new line* post this other wise the file gets inlcuded and complains **redeclared block**
- wire.exe path should be included in the env path variable 
- use `go generate` to generate DI container using wire OR Use `wire` command to generte wire_gen files
- to generate recursively use `go generate ./...`
- when building the app should use `go build` so that wire_gen and main files are also part of the build or `go build main.go wire_gen.go`
- #grpc to generate service pb files `protoc --go_out=plugins=grpc,paths=source_relative:./pkg/ ./proto/v1/qh.proto` protoc.exe must be added to the env variable
- slice is formed by slicing an array
  - array is a value type
  - slice is a pointer to an array(careful when appending)
  - append will re allocate and increase the capacity 2 times along with the length
- reading config type safe
  - get viper `go get -u github.com/spf13/viper`
  - create new config.go and config.yaml in config folder
  ```go
  \\config.go
  package config

  type Config struct {
    RabbitMQ struct {
      Host string `yaml:"hosts"`
      Port int    `yaml:"port`
    } `yaml:rabbitmq`
  }
  ```

  ```yaml
  rabbitmq:
    host: "localhost"
    port: 5671
    username: "guest"
    password: "guest"
  ```

  ```go
  \\main.go
  viper.SetConfigName("config")
	viper.AddConfigPath("./config")
	viper.AutomaticEnv()
	viper.SetConfigType("yml")
	var config c.Config

	if err := viper.ReadInConfig(); err != nil {
		fmt.Printf("Error reading config file, %s", err)
	}

	err := viper.Unmarshal(&config)
	if err != nil {
		fmt.Printf("Unable to decode into struct, %v", err)
	}

	fmt.Println("Host is\t", config.RabbitMQ.Host)
  ```
- go test
  - to test a specific test file `go test ./folder1/file1.go ./folder1/file1_test.go`
  - to test everything including subfolders `go test ./...`
  - make sure `func TestXxx(*testing.T)` note the caps X
  
- go middleware and handlers
  - handle using mux --> middleware handler --> app handler
  ```go
  loggingHandler := newLoggingHandler(logFile)
  func newLoggingHandler(dst io.Writer) func(http.Handler) http.Handler {
    return func(h http.Handler) http.Handler {
      return handlers.LoggingHandler(dst, h)
    }
  }
  mux := http.NewServeMux()
  mux.Handle("/", playground.Handler("GraphQL playground", "/query"))
  mux.Handle("/query", loggingHandler(srv))
  http.ListenAndServe(":"+port, mux)

  ```
  - [reference](https://www.alexedwards.net/blog/making-and-using-middleware)
  - [explains decorator pattern](https://medium.com/@matryer/writing-middleware-in-golang-and-how-go-makes-it-so-much-fun-4375c1246e81)
- graphql
  - when using gqlgen had issue complaing method already defined in resolver.go and resolver.schemas.go. implement the method by calling the method in reselover.go from resolver.schemas.go using a different method name.
  - to generate resolvers for fields within a model we have to update the gqlgen yml file
  ```
  Product:
    fields:
      categories:
        resolver: true # force a resolver to be generated
  ``` 
  - simple web server with native go
  ```go
      func main() {
        fmt.Println("Running web server on port 8080!")

        
        fs := http.FileServer(http.Dir("./static"))
        http.Handle("/static/", http.StripPrefix("/static/", fs))

        http.HandleFunc("/", handlers.RootHandler)

        err := http.ListenAndServe(":8080", nil)
        if err != nil {
          log.Fatal(err)
        }
      }
  ```

- Reference
  - [learn go with example](http://gobyexample.com/)
  - [slice and array internals](https://blog.golang.org/go-slices-usage-and-internals) 
  - [how append works](https://dev.to/andyhaskell/a-closer-look-at-go-s-slice-append-function-3bhb)
  - [wire dependency injection](https://blog.golang.org/wire)