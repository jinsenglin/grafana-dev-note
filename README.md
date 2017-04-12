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

---

Use `go run` to launch grafana-server

```
cd $GOPATH/src/github.com/grafana/grafana/pkg/cmd/grafana-server
go run main.go server.go web.go -homepath $GOPATH/src/github.com/grafana/grafana
```

---

Use debugger to launch grafana-server

```
cd $GOPATH/src/github.com/grafana/grafana/pkg/cmd/grafana-server
dlv debug -- -homepath $GOPATH/src/github.com/grafana/grafana

(dlv) break main.main
(dlv) break main.main:1
(dlv) break main.main:2
(dlv) break api.LoginPost:1
(dlv) continue
(dlv) continue
(dlv) continue
(dlv) continue

# Try login from http://localhost:3000/login

(dlv) continue
```

If panic

```
(dlv) restart
```

Have tried

* delve http://blog.ralch.com/tutorial/golang-debug-with-delve/
* godebug
* gdb
