# golang

## dev env

Install to ``$GOPATH/bin`
```shell=
go install github.com/rakyll/hey@latest
```

Basic linting and vetting
```shell=
go fmt ./...
go vet ./...
go build hello.go
```

## Built-in Types

**The Zero Value**
0, "", null

**Literals**

Literals are untyped; they can interact with any variable that is compatible with the literal.

- integers 1_234, 0b1111, 0o777, 0xabcd
- floats 6.03e23, 0x1234p23
- rune 'a', '\141' octal, '\x61' hex 8-bit, '\u0061' hex 16-bit, '\U00000061' hex 32-bit
- string "hello"
- raw string `new line \n is interpreted as is`

**Booleans**
```go
var flag bool // default is false
var isAwsome = true
```

**Numeric types**

Signed `int{8,16,32,64}`, and unsigned `uint{8,16,32,64}` integers.

Integer aliases: `int=int32 or int64`, `uint=uint32 or uint64`, `uint8 = byte`.

Special integer types: `rune` and `uintptr`.

Floating point types IEEE 754: `float32` and `float64` (default). Typical domains are graphics and scientific operations.

```
sign bit 0+,1- | base 2 exp E 8 or 11 | Mantissa 23 or 52
```

Floating point type zero-division: +-N./0 = +-Inf, 0/0=NaN.

Complex numbers: complex{64,128}. The real and img parts are `float{32,64}`.
- Constructor `complex(x, y)`.
- Get a real part `real(c)`.
- Get an img part `imag(c)`. Literal `3i`.

## Strings are utf-8
```
"abcde" < "cd" // true
"cde" < "cd" // false
```

A single code point is `rune = int32`.