# 路由
### 路由模块
```ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

const routes: Routes = [];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### 使用路由
```html
<nav>
  <ul>
    <li><a routerLink="/first-component" routerLinkActive="active">First Component</a></li>
    <li><a routerLink="/second-component" routerLinkActive="active">Second Component</a></li>
  </ul>
</nav>
```

### 配置路由
```ts
const routes: Routes = [
  { 
    path: 'first-component', 
	  component: FirstComponent,
	  children: [
	    { path: 'child-a', component: ChildAComponent },
	    { path: 'child-b', component: ChildBComponent },
	  ],
	},
  { path: 'second-component', component: SecondComponent },
	{ path: '',   redirectTo: '/first-component', pathMatch: 'full' },
  { path: '**', component: PageNotFoundComponent },
];
```

### 路由接口
```ts
import { Router, ActivatedRoute, ParamMap } from '@angular/router';

constructor( private route: ActivatedRoute, private router: Router) {}

ngOnInit() {
	// 获取查询参数
  this.route.queryParams.subscribe(params => {
    this.name = params['name'];
  });
}

gotoItems() {
  this.router.navigate(['/heroes', { id: "" }]);
}
```

### 路由鉴权
生成鉴权代码
```sh
ng generate guard your-guard
```

鉴权类型
- CanActivate
- CanActivateChild
- CanDeactivate
- Resolve
- CanLoad

鉴权类
```ts
import { Injectable } from '@angular/core';
import {
  ActivatedRouteSnapshot,
  CanActivate,
  RouterStateSnapshot,
  UrlTree,
} from '@angular/router';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class YourGuard implements CanActivate {
  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ):
    | Observable<boolean | UrlTree>
    | Promise<boolean | UrlTree>
    | boolean
    | UrlTree {
    return true;
  }
}
```

设置路由
```ts
{
  path: '/your-path',
  component: YourComponent,
  canActivate: [YourGuard],
}
```

### 路由链接
```html
<a [routerLink]="['/heroes']">Heroes</a>
<a [routerLink]="['/hero', hero.id]">
  <span class="badge">{{ hero.id }}</span>{{ hero.name }}
</a>
<a [routerLink]="['/crisis-center', { foo: 'foo' }]">Crisis Center</a>

<nav>
  <a [routerLink]="['/crisis-center']">Crisis Center</a>
  <a [routerLink]="['/crisis-center/1', { foo: 'foo' }]">Dragon Crisis</a>
  <a [routerLink]="['/crisis-center/2']">Shark Crisis</a>
</nav>
```

### 本地路由策略
- PathLocationStrategy：HTML5支持的现代路由
- HashLocationStrategy：哈希路由

```ts
@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```
