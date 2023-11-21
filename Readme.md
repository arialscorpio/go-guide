## Go Guide
### Best Practices
* Make dependencies explicit by passing them as parameters.

#### Type Assertion
```
x.(T)
```
The above expression states that 
1. the interface type variable `x` is not `nil`.
2. the value stored in `x` is of type `T`.

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
