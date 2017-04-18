# Goals

* add new configuration section and key in conf/custom.ini
* use the new key in public/views/index.html
* use the new key in public/app/features/dashboard/dashnav/dashnav.html

---

# Goal: add new configuration section and key in custom.ini

Result:

```
# in conf/custom.ini
[ui.manage_dashboard]
enabled = false

# in pkg/setting/setting.go
var (
    ...
    UIManageDashboard bool = true
    ...
)

uiManageDashboard := Cfg.Section("ui.manage_dashboard")
UIManageDashboard = uiManageDashboard.Key("enabled").MustBool(true)
```

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

# Goal: use the new key in index.html

Result: 

```
# in public/views/index.html
[[.UIManageDashboard]]

# in pkg/api/index.go
var data = dtos.IndexViewData{
    ...
    UIManageDashboard:   setting.UIManageDashboard,
}

# in pkg/api/dtos/index.go
type IndexViewData struct {
    ...
    UIManageDashboard   bool
}
```

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
        Settings:                settings,
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

---

# Goal: use the new key in dashnav.html

Result:

```
# in public/views/index.html
window.grafanaBootData = {
    ...
    uiManageDashboard: [[.UIManageDashboard]]
};

# in public/app/core/services/context_srv.ts
export class ContextSrv {
    ...
    uiManageDashboard: any;
    
    constructor() {
        ...
        this.uiManageDashboard = config.bootData.uiManageDashboard;
    }

# in public/app/features/dashboard/dashnav/dashnav.ts
$scope.contextSrv.uiManageDashboard
```

In public/app/features/dashboard/dashnav/dashnav.html

```
<li ng-if="::showSettingsMenu" class="dropdown">
```

In public/app/features/dashboard/dashnav/dashnav.ts

```
export class DashNavCtrl {

    /** @ngInject */
    constructor($scope, $rootScope, dashboardSrv, $location, playlistSrv, backendSrv, $timeout, datasourceSrv) {
        
        $scope.init = function() {
            $scope.showSettingsMenu = $scope.dashboardMeta.canEdit || $scope.contextSrv.isEditor;
            ...
    
}
```

In public/app/features/dashboard/dashboard_ctrl.ts

```
$scope.dashboardMeta = dashboard.meta;
```

In public/app/core/components/grafana_app.ts

```
$scope.contextSrv = contextSrv;
```

In public/app/core/services/context_srv.ts

```
import config from 'app/core/config';
...
export class User {
    ...
    orgRole: any;
    ...
    
    constructor() {
        if (config.bootData.user) {
            _.extend(this, config.bootData.user);
        }
    }
}

export class ContextSrv {
    ...
    user: User;
    ...
    isEditor: any;
    ...
    
    constructor() {
        ...
        this.user = new User();
        ...
        this.isEditor = this.hasRole('Editor') || this.hasRole('Admin');
    }
    
    hasRole(role) {
        return this.user.orgRole === role;
    }
}

...

var contextSrv = new ContextSrv();
```

In public/app/core/config.js

```
define([
  'app/core/settings',
],
function (Settings) {
  "use strict";

  var bootData = window.grafanaBootData || { settings: {} };
  var options = bootData.settings;
  options.bootData = bootData;

  return new Settings(options);

});
```

In public/views/index.html

```
<script>
    window.grafanaBootData = {
        user:[[.User]],
        settings: [[.Settings]],
        mainNavLinks: [[.MainNavLinks]]
    };
</script>
```