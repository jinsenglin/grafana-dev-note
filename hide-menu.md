# Hide menu

In pkg/api/index.go

```
func setIndexViewData(c *middleware.Context) (*dtos.IndexViewData, error) {
    ...
    dashboardChildNavs := []*dtos.NavLink{
        {Text: "Home", Url: setting.AppSubUrl + "/"},
        {Text: "Playlists", Url: setting.AppSubUrl + "/playlists"},
        {Text: "Snapshots", Url: setting.AppSubUrl + "/dashboard/snapshots"},
    }  
    ...
}
```

---

In public/app/core/components/sidemenu/sidemenu.ts

```
export class SideMenuCtrl {
    ...
    openUserDropdown() {
        ...
        this.orgMenu = [
            {section: 'You', cssClass: 'dropdown-menu-title'},
            {text: 'Profile', url: this.getUrl('/profile')},
        ];
        ...
    }
    ...
}
```

---

In public/app/core/components/sidemenu/sidemenu.html

```
<li ng-repeat="item in ::ctrl.mainLinks" class="dropdown">
``` 
