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

Install delve

```
brew install go-delve/delve/delve
```

Use delve

```
cd $GOPATH/src/github.com/grafana/grafana/pkg/cmd/grafana-server
dlv debug -- -homepath $GOPATH/src/github.com/grafana/grafana

# grafana version 4.1.2

(dlv) break main.main
(dlv) break main.main:1
(dlv) break main.go:46
(dlv) break api.LoginPost:1
(dlv) continue
(dlv) continue
(dlv) continue
(dlv) continue

# Try login from http://localhost:3000/login

(dlv) args

# Can see the input username and password

(dlv) continue
```

If panic

```
(dlv) restart
```

(dlv) step-related commands

* next : step over to next source line.
* step : single step through program.
* stepout : step out of the current function.

(dlv) print-related commands

* args : print function arguments.
* breakpoints: print out info for active breakpoints.
* funcs : print list of functions.
* goroutines : print program goroutines.
* help : prints the help message.
* locals : print local variables.
* regs : print contents of CPU registers.
* sources : print list of source files.
* stack : print stack trace.
* threads : print out info for every traced thread.
* types : print list of types
* vars : print package variables.

Have tried

* delve
* godebug
* gdb

References

* https://github.com/derekparker/delve/tree/master/Documentation

---

In pkg/middleware/middleware.go

```
func initContextWithBasicAuth(ctx *Context, orgId int64) bool {

    if !setting.BasicAuthEnabled {
        return false
    }
    ....
}
```

In pkg/setting/setting.go

```
var (
    ...
    BasicAuthEnabled bool
    ...
)

...

func NewConfigContext(args *CommandLineArgs) error {

    ...
    authBasic := Cfg.Section("auth.basic")
    BasicAuthEnabled = authBasic.Key("enabled").MustBool(true)
    ...
    
}
```

In conf/defaults.ini

```
[auth.basic]
enabled = true
```

Initialization

```
main.main in pkg/cmd/grafana-server/main.go 
-> setting.NewConfigContext in pkg/setting/setting.go
  -> main.Start in pkg/cmd/grafana-server/server.go
    -> main.initRuntime in pkg/cmd/grafana-server/main.go

```

---

In public/views/index.html

```
[[if .User.LightTheme]]
...
[[end]] 

...

<a href="http://grafana.org/download" target="_blank" bs-tooltip="'[[.NewGrafanaVersion]]'">
```

In pkg/api/index.go

```
func setIndexViewData(c *middleware.Context) (*dtos.IndexViewData, error) {
    settings, err := getFrontendSettingsMap(c)
    ...
    appUrl := setting.AppUrl
    ...
    var data = dtos.IndexViewData{
        User: &dtos.CurrentUser{
            ...
            LightTheme:     prefs.Theme == "light",
            ...
        },
        ...
        NewGrafanaVersion:       plugins.GrafanaLatestVersion,
        ...
    }
    ...
    return &data, nil
}
```

In pkg/api/frontendsettings.go

```
func getFrontendSettingsMap(c *middleware.Context) (map[string]interface{}, error) {
    ...
    jsonObj := map[string]interface{}{
        ...
    }

    return jsonObj, nil
}
```

In pkg/setting/setting.go

```
var (
    ...
    AppUrl       string
    ...
)
```

In pkg/api/dtos/index.go

```
package dtos                                                                                                           
                                                                                                                       
type IndexViewData struct {
        User                    *CurrentUser
        ...
        NewGrafanaVersion       string                                                                                
}
```