# Go Tools

## Tests

### Commands
```
go test -timeout 30s -race
go test -count=1 // no caching
```

### Test function
```go=
func TestAddNumbers(t *testing.T) {
    res := addNumbers(2, 3)
    if res != 5 {
        t.Error("incorrect")
    }
}
```

### Test object `testing.T`
- Error() report and continue
- Fatal() report and stop

### Setting Up and Tearing Down
Use a `TestMain(m *testing.M)` function for a global setup and tear down procedure (the TestMain is **invoked once**).

Usage examples:

- When you need to set up data in an external repository, such as a database
- When the code being tested depends on package-level variables that need to be initialized (though is better to avoid this)

```go=
var now time.Time // used in test functions

func TestMain(m *testing.M) {
    fmt.Println("Set up")
    now = time.Now() // configure state
    exitVal := m.Run() // run test functions
    fmt.Println("Clean up")
    os.Exit(exitVal)
}
```

`t.Cleanup(func(){})` defines test cleanup to run after.

### Testing Public API
By convention, define a package `packagename_test`. You can have test source files with both package names intermixed in the same source directory.

### Compare Results

An alternative to `reflect.DeepEqual` is an equality package [go-com](https://github.com/google/go-cmp). For example,
`cmd.Diff(expected, result)`.

It is possible to define own comparision function:
```go=
comparer := cmp.Comparer(func(x, y Persion) bool {
    return x.Name == y.Name && x.Age == y.Age
})
cmd.Diff(expected, result, comparer)
```

## Code Coverage
You can check what parts of the code (even parts of functions) are covered by test execution.

```
go test -v -cover -coverprofile=c.out
go tool cover -html=c.out // open report in browser
```

## Benchmarks