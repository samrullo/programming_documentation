# Angular

## Installation
```javascript
npm install -g @angular/cli
```

To create a new angular project

```javascript
ng new
```

To serve angular project

```javascr
ng serve
```

## Adding bootstrap to dependencies

To install bootstrap

```javas
npm install --save bootstrap@4
```

Then add it to dependencies in ```angular.json```

```javasc
"styles":["styles/custom.css","node_modules/bootstrap/dist/css/bootstrap.min.css"]
```

# Angular component

Angular component is a component that can be used in ```app-root``` 

Each component has 3 elements to it

1. The typescript, where the component is defined as a class

   ```javascrip
   import {Component} from "@angular/core";
   
   @Component(
   {selector:"app-new-component",
   templateUrl:"./app-new-component.html"}
   )
   export class NewComponent{
   
   }
   ```

2. HTML element
3. CSS element

We can create angular component from command line running below command

```lin
ng generate component <new component name>
```

or

```javascri
ng g c <new component name>
```

## Models

We will want to define data models as a seperate class. For instance let's say we want to create a model for our ingredients in Recipe Book web application. We can just create a file called ```ingredient.model.ts```

```typescript
export class Ingredient{
  public name:string;
	public amount:number;
	
	constructor(name:string, amount:number){
    this.name=name;
    this.amount=amount
  }
}
```

Typescript offers a shortcut to do the same as below

```type
export class Ingredient{
	constructor(public name:string, public amount:number){
	
	}
}
```



# Databinding

Angular offers double binding using ```ngModel``` directive. In below code ```name``` is a member variable of an angular component, defined in its own class.

```html
<input type="text" name="name" [(ngModel)]="name">
```

In the above html element, the value of ```input``` is bound to a variable ```name``` defined in the typescript of the component.

We can also bind a property of an html element to a variable defined in the component typescript

Let's say we want to bind a button ```disabled``` property to a variable ```isDisabled``` 

```html
<button [disabled]="isDisabled">Press</button>
```

## Event binding

You define your event handler in the component typescript. You can set the event handler to the event as in below example. Different from javascript events you omit ```on``` in the beginning.

```html
<button (click)="handleClick">Press</button>
```

## Exposing component variables to parent components

By default all member variables of a component are accessible inside the component. You have to be explicit about what component properties are accessible from outside.

Let's say we have ```product``` component, and it has ```product_name``` and ```product_price``` member variables.

And let's say we have another component called ```product-controller``` which holds list of products with their names and prices. ```product-controller``` has a form to input a product name and price and a submit button. When submit button is clicked a new product is added to the list. The ```product-controller``` also lists the products on the same page, each product is rendered as a ```product``` component. When we render each product component we have to pass product name and price to it. Since the values are passed from parent component, these variables should be exposed to the outside

```typescript
  @Input() product:{name:string,price:number}
```

Then in product-controller html page

```html
<app-product *ngFor="let product of products" [product]="product"></app-product>
```



## Broadcasting custom events from the component

When we want to broadcast, pass component properties to its parent. In other words, notify the parent of a custom event. For instance, in the product component we can define a custom event called removeProduct. When a remove button on product component's template is pressed, this event is broadcasted. Then product-controller component can listen to that custom event and remove the corresponding product from the list.

```typescript
@Output() productRemoved = new EventEmitter<{name:string,price:number}>();

onProductRemoved(){
    this.productRemoved.emit({name:this.product.name, price:this.product.price})
}
```

Then in product-controller we can listen to that event and handle it with a function.

```html
<app-product [product]="product" (productRemoved)="onProductRemoved"></app-product>
```

# References to HTML elements
## Reference local HTML element

To reference a local HTML element

```html
<input type="text" #productName>
<button (click)="recordName(productName)">Submit</button>
```

```typescript
recordName(productName:HTMLInputElement){
    this.product.name=productName.value;
}
```

Remember we can use local references inside the template only. We can pass them as arguments to click methods though.

We can also get reference to a local HTML element using ```ViewChild``` decorator as well

```type
@ViewChild('productName',{static:true}) productNameInput;
```



# Angular Directives

Some well known directives are ```ngFor``` and ```ngIf```. 

```ngFor``` allows you to generate a loop over the elements of a list and render some component, usually a list item

in the loop.

```javascr
<app-product *ngFor="let product of products"/>
```

```ngIf``` allows to control whether to render a component or not based on some condition. In below example ```app-product``` component is rendered only if ```isValid``` variable is ```True```

```javascri
<app-product *ngIf="isValid"/>
```

```ngClass``` applies a class based on some condition. Likewise, ```ngStyle``` applies some style based on some condition

```javasc
<app-component *ngClass="{<class name> : <condition>}"></app-component>
```

```javasc
<app-component *ngStyle="{backgroundColor:blue?<condition>}"
```

## Basic highlighter directive

Let's create our own directive called basic highlighter. This directive simply highlights some DOM component with some color such as blue or green. We can organize our directives under a folder called directives.

```typescript
import { Directive, ElementRef } from '@angular/core';
@Directive({
  selector: '[appBasicHighlight]'
})
export class BasicHighlightDirective {

  constructor(private elemRef:ElementRef) { 
    elemRef.nativeElement.style="background-color:green";
  }
}
```

And we will have to include ```BasicHighlightDirective``` in ```app.module.ts``` in ```declarations```

```typescript
...
@NgModule({
  declarations: [
    AppComponent,
    ProductComponent,
    ProductControllerComponent,
    BasicHighlightDirective
  ],
  ...
```

## Better highlighter directive that uses Renderer

Setting DOM element's style directly inside the typescript is not a good practice. Because there is no guarantee that DOM element actually exists. Instead we can use ```Renderer```

Let's create a ```better-highlighter``` directive that uses ```Renderer``` within directives folder

```
ng generate directive directives/better-highlighter
```

```typescript
import { Directive, ElementRef, OnInit, Renderer2 } from '@angular/core';

@Directive({
  selector: '[appBetterHighlighter]'
})
export class BetterHighlighterDirective implements OnInit{

  constructor(private elRef:ElementRef,private renderer:Renderer2) { }

  ngOnInit(){
    this.renderer.setStyle(this.elRef.nativeElement,'background-color','lightblue')
  }
}
```

## Highlighter directive that uses HostListener

Above all is good, but I want a dynamic directive that changes the element's background color when I hover over it and changes its background color back to original when I leave the element. For this we can use ```HostListener``` to listen to events that occur on the host element and perform some action such as changing background color.

```typescript
import { Directive, ElementRef, HostListener, OnInit, Renderer2 } from '@angular/core';

@Directive({
  selector: '[appBetterHighlighter]'
})
export class BetterHighlighterDirective implements OnInit {
  constructor(private elRef: ElementRef, private renderer: Renderer2) { }

  ngOnInit() {
  }

  @HostListener('mouseenter') mouseOver(event: MouseEvent) {
    this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'lightblue');
  }

  @HostListener('mouseleave') mouseLeave(event: MouseEvent) {
    this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'transparent');
  }
}

```

```html
<div appBetterHighlighter> I am better highlighted </div>
```

## Highlighter with HostListener and HostBinding

We can bind to the host element's property such as ```style.backgroundColor``` and then change it dynamically.

```typescript
import { Directive, ElementRef, HostBinding, HostListener, OnInit, Renderer2 } from '@angular/core';

@Directive({
  selector: '[appBetterHighlighter]'
})
export class BetterHighlighterDirective implements OnInit {

  constructor(private elRef: ElementRef, private renderer: Renderer2) { }

  ngOnInit() {
  }

  @HostBinding('style.backgroundColor') backgroundColor: string;
  @HostBinding('style.color') color: string;

  @HostListener('mouseenter') mouseOver(event: MouseEvent) {
    this.backgroundColor = "lightblue";
    this.color = "red";
  }

  @HostListener('mouseleave') mouseLeave(event: MouseEvent) {
    this.backgroundColor = "transparent";
    this.color = "black";
  }
}
```

## Structural directives

Structural directives are directives that are converted to something more by angular. Angular detects structural directives by a preceding star in front of it. For instance to take ```*ngIf``` as an example following code is equivalent to the one below it

```html
<div *ngIf="some condition">I am rendered if the condition is true</div>
```

```html
<ng-template [ngIf]="some condition">
  <div> I am rendered if condition is true</div>
</ng-template>
```

### Our first structural directive

Let's create our first structural directive called ```unless``` which is the opposite of ```ngIf``` 

```typescript
import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

@Directive({
  selector: '[appUnless]'
})
export class UnlessDirective {

  @Input() set appUnless(condition: boolean) {
    if (!condition) {

      // if condition is false embed template into view container
      this.vcRef.createEmbeddedView(this.templateRef);
    } else {
      // if condition is true, clear view container
      this.vcRef.clear();
    }
  }
  constructor(private templateRef: TemplateRef<any>, private vcRef: ViewContainerRef) { }
}
```

### ngSwitch directive

Sometimes ngSwitch directive may come in handy. Here is how you can use it

```html
 <div [ngSwitch]="product.version">
   <div *ngSwitchCase="1" appBetterHighlighter class="col-4">{{product.version}}</div>
   <div *ngSwitchCase="2" appBasicHighlight class="col-4">{{product.version}}</div>
   <div *ngSwitchDefault class="col-4">{{product.version}}</div>
</div>
```

# Services

## How to create a service and make it available for usage

Let's think of a simple LoggingService that simply logs status change. Creating a service is straightforward. You just create typescript file either in some shared folder or directly under app folder. Service is just a TypeScript class

```typescript
export class LoggingService{
  logStatusChange(status:string){
    console.log("status changed to : "+status)
  }
}
```

To use a service in a component

1. We have to inject service into dependencies in the constructor
2. We have to specify the service in providers list

```typescript
import {Component} from "@angular/core"
import {LoggingService} from "../logging.service.ts"

@Component({
  selector:app-some-component,
  templateUrl:"some-component.html",
  styleUrls:[],
  providers:[LoggingService]
})
export class SomeComponent {
  myStatus:string;
  constructor(private loggingService:LoggingService){}
  
  someMethod(){
    loggingService.logStatusChange(this.myStatus)
  }
}
```

If you want to make a service available across your whole application you can achieve it in one of two ways

1. Provide it in the providers of AppModule

2. Add Injectable decorator in the following manner

   ```typescript
   @Injectable({providedIn:'root'})
   export class SomeService {
     ...
   }
   ```

   

## Services as Data providers

We can use services to store and manage data. Suppose a simple application where we have a list of accounts and we need to render accounts and have ability to add an account and to modify an account. Our application could have 

1. AppComponent : that renders NewAccountComponent and list of AccountComponents
2. NewAccountComponent : has form to input account name and status and submit
3. AccountComponent : component that renders the account

```typescript
export class AccountsService{
  accounts=[{name:'accOne',status:'active'},{name:'accTwo',status:'blocked'}];
  
  addAccount(new_account:{name:string,status:string}){
    this.accounts.push(new_account);
  }
  
  updateAccount(id:number,new_status:string){
    this.accounts[id].status=new_status;
  }
}
```

As before we could use this service in AppComponent by injecting it in constructor and specifying it in providers. And similarly we can provide it to NewAccountComponent. But this approach has a problem. Since we provide the service to each component separately angular creates seperate instances of AccountService for each and its accounts list is not shared between AppComponent and NewAccountComponent as a result.

To solve this problem we can take advantage of hierarchical injection, which means when we provide the service to a component, the same instance of the service is made available to its children.

The topmost component in angular is AppModule. So by specifying AccountsService in AppModule's providers we make the same instance of AccountsService available to the whole angular application.

## Listen to service data changes

We can have situations where our Data Management Service holds an array of data points and we make them accessible only with a getter method and we pass a copy of it to prevent undesired modifications to it from outside. But then every time our array changes we have to notify all the components that use this service about the change. We can use EventEmitters for that purpose. And within the component we can subscribe to those EventEmitters and capture the emitted data

```typescript
ngOnInit(){
  this.items=this.someService.getItems();
  this.someService.someEventEmitter.subscribe(
  (passedData:DataType) => {
    //store the passed data in the local variable
    this.items=passedData;
  });
}
```

### How to spread out array elements in javascript

Sometimes we want to add individual elements of an array to another array at one go. For such cases we can use spread out feature as below. In below example three dots in front the array will spread out the array and add each of its elements to someArray

```javas
someArray.push(...someOtherArray)
```

# Routing

## Routing first steps

Let's consider an application that has HomeComponent, UsersComponent and ServersComponent.

And we want our application to render these components based on url routes. For instance "localhost:4200/users" shoudl display UsersComponent.

How can we achieve it?

Well first we have to define routes used by our application. And the right place to define it is in AppModule, as this is where we define things that should be accessible by the whole application. 

We define appRoutes that should have a type Routes, which is an array of routes. Each route is a javascript object that has ```path``` and ```component``` keys.

```typescript
const appRoutes:Routes = [
  {path:'',component:HomeComponent},
  {path:'users',component:UsersComponent},
  {path:'servers',component:Servers}
]
```

Next we have to import RouterModule and use its ```forRoot``` method to register these routes

```typescript
@NgModule({
  //....
  imports:[
    ...,
    RouterModule.forRoot(appRoutes)
  ],
})
```

Finally we have to specify where to render the components when each route is visited. For this we use a special directive ```router-outlet```

```typescript
<router-outlet></router-outlet>
```

## Route navigation

Using href is not the right way of navigation because it will cause our app to reload and lose its state. Instead we should use ```routerLink``` directive. Pay attention how we wrapped ```routerLink``` in square brackets in the last link. This allows to specify route as an array of route parts.

```html
<div class="container">
  <div class="row">
    <div class="col-xs-12 col-sm-10 col-md-8 col-sm-offset-1 col-md-offset-2">
      <ul class="nav nav-tabs">
        <li role="presentation" class="active"><a routerLink="/">Home</a></li>
        <li role="presentation"><a routerLink="/servers">Servers</a></li>
        <li role="presentation"><a [routerLink]="['/users']">Users</a></li>
      </ul>
    </div>
  </div>
  <div class="row">
    <div class="col-xs-12 col-sm-10 col-md-8 col-sm-offset-1 col-md-offset-2">
      <router-outlet></router-outlet>
    </div>
  </div>
</div>
```

## Understand navigation path

Route pathes can be absolute or relative. When we use slash in front of the route it tells angular this is absolute path, which means it will be added to the domain directly. If we don't use slash in front then it gets appended to the current path. This behavior comes handy when we want to construct complex pathes.

```html
<a [routerLink]="['/firstPart','secondPart','thirdPart']">someLink</a>
```

## Navigating routes programmatically

Sometimes we might want to navigate to a route path programmatically from within TypeScript after completing some complex calculation. We can do this leveraging ```Router``` module's ```navigate``` method

```typescript
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {

  constructor(private router: Router) { }

  ngOnInit() {
  }

  onLoadServer() {
    // do some complex calculation
    this.router.navigate(["/servers"])
  }
}
```

### Capturing active route

By default relative pathes are directly added to the domain when we navigate from typescript because typescript doesn't know what is the current active route. We can get hold of it though and then pass it to relativeTo to explicitly instruct to what route the relative path should be appended to.

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { ServersService } from './servers.service';

@Component({
  selector: 'app-servers',
  templateUrl: './servers.component.html',
  styleUrls: ['./servers.component.css']
})
export class ServersComponent implements OnInit {
  public servers: { id: number, name: string, status: string }[] = [];

  constructor(private serversService: ServersService, private router: Router, private route: ActivatedRoute) { }

  ngOnInit() {
    this.servers = this.serversService.getServers();
  }

  reload() {
    this.router.navigate(["servers"], { relativeTo: this.route })
  }
}

```

## Parameters in the route

Just by putting colon in front of the route we can convert it to a parameter that can be any value

```typescript
const appRoutes:Routes=[
  {path:'',component:HomeComponent},
  {path:'users',component:UsersComponent},
  {path:'users/:id/:name',component:UserComponent},
  {path:'servers',component:ServersComponent}
];
```

Then we can capture these parameters in the component using ActivatedRoute ```snapshot.params``` 

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  styleUrls: ['./user.component.css']
})
export class UserComponent implements OnInit {
  user: {id: number, name: string};

  constructor(private route:ActivatedRoute) { }

  ngOnInit() {
    this.user={
      id:this.route.snapshot.params['id'],
      name:this.route.snapshot.params['name']
    }
  }

}

```

## Unsubscribing from params observable explicitly

Angular unsubscribes from observable when our component is destroyed. It is important to know that

```typescript
  paramSubscription: Subscription;
...
ngOnInit(){
  ...
  this.paramSubscription = ...
}
...
ngOnDestroy(){
    this.paramSubscription.unsubscribe();
  }

```

## Defining query parameters and fragments

```html
<a
        [routerLink]="['/servers',5,'edit']"
        [queryParams]="{allowEdit:'1'}"
        [fragment]="'loading'"
        class="list-group-item"
        *ngFor="let server of servers">
        {{ server.name }}
      </a>
```

```typescript
reload(id: number) {
    this.router.navigate(["/servers", id, 'edit'], { queryParams: { allowEdit: "1" }, fragment: "loading" })
  }
```

## Capturing query parameters

just like in the case of params we can capture them using ```snapshot.queryParams``` of ```ActivatedRoute```

or subscribe to ```queryParams``` observable

## Nested routes

We can nest routes within a route as below

```typescript
const appRoutes: Routes = [
  { path: '', component: HomeComponent },
  {
    path: 'users', component: UsersComponent,
    children: [
      { path: ':id/:name', component: UserComponent }
    ]
  },

  {
    path: 'servers', component: ServersComponent, children: [
      { path: ':id', component: ServerComponent },
      { path: ':id/edit', component: EditServerComponent }
    ]
  },

];

```

But for it to work we have to specify ```<router-outlet></router-outlet>``` in the parent component to specify the spot where to render nested routes

## Preserving query parameters

To preserve query parameters when navigating from one route to another we can use ```queryParamsHandling``` property and set its value to ```preserve```

```typescript
 onEdit() {
    this.router.navigate(["edit"], { relativeTo: this.route, queryParamsHandling: "preserve" })
  }
```

## Redirecting one route to another

We can redirect route to another existing route as below

```typescript
{ path: "not-found", component: PageNotFoundComponent },
{ path: "**", redirectTo: "/not-found" }
```

Be careful with redirecting empty path. Since empty path is part of any path, it will always redirect. To solve this issue set ```pathMatch``` property to ```full```

```typescript
{ path: "", redirectTo: "/recipes", pathMatch: "full" }
```



## How can you separate routes into its own module

We can create AppRoutingModule as below and then import it into AppModule

```typescript
import { NgModule } from "@angular/core";
import { RouterModule, Routes } from "@angular/router";
import { HomeComponent } from "./home/home.component";
import { PageNotFoundComponent } from "./page-not-found/page-not-found.component";
import { EditServerComponent } from "./servers/edit-server/edit-server.component";
import { ServerComponent } from "./servers/server/server.component";
import { ServersComponent } from "./servers/servers.component";
import { UserComponent } from "./users/user/user.component";
import { UsersComponent } from "./users/users.component";

const appRoutes: Routes = [
    { path: '', component: HomeComponent },
    {
        path: 'users', component: UsersComponent,
        children: [
            { path: ':id/:name', component: UserComponent }
        ]
    },

    {
        path: 'servers', component: ServersComponent, children: [
            { path: ':id', component: ServerComponent },
            { path: ':id/edit', component: EditServerComponent }
        ]
    },
    { path: "not-found", component: PageNotFoundComponent },
    { path: "**", redirectTo: "/not-found" }

];


@NgModule({
    imports: [RouterModule.forRoot(appRoutes)],
    exports: [RouterModule]
})
export class AppRoutingModule {

}
```

```typescript
imports: [
    BrowserModule,
    FormsModule,
    AppRoutingModule
  ],
```

## Guards

Guards are a way of guarding routes. For instance we might want to ban access to certain routes if the user is not logged in. For this we can develop AuthGuard

### CanActivate Guard

```typescript
import { Injectable } from "@angular/core";
import { ActivatedRouteSnapshot, CanActivate, CanActivateChild, Router, RouterStateSnapshot } from "@angular/router";
import { Observable } from "rxjs/Observable";
import { AuthService } from "./auth.service";

@Injectable()
export class AuthGuard implements CanActivate, CanActivateChild {
    constructor(private authService: AuthService, private router: Router) { }
    canActivate(route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
        return this.authService.isAuthenticated().then(
            (authenticated: boolean) => {
                if (authenticated) {
                    return true;
                } else {
                    this.router.navigate(["/"])
                }
            }
        )
    }

    canActivateChild(route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
        return this.canActivate(route, state);
    }
}
```

Then attach AuthGuard to the corresponding route

```typescript
 {
        path: 'servers',
        //canActivate: [AuthGuard], 
        component: ServersComponent, 
        canActivateChild: [AuthGuard],
        children: [
            { path: ':id', component: ServerComponent },
            { path: ':id/edit', component: EditServerComponent }
        ]
    },
```

### CanDeactivate Guard

Let's say we want to display a confirm message to the user when he is trying to leave the route without saving his changes. For example EditServerComponent can have ```changesSaved``` boolean variable which indicates whether changes are saved or not and based on its value we want to display confirm message when user tries to leave the route. 

For this we can use ```CanDeactivateGuard```. But to associate it with the component we have to define an interface to be implemented by the component

```typescript
import { ActivatedRouteSnapshot, CanDeactivate, RouterStateSnapshot } from "@angular/router";
import { Observable } from "rxjs"

export interface CanComponentDeactivate {
    canDeactivate: () => Observable<boolean> | Promise<boolean> | boolean;
}

export class CanDeactivateGuard implements CanDeactivate<CanComponentDeactivate>{
    canDeactivate(component: CanComponentDeactivate,
        currentRoute: ActivatedRouteSnapshot,
        currentState: RouterStateSnapshot, nextState: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
        return component.canDeactivate();
    }
}
```

In the route definition

```typescript
{
                path: ':id/edit',
                component: EditServerComponent,
                canDeactivate: [CanDeactivateGuard]
            }
```

In the component

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Params, Router } from '@angular/router';
import { Observable } from 'rxjs';

import { ServersService } from '../servers.service';
import { CanComponentDeactivate } from './can-deactivate.service';

@Component({
  selector: 'app-edit-server',
  templateUrl: './edit-server.component.html',
  styleUrls: ['./edit-server.component.css']
})
export class EditServerComponent implements OnInit, CanComponentDeactivate {
  server: { id: number, name: string, status: string };
  serverName = '';
  serverStatus = '';
  allowEdit: boolean = false;
  changesSaved: boolean = false;

  constructor(private serversService: ServersService,
    private route: ActivatedRoute,
    private router: Router) { }

  ngOnInit() {
    console.log(this.route.snapshot.queryParams);
    console.log(this.route.snapshot.fragment);

    this.route.queryParams.subscribe(
      (queryParams: Params) => {
        this.allowEdit = queryParams['allowEdit'] === '1' ? true : false;
      }
    );

    const serverId: number = +this.route.snapshot.params['id'];
    this.server = this.serversService.getServer(serverId);
    this.serverName = this.server.name;
    this.serverStatus = this.server.status;
  }

  onUpdateServer() {
    this.serversService.updateServer(this.server.id, { name: this.serverName, status: this.serverStatus });
    this.changesSaved = true;
    this.router.navigate(["../"], { relativeTo: this.route })
  }

  canDeactivate(): Observable<boolean> | Promise<boolean> | boolean {
    if (!this.allowEdit) {
      return true;
    }

    if ((this.serverName !== this.server.name || this.serverStatus !== this.server.status)
      && !this.changesSaved) {
      return confirm("Do you want to discard the changes?");
    }else{
      return true;
    }
  }
}

```

## Capturing static data from route

Sometimes you might want to pass some static data with your route. 

```typescript
{ path: "not-found", component: ErrorPageComponent, data: { 'message': 'Page is not found! This is static message passed by route' } },
    { path: "**", redirectTo: "/not-found" }
```

Then capture that static data in your component

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Data } from '@angular/router';

@Component({
  selector: 'app-error-page',
  templateUrl: './error-page.component.html',
  styleUrls: ['./error-page.component.css']
})
export class ErrorPageComponent implements OnInit {
  errorMessage: string;

  constructor(private route: ActivatedRoute) { }

  ngOnInit(): void {
    this.errorMessage = this.route.snapshot.data['message'];

    this.route.data.subscribe(
      (data: Data) => {
        this.errorMessage = data['message'];
      }
    );
  }

}

```

## Dynamic data. Resolvers

Before loading a route let's say we want to fetch some data. For this we can define ```server-resolver.service.ts``` 

```typescript
import { Injectable } from "@angular/core";
import { ActivatedRouteSnapshot, Resolve, RouterState, RouterStateSnapshot } from "@angular/router";
import { Observable } from "rxjs/Observable";
import { ServersService } from "../servers.service";

interface Server {
    id: number,
    name: string,
    status: string
}

@Injectable()
export class ServerResolver implements Resolve<Server>{
    constructor(private serversService: ServersService) { }
    resolve(route: ActivatedRouteSnapshot, status: RouterStateSnapshot): Observable<Server> | Promise<Server> | Server {
        return this.serversService.getServer(+route.params['id']);
    }
}
```

And then use it to define ```resolver``` attribute of a route

```typescript
{ path: ':id', component: ServerComponent, resolve: { server: ServerResolver } },

```

Finally capture this data in the component

```typescript
ngOnInit() {
    this.route.data.subscribe(
      (data: Data) => {
        this.server = data['server'];
      }
    );
}
```

## Important note when using angular routes on real web server

It is important to remember that in the real world, real web server returns 404 page not found when we try to access routes defined by angular. To prevent this we either have to configure our web server to pass control to angular whenever it encounters 404 error or use hash to make web server ignore all the route after the domain, so that angular can handle them

```typescript
RouterModule.forRoot(appRoutes, {useHash:true})
```

# Observable

## What is an observable

We can think of observables as Data Sources. For instance when you make HttpRequest to a server to fetch some data. Observables are used to accomplish an asynchronous tasks. And as the name implies there is an Observer for an Observable. Observer implements following that can be returned by ```Observable```

- Handle data
- Handle Error
- Handle completion

## Simple observable example

```rxjs``` comes shipped with a function called interval that emits a number every whatever milliseconds we pass as an argument. In below example we are demonstrating simple observable and its unsubscription

```typescript
import { Component, OnDestroy, OnInit } from '@angular/core';

import { interval, Subscription } from 'rxjs';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit, OnDestroy {

  private firstSubscription: Subscription;

  constructor() { }

  ngOnInit() {
    this.firstSubscription = interval(1000).subscribe(
      (count) => {
        console.log(count);

      }
    );
  }

  ngOnDestroy(){
    this.firstSubscription.unsubscribe();
  }

}

```

## Custom observable

We can create our own custom observable as below

```typescript
const customIntObservable=Observable.create(observer=>{
      let count=0;
      setInterval(()=>{
        observer.next(count);
        count++;
      },1000)
    });

    this.firstSubscription=customIntObservable.subscribe(
      (count) => {
        console.log(count);
        
      }
    );
```

## Operators

Sometimes we might want to transform our data that we receive from an Observable before subscribing to it. That's where Operators come in handy. You can define operators using ```pipe``` method of the Observable.

```typescript
import {map, filter} from 'rxjs/operators';
...
const myOperator = customIntObservable.pipe(
      filter((data: number) => {
        return data > 0;
      }),
      map((data: number) => {
        return "Round : " + (data + 1);
      }));

    this.firstSubscription = myOperator.subscribe(
      (count) => {
        console.log(count);

      }, error => {
        console.log(error);
        alert(error);
      }, () => {
        console.log("Completed,boy!");

      }
    );
```

## Subject

Subject is like EventEmitters. It can be used to emit some data, for instance a boolean value to indicate whether used is activated or not

```typescript
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class UserService {

  isActivated = false;
  activatedSubject = new Subject<boolean>();

  constructor() { }

  activate() {
    this.isActivated = true;
    this.activatedSubject.next(this.isActivated);
  }

  deactivate() {
    this.isActivated = false;
    this.activatedSubject.next(this.isActivated);
  }
}

```

And we can subscribe to Subjects from the component just like in the case of Observables

```typescript
ngOnInit() {
    this.userService.activatedSubject.subscribe(
      (isActivatedFromService) => {
        this.isActivated = isActivatedFromService;
      }
    );
```

And remember that we should unsubscribe from Subjects when the component is destroyed hooking to OnDestroy method

```typescript
ngOnDestroy(){
  subjectSubscription.unsubscribe();
}
```

And remember that although Subjects are similar to EventEmitters you can't use them when emitting events from the component using ```Output``` 

# Forms

Angular has ```FormsModule``` to process forms.

## Template Driven and Reactive approach

Angular offers two approaches for handling forms

1. Template Driven approach : Angulars infers form structure from the template
2. Reactive approach : We define form structure in TypeScript which will then synchronize with the DOM

## Making FormsModule aware of controls

Pass ```ngModule``` to the input and specify ```name``` attribute

## Getting hold of form as javascript object

To get hold of form as javascript object we can use reference to html element.

```typescript
<form (ngSubmit)="onSubmit(f)" #f="ngForm">
.....
</form>
```

```f``` will have form input values as well as lots of metadata such as whether the form is valid or not

## Adding validations to form input

We can easiliy add validations as below, simply adding directives such as ```required``` or ```email```.

```typescript
<input 
            type="text" 
            id="username" 
            class="form-control" 
            ngModel 
            name="username"
            required>
....
<input 
            type="email" id="email" class="form-control" ngModel name="email" required email>
```

then we can leverage css to mark invalid input as red bordered

```css
input.ng-invalid.ng-touched{
  border:1px solid red;
}
```

To add validation to inputs of type number where we want to accept positive numbers only, we can leverage ```pattern``` validator

```html
 <div class="col-sm-2 form-group">
   <label for="amount">Amount</label>
   <input type="number" id="amount" 
          class="form-control" 
          name="amount" 
          ngModel 
          required
          [pattern]="'^[1-9]+[0-9]*$'">
</div>
```



## Displaying help messages for form inputs

We would want to display a message that indicates the form input is invalid and reason for it. For this we can use html element reference and ngIf directive

```html
<input 
            type="email" 
            id="email" class="form-control" 
            ngModel 
            name="email" 
            required 
            email
            #email="ngModel">
            <span *ngIf="!email.valid && email.touched" class="help-block">Please enter a valid email!</span>
```

## Setting default values with ngModel

We can set default values to form input tapping into ```ngModel``` property

```typescript
<select 
id="secret" 
class="form-control" 
[ngModel]="defaultQuestion"
name="secret">
```

And of course you will have to define ```defaultQuestion``` local variable.

## Grouping form controls

We can group form controls using ```ngModelGroup``` and we can set local reference to it and access it as javascript object by setting local reference something like ```#userData``` and set its value to ```ngModelGroup```

```html
<div id="user-data" ngModelGroup="userData" #userData="ngModelGroup">
  <div class="form-group">
    <label for="username">Username</label>
    <input type="text" id="username" class="form-control" ngModel name="username" required>
  </div>
  <button class="btn btn-default" type="button">Suggest an Username</button>
  <div class="form-group">
    <label for="email">Mail</label>
    <input type="email" id="email" class="form-control" ngModel name="email" required email #email="ngModel">
    <span *ngIf="!email.valid && email.touched" class="help-block">Please enter a valid email!</span>
  </div>
</div>
<p *ngIf="!userData.valid && userData.touched">User Data is invalid!</p>
```

## Radio buttons

We can add radio buttons as below. This example assumes that we define ```genders``` array with values ```['male','female']```

```html
 <div class="radio" *ngFor="let gender of genders">
   <label>
     <input 
            type="radio" 
            ngModel name="gender" 
            [value]="gender">{{gender}}
   </label>
</div>
```

## Setting form values

### Setting the values of all form controls

We pass all form controls with their respective values to ```setValue``` method

```typescript
this.signUpForm.setValue({
      userData:{
        username:suggestedName,
        email:''
      },
      secret:'',
      questionAnswer:'',
      gender:''
    })
```

### Setting specific form control's value

To set the value of a specific form control we can use NgForm's ```form.patchValue``` method

```typescript
this.signUpForm.form.patchValue({
      userData:{
        username:suggestedName
      }
    })
```

## Accessing form control values and resetting the form

```typescript
onSubmit() {
    this.submitted=true;
    this.user.username = this.signUpForm.value.userData.username;
    this.user.email = this.signUpForm.value.userData.email;
    this.user.secret = this.signUpForm.value.secret;
    this.user.answer = this.answer;
    this.user.gender = this.signUpForm.value.gender;
    this.signUpForm.reset();
  }
```

## Reactive Forms

### How to use Reactive Forms

In case of reactive forms we define our form in the typescript and then link it to the form html element defined in the template url. For reactive forms to work we have to import ```ReactiveFormsModule``` in AppModule.

```typescrip
signupForm:FormGroup;
```

### Defining reactive forms and linking them with the template

We can define reactive form as below

```typescript
import { Component, OnInit } from '@angular/core';
import { FormControl, FormGroup, Validators } from "@angular/forms";

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  genders = ['male', 'female'];
  signupForm: FormGroup;

  ngOnInit() {
    this.signupForm = new FormGroup({
      'userData': new FormGroup({
        'username': new FormControl(null, Validators.required),
        'email': new FormControl(null, [Validators.required, Validators.email])
      }),
      'gender': new FormControl('female')
    });
  }

  onSubmit() {
    console.log(this.signupForm);

  }
}

```

we can link reactive form to the template form html element

```html
<form [formGroup]="myForm">...</form>
```

To connect form controls to the template form controls

```html
<input type="text" formControlName="myFormControl">
```

### Grouping form controls

We simply nest ```FormGroup``` within top FormGroup

```typescript
myForm=new FormGroup({
  'userData': new FormGroup({
    'username': new FormControl(null),
    'email': new FormControl(null)
  })
})
```

Then we have to nest corresponding form controls within the newly defined FormGroup in the template

```html
<div formGroupName="userData">...</div>
```

to access them with ```get``` method

```html
<span *ngIf="!myForm.get('userData.username').valid">Enter valid username</span>
```

### FormArray : an array of form controls

We can define FormArrays to which we can dynamically push FormControls.

```typescript
import { Component, OnInit } from '@angular/core';
import { FormArray, FormControl, FormGroup, Validators } from "@angular/forms";

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  genders = ['male', 'female'];
  signupForm: FormGroup;

  ngOnInit() {
    this.signupForm = new FormGroup({
      'userData': new FormGroup({
        'username': new FormControl(null, Validators.required),
        'email': new FormControl(null, [Validators.required, Validators.email])
      }),
      'gender': new FormControl('female'),
      'hobbies':new FormArray([])
    });
  }

  onSubmit() {
    console.log(this.signupForm);
  }

  getHobbiesControls(){
    return (<FormArray>this.signupForm.get('hobbies')).controls;
  }

  onAddHobby(){
    (<FormArray>this.signupForm.get('hobbies')).push(new FormControl(null,Validators.required));
  }
}

```

Then defining Form Array in the template

```html
<div formArrayName="hobbies">
  <button class="btn btn-default" type='button' (click)="onAddHobby()">Add hobby</button>
  <div class="form-group" *ngFor="let hobbyControl of getHobbiesControls();let i=index">
    <input type="text" [formControlName]="i" class="form-control">
  </div>
</div>
```

To remove element with certain index from FromArray

```typescript
(<FormArray>this.myForm.get('ingredients')).removeAt(index)
```

To remove all elements from FromArray

```typescript
(<FormArray>this.myForm.get('ingredients')).clear()
```



### Custom validator

We can use functions as validators

```typescript
import { Component, OnInit } from '@angular/core';
import { FormArray, FormControl, FormGroup, Validators } from "@angular/forms";

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  genders = ['male', 'female'];
  signupForm: FormGroup;
  forbiddenNames = ["nohbus", "samrullo"]

  ngOnInit() {
    this.signupForm = new FormGroup({
      'userData': new FormGroup({
        'username': new FormControl(null, [Validators.required, this.checkForbiddenNames.bind(this)]),
        'email': new FormControl(null, [Validators.required, Validators.email])
      }),
      'gender': new FormControl('female'),
      'hobbies': new FormArray([])
    });
  }

 ...

  checkForbiddenNames(control: FormControl): { [s: string]: boolean } {
    if (this.forbiddenNames.indexOf(control.value) !== -1) {
      return { nameIsForbidden: true };
    }
    return null;
  }
}

```

### Tapping into errors property of the form controls

We can display proper messages that corresponds to specific validations by tapping into ```errors``` property of the form control

```html
<span *ngIf="!signupForm.get('userData.username').valid && signupForm.get('userData.username').touched"
      class="help-block">
  <span *ngIf="signupForm.get('userData.username').errors['nameIsForbidden']">This username is forbidden!</span>
  <span *ngIf="signupForm.get('userData.username').errors['required']">Username is required!</span>
</span>
```

### Asynchronoous validators

Validators like checking if the specified already exists in the database requires fetching data from the back end server, which in turn requires to be handled asynchronoously

```typescript
checkForbiddenEmails(control: FormControl): Promise<any> | Observable<any> {
    const promise = new Promise<any>((resolve, reject) => {
      setTimeout(() => {
        if (control.value === 'test@test.com') {
          resolve({ 'emailIsForbidden': true });
        } else {
          resolve(null);
        }
      }, 1500);
    });
    return promise;
  }
```

```typescript
        'email': new FormControl(null, [Validators.required, Validators.email],[this.checkForbiddenEmails])

```

# Pipes

## What are Pipes?

Pipes are what allow to transform an output in our template. Below is an example where you have a ```username``` variable defined in your component. And only when you display it in your template you want username to be in uppercase

```typescript
username:string="Subkhon"
```

```html
<p>{{username | uppercase}}</p>
```

## Pipe parmeters

We can pass arguments to the pipe after colon as below

```html
<p>{{somedate | date:'fullDate'}}</p>
```

## Built in pipes

You can check built in pipes in https://angular.io/api?query=pipe

## Your own pipe

You can create your own pipe by writing class as below. It should implement PipeTransform and should have a Pipe decorator where you specify the pipe name. Finally you should add your app to declarations in app.module.ts

```typescript
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({
    'name':'shorten'
})
export class ShortenPipe implements PipeTransform{
    transform(value:any){
return value.substr(0,10);
    }
}
```

We can parmeterize the pipe simply by definiing parmeters

```typescript
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({
    'name': 'shorten'
})
export class ShortenPipe implements PipeTransform {
    transform(value: any, limit: number) {
        if(value.length>limit){
            return value.substr(0, limit)+" ...";
        }
        return value;
    }
}
```

```html
<strong>{{ server.name | shorten:5 }}</strong> | 
```

Below is another example of a pipe that filters an array based on some condition and returns

We can create pipes from command line

```
ng generate pipe filter
```

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'filter'
})
export class FilterPipe implements PipeTransform {
  transform(value: any, filteredServer:string,propName:string) {
    const resultArr=[];
    for (const item of value){
      if(item[propName]===filteredServer){
        resultArr.push(item);
      }
    }
    return resultArr;
  }
}
```

```html
<ul class="list-group">
  <li
      class="list-group-item"
      *ngFor="let server of servers | filter:filteredServer:'status'"
      [ngClass]="getStatusClasses(server)">
    <span
          class="badge">
      {{ server.status}}
    </span>
    <strong>{{ server.name | shorten:5 }}</strong> | 
    {{ server.instanceType | uppercase}} | 
    {{ server.started | date:'fullDate'}}
  </li>
</ul>
```

## Pure and impure pipes

In the above example, if we filter once and add servers that match filter criteria, it won't display. Only when we change input text then we see it. This is default behaviour of the pipes, where they don't re-trigger every time data changes, to save performance. But we can change that behavior by setting ``pure`` to false in Pipe decorator

## Async pipe

Sometimes some data can be fetched from the server, so it will take it some time to have a valid value. And let's say we want to display that kind of data once it is fetched. This where we can apply ```async``` pipe

# HTTP Requests

## HTTP Request anatomy

1. HTTP Verb : POST, GET, PUT...
2. URL (API endpoint)
3. Headers (Metadata) : {"Content-Type":"application/json"}
4. Body : {"title":"New Post"}

## How to use HTTPClient

Import module ```HttpClientModule``` to unlock the http requests functionality. In the component to send http requests we have to import ```HttpClient``` and inject it in the constructor.

## Send POST request

We can send POST request as below. Remember that angular won't process our post request if we don't subscribe to it.

```typescript
onCreatePost(postData: { title: string; content: string }) {
    // Send Http request
    this.http.post('https://myangularbackend-26680-default-rtdb.firebaseio.com/posts.json',postData).subscribe(
      responseData=>{
        console.log(responseData);        
      }
    );
  }
```

## Fetching items from database

For this we can use ```HttpClient``` ```get``` method and subscribe to it as usual

```typescript
private fetchPosts(){
  this.http.get(this.postsEndpoint).subscribe(posts=>{
    console.log(posts);
  })
}
```

## Delete database items

We can delete all items in the database by sending DELETE request.

```typescript
deletePosts(){
  return this.http.delete(this.postsEndpoint);
}

```

```typescript
onClearPosts() {
  // Send Http request
  this.postsService.deletePosts().subscribe(()=>{
    this.loadedPosts=[];
  });
}

```

## Handle errors

We can handle errors by capturing as the second callback function to subscribe method

```typescript
onFetchPosts() {
  // Send Http request
  this.isFetching = true;
  this.postsService.fetchPosts().subscribe(posts => {
    this.isFetching = false;
    this.loadedPosts = posts;
  },error=>{
    console.log(error);

    this.error=error.message;
  });
}

```

You can define an error Subject that emits the error message to the components that use the service.

```typescript
error:Subject<string>=new Subject<string>();
...
this.http.post(this.postsEndpoint, postData).subscribe(
  responseData => {
    console.log(responseData);
    this.postsChanged.next(true);

  },error=>{
    this.error.next(error.message);
  }
);

```

You can use catchError operator to throw error

```typescript
import { catchError, map } from 'rxjs/operators';
import { Subject,throwError } from 'rxjs';

...

return this.http.get<{ [key: string]: Post }>(this.postsEndpoint)
  .pipe(map(
  (responseData) => {
    const postsArr = [];
    for (const key in responseData) {
      if (responseData.hasOwnProperty(key)) {
        postsArr.push({ ...responseData[key], id: key });
      }
    }
    return postsArr;
  }
)),catchError(errResp=>{
  this.error.next(errResp.message);
  return throwError(errResp);
})

```

## Http Headers

Sometimes you might want to set Http headers, to pass authorization credentials, to specify content type or to send some custom header that your API expects.

To set headers we leverage ```HttpHeaders``` object and pass key value javascript object to it

```typescript
import { HttpClient, HttpHeaders } from '@angular/common/http';
...
fetchPosts() {
  return this.http.get<{ [key: string]: Post }>(this.postsEndpoint,
                                                { headers: new HttpHeaders({ 'Custom-Header': "Hello" }) })
    .pipe(
    map(
      (responseData) => {
        const postsArr = [];
        for (const key in responseData) {
          if (responseData.hasOwnProperty(key)) {
            postsArr.push({ ...responseData[key], id: key });
          }
        }
        return postsArr;
      }
    ))
}


```

## Adding query parameters

We can add query parameters to the URL endpoint as below

```typescript
fetchPosts() {
  return this.http.get<{ [key: string]: Post }>(this.postsEndpoint,                         {
    headers: new HttpHeaders({ 'Custom-Header': "Hello" }),
    params:new HttpParams().set('print','pretty')
  })
    .pipe(
    map(
      (responseData) => {
        const postsArr = [];
        for (const key in responseData) {
          if (responseData.hasOwnProperty(key)) {
            postsArr.push({ ...responseData[key], id: key });
          }
        }
        return postsArr;
      }
    ))
}

```

if you want to use more than one query parameters

```typescript
fetchPosts() {
  let searchParams=new HttpParams();
  searchParams=searchParams.append('print','pretty');
  searchParams=searchParams.append('custom','key');

  return this.http.get<{ [key: string]: Post }>(this.postsEndpoint,
                                                {
    headers: new HttpHeaders({ 'Custom-Header': "Hello" }),
    params:searchParams
  })
    .pipe(
    map(
      (responseData) => {
        const postsArr = [];
        for (const key in responseData) {
          if (responseData.hasOwnProperty(key)) {
            postsArr.push({ ...responseData[key], id: key });
          }
        }
        return postsArr;
      }
    ))
}

```

## Tapping to Http events

we can tap to Http events by leveraging ```tap``` operator

```typescript
deletePosts() {
  return this.http.delete(this.postsEndpoint,{observe:'events'}).pipe(
    tap(event=>{
      if(event.type===HttpEventType.Response){
        console.log(event.body);
      }
    })
  );
}
```

## Response type

you can also specify ```responseType``` when sending http requests. By default responseType is json, but you can change it to text or blob if necessary.

```typescript
...
this.http.get('API endpoint url',{
  headers:...,
  params:...,
  responseType:'text'
})
```

## Interceptors

Sometimes we might want to execute some task before sending requests across our angular applciation. For instance tasks like appending auth headers. For this we can leverage interceptors

```typescript
import { HttpHandler, HttpInterceptor, HttpRequest } from "@angular/common/http";

export class AuthInterceptorService implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler) {
    console.log("Request is on its way");
    return next.handle(req);
  }
}
```

Then we have to add this service to providers in the following manner

```typescript
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
...
providers: [{
  provide: HTTP_INTERCEPTORS,
  useClass: AuthInterceptorService,
  multi: true
}],
```

Sometimes you might want to modify the request before sending it across angular applciation. Below is how you can do it.

```typescript
export class AuthInterceptorService implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler) {
    console.log("Request is on its way");
    const modifiedRequest=req.clone({headers:req.headers.append('Auth','xyz')});
    return next.handle(modifiedRequest);
  }
}
```

We can intercept responses in the following way

```typescript
import { HttpEventType, HttpHandler, HttpInterceptor, HttpRequest } from "@angular/common/http";
import { tap } from "rxjs/operators";

export class AuthInterceptorService implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler) {
    console.log("Request is on its way");
    const modifiedRequest=req.clone({headers:req.headers.append('Auth','xyz')});
    return next.handle(modifiedRequest).pipe(tap(event=>{
      console.log(event);
      if(event.type===HttpEventType.Response){
        console.log('Response arrived. Response body : ');
        console.log(event.body);                                
      }            
    }));
  }
}
```

We can define multiple interceptors. For example we could create another interceptor called LoggingInterceptorService and provide it in AppModule as well. But ordering becomes important. Interceptors will be executed in the order they are provided in AppModule