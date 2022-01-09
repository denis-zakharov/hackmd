# Go Tools

## Tests

### Commands
```
go test -run=Example
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

### Test object `testing.B`
A basic benchmark.
```go=
var blackhole int

func BenchmarkFileLen(b *testing.B) {
    for i:=0; i<b.N; i++ {
        result, err := FileLen("testdata.txt", 1)
        if err != nil {
            b.Fatal(err)
        }
        blackhole = result
    }
}
```

Run benchmark with different arguments.
```go=
func BenchmarkFileLen(b *testing.B) {
    for _, v := range []int{1, 10, 100, 1000, 10000, 100000} {
        b.Run(fmt.Sprintf("FileLen-%d", v), func(b *testing.B) {
            for i := 0; i < b.N; i++ {
                result, err := FileLen("testdata.txt", v)
                if err != nil {
                    b.Fatal(err)
                }
                blackhole = result
            }
        })
    }
}
```

### Commands
```
go test -bench=. -benchmem
go test -bench=<bench func regex>
```

Output examle:
```
BenchmarkFileLen-12  25  47201025 ns/op  65342 B/op  65208 allocs/op

BenchmarkFileLen-12 == <name>-<GOMAXPROCS>
25 == no. of time the test ran.

// b.Run
BenchmarkFileLen/FileLen-10-12
BenchmarkFileLen/<b.Run prefix>-<GOMAXPROCS>
```

## Profiling
[Profiling a Go Program](https://pkg.go.dev/runtime/pprof)

Adhoc profiling using a benchmark.
```
go test -cpuprofile cpu.prof -memprofile mem.prof -bench .
go tool pprof cpu.prof
```

Must-know commands:
- top
- web
- help

Runtime profiling usually performed by instrumenting the code
and calling an http endpoint to trigger the pprof.