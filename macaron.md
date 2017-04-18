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

In pkg/cmd/grafana-server/web.go

```
func newMacaron() *macaron.Macaron {
    ...
    m := macaron.New()
    m.Use(middleware.Logger())
    ...
}
```

In pkg/middleware/logger.go

```
func Logger() macaron.Handler {
    return func(res http.ResponseWriter, req *http.Request, c *macaron.Context) {
        ...
    }
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

Middlewares allow you easily plugin/unplugin features for your Macaron applications.

e.g., Middleware oauth2 provides support of user login via an OAuth 2.0 backend for Macaron.

```
m.Use(oauth2.Google(
	&goauth2.Config{
		ClientID:     "client_id",
		ClientSecret: "client_secret",
		Scopes:       []string{"https://www.googleapis.com/auth/drive"},
		RedirectURL:  "redirect_url",
	},
))
```