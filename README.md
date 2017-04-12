# Introduction

When running `go run build.go build` will see

`go build -ldflags -w -X main.version=1.1.5-rc -X main.commit=v1.1.4+23-gdd35b31 -X main.buildstamp=1491902483 -o ./bin/grafana-server ./pkg/cmd/grafana-server`

Which means

* source code input path is `./pkg/cmd/grafana-server`
* binary code output path is `./bin/grafana-server`
* omit the DWARF symbol table

Notes

* both `-ldflags` and `-o` are flags of `build`
* both `-w` and `-X` are flags of `-ldflags`
* full list of `-ldflags` flags see here https://golang.org/cmd/link/