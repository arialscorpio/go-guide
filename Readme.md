# Go Guide
### Best Practices
* Make dependencies explicit by passing them as parameters.

#### Type Assertion
```
x.(T)
```
The above expression states that 
1. the interface type variable `x` is not `nil`.
2. the value stored in `x` is of type `T`.

## Go Commands
### go get
Updates dependencies of current module, i.e. `go.mod`.
- Takes one or more package or module paths as arguments.
- Resolves package to a module at a specific version.
- Updates module version in `go.mod`.
- Downloads module source code into the module cache.
- Dependencies are resolved recursively.
- Modules are only downloaded, but not built.
- No error is reported if updated package can’t be built for the current platform.

#### Add or upgrade a dependency to its latest version
```
go get github.com/some/pkg
```

#### Upgrade or downgrade a dependency to a specific version
```
go get github.com/some/pkg@v1.2.3
```

#### Remove a dependency
```
go get github.com/me/mod@none
```

### go install
- Builds and installs packages.
- ALL packages must refer to the main packages.
- Executables are installed to the directory specified in `GOBIN` env. variable (by default set to `GOPATH/bin`).
- Given an explicit package version ignores the `go.mod` file in current or parent directory. No changes are made to `go.mod` file. It's useful for installing executables without affecting the dependencies of the main module.
  ```
  go install github.com/me/dummy@v1.2.0
  ```
- Without an explicit version uses the version selected by the module in the current directory.
  ```
  go install github.com/me/dummy
  ```

### RoundTripper
- Interface in `http` package to execute one single HTTP transaction, i.e. sending a request and returning corresponding response.
  ```
  type RoundTripper interface {
      RoundTrip(*Request) (*Response, error)
  }
  ```
  
**Applications**
- Set authorization headers in request
- Logging (request URL, response status, time taken etc.)

### ReadResponse
- Builds and returns an HTTP response with the body read from the given `r` parameter.
  ```
  fund ReadResponse(r *bufio.Reader, req *Request) (*Response, error)
  ```
  `req` parameter, if provided, is set as the related request in the returned response.

### Concurrency
Perform multiple independent tasks simultaneously.

### Goroutine
- Function that runs concurrently with other functions.
- Lightweight thread managed by go runtime.
- Precede function invocation by `go` prefix.
  ```
  go doSomething(arg1, arg2)
  ```
  Evaluation of `arg1` and `arg2` happens in current goroutine whereas execution of `doSomething` happens in a new goroutine.
- Non blocking. Programm control moves immediately to the next instruction.
- Share same address space; access to shared memory must be synchronized.
- `main()` function of `main` package is implicitly a goroutine.

### Channels
- Channels provide a way for two goroutines to communicate with each other.
- Type identifier `chan`.
  ```
  var c chan string = make(chan string)

  // send
  c <- "hello"

  // receive
  msg := <- c
  ```
  Data type at the end defines the type of data flows through the channel.
- Receiver is blocked until sender sends the data and vice versa, i.e. sender waits until receiver receives the data.
- Read/write/uni-directional channel
  ```
  // w is write only channel
  func Ping(w chan<- string) {
      ...
  }

  // r is read only channel
  func Ping(r <-chan string) {
      ...
  }
  ```
- A bi-directional channel can be used to both send and receive data. It can also be passed as an argument to a function accepting uni-directional channel. The opposite is not true.
- Can be closed by sender to signal the end of transmittion.
  > NOTE: Sending data on closed channel triggers panic.
- Receiver can detect a closed channel.
  ```
  d, ok := <- c
  ```
  `ok` is *false* if channel is closed.

### Buffered Channel
- Channel with associated buffer, i.e. capacity more than one.
- Sending/receiving is not blocked as long as the channel buffer is not full.
  ```
  // channel with buffer capacity 3
  var c chan string := make(chan string, 3)
  ```

### Select
Switch for channels.
```
var c1, c2 chan string

...

select {
    case d := <- c1
        ...
    case d := <- c2
        ...
    default:
        ...
}
```
- Selects the first channel ready to receive or send.
- Randomly selects one if more than one channels are ready.
- Blocks if none of the channels are ready until one is available. A default case, if defined, is executed in this case.
