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