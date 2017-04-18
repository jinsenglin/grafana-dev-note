# Grafana uses macaron web framework

In pkg/cmd/grafana-server/server.go

```
func (g *GrafanaServerImpl) startHttpServer() {
    ...
    m := newMacaron()
    api.Register(m)
    ...
}
```

In pkg/cmd/grafana-server/web.go

```
func newMacaron() *macaron.Macaron {
    ...
}
```

In pkg/api/api.go

```
func Register(r *macaron.Macaron) {
    ...
    r.Get("/", reqSignedIn, Index)
    ...
}
```

---

Package [macaron](https://github.com/go-macaron/macaron) is a high productive and modular web framework in Go.

The very basic usage of Macaron:

```
package main

import "gopkg.in/macaron.v1"

func main() {
	m := macaron.Classic()
	m.Get("/", func() string {
		return "Hello world!"
	})
	m.Run()
}
```