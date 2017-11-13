# Angular Learn Notes
## Establish Environment
1. node install (node 6.9.0&up, npm 3.0.0&up).
1. TypeScript install `npm install -g typescript`
1.  angular CLI install  `npm install -g @angular/cli`
    1. may require `brew install watchman`
    1. angular CLI now is using Typescript 2.1, not supporting 2.5 `Enums`.

## Angular CLI Commands
* create project `ng new <project-name>`
* serve the project locally `ng serve` || `ng serve --port <port-number>`
* generating component `ng generate component <component-name>`
* build application `ng build --target=<target-environment> --base-href <root-path>`, target-environment is usually production, root-path is usually /.
    * root-path: `/.`, `/path/`. No quote mark required.
* deploy application: this step is only to move built application to the server. In book example, it uses `now` which is a serverless host, but it can be viable through servers. Think of my way to deploy through openshift.

## File Explanation
* .angular-cli.json: defines angular-cli entry point and more.
* _src_
    * _app_
        * app.modules.ts: defines angular AppModule, angular boots from a module instead of a component directly.
            * declarations: components defined in this module
            * imports: import dependencies (modules), for use in templates or dependency injection.
            * providers: for dependency injection.
            * bootstrap: tells top-level component.
        * app.component.ts: defines the component.
        * app.component.html: definds template.
        * app.component.css: defines stylesheet.
        * _component-name_
            * component-name.component.*
        * _vendor_: 3rd party library.
    * _assets_
        * _images_: image source.
    * _environments_
    * favicon.ico
    * index.html
    * main.ts: angular-cli entry point.
    * polyfills.ts
    * style.css: main css file.
    * test.ts
    * tsconfig.app.json
    * tsconfig.spec.json
    * typings.d.ts

## Design tips
**cheatsheet: https://angular.io/guide/cheatsheet**
* Import Dependencies: `import { things } from 'where'`.
* Decorators (define selector/template/css): `@Component({})` (inline template for template shorter than a page or url template).
* TS class: controller and more.
* Selector tag `<component-name></component-name>` : load component in page.
* Accept Input: import `Input` and use `@Input()` in class to define an input property.
* Passing Input: use attribute `[target-property]="source-reference-name"` inside target selector tag. `<component-name [property]="reference-name">`
* Accept Output: import `Output` and use `@Output() event-name: EventEmitter<Type>` in class to define an output property.
* Passing Output: use attribute `(event-name)="event-handle-function($event)"` inside target selector tag. `<component-name (event)="handle($event)">`
    * Read Output: $event is the object that got emitted.
    * Emit Event (Write Output): eventEmitter.emit(object);
    * _NOTICE_: `EventEmitter` should be imported in parent component.
    * angular built-in events: click, mousedown, mousemove, dbl-click, etc.
* Binding actions to event: `(event)="function(params)"`
    * `click` event: function should return false, otherwise it will propagate to the parent components - follow an empty link and reload the page.
* Binding input (resolve): `<input #object-name>`
* Define property: `@decoration property: type`
* Define function: `function(param: type): type {}`
* Host Binding: import `HostBinding` and use `@HostBinding(things)` in class to define things to apply to the "host" of this component, like `'attr.class'`. **This idea is to control host inside the component instead of outside the component. Because the component is used in its parent component and attributes are set in the parent not the component itself; But by using Host Binding the componet can modify its attribute directly and there will be no need to touch the parent view.**
* Define Model: in component's component-name.model.ts, export the model as class.
* `the Law of Demeter`:  a given object should assume as little as possible about the structure or properties of other objects. Although this may look redundant, it is better considering object-oriented and MVC. **Fat Models, Skinny Controllers.**
* Modules: the upper level component must be in the same module as the target components it uses, or has its module to import modules that contains the target component it uses.

## Decoration List:
* NgModule
    * BrowserModule
    * FormsModule: ngModel, NgForm
    * ReactiveFormsModule: formControl, ngFormGroup
* Component
* OnInit
* Input
* Output
* HostBinding

## Angular Built-In
### Inputs
* <img [src]>

### Outputs
* (click)
* <form (ngSubmit)>

### Directives: to output dynamic data
* NgIf: `*ngIf="expression"`
* NgSwitch: `[ngSwitch]="expression"`, `*ngSwitchCase`, `*ngSwtichDefault`(optional). Can same case can be assigned to multiple DOM element inside, but a single thing can only be assigned one case.
* NgStyle: `[style.cssproperty]="expression"`, or `[ngStyle]="{key: expression, key: expression}"`
* NgClass: 
    * `[ngClass]="{className: boolean-expression}"`
    * `[ngClass]="[classname, classname]"`(_classname array only accept classnames, no key value accepted._)
    * `[class.className]="expression"`
* NgFor: `*ngFor='let item of items`, or `*ngFor="let item of items; let num = index`(index here is a notation only, cannot be directly used in render expression, but can be assigned to index and use `let index = index`.)
    * sort: define array through a sorting function, return sorted array, this sort will be a live sort responding to user interaction.
* NgNonBindable: `<tag ngNonBindable>`, to render exact text including {{}}.

### Forms: to accept user interaction data
* FormControl: `[formControl]="expression"`Angular user input control.
    * value: any
    * status: 
    * errors: StringMap<string, any>
    * dirty: boolean
    * valid: boolean
    * touched: boolean, if input has been touched.
    * hasError('errorName', 'error'): boolean, specify error.
* FormGroup: wrapper of FormControl collection.
    * hasError('errorName', 'controlName'): boolean specify error and control, where control can be omitted.
* FormsModule: no import required in component.
    * NgForm: `<form>` automatically use it, except those forms with formGroup or with ngNoForm attribute.
        * it provides a FromGroup named ngForm (already created), which can be assigned to an alias variable `#v="ngForm"`.
    * NgModel: `<input name="variablename" ngModel>`, provides one-way data binding to a FormControl (already created and added to parent FormGroup).
* ReactiveFormModule: customized FormGroup and FormControl, using FormBuilder. imports required in component.
    * _Notice: formGroup must be declared and created in component._
        * To use an existing formGroup: `<form [formGroup]="formGroupName">`, or assign in component class.
        * To use an existing formControl: `<input [formControl]="formGroupName.controls['controlName']">`, or `<input [formControl]="formGroupName.get('controlName')">`, or assign in component class.
    * FormBuilder: `import { FormBuilder, FormGroup} from '@angular/forms'`, and `constructor(variable: FormBuilder){}`
        * Methods: `control()` creates a FormControl, `group({formControlName: [defaultValue, Validators.componse(Validators.required, customValidatorName)]})` creates a FormGroup where things inside [] are optional.
* Validators:
    * Built-in Validators: `required`.
    * Custom Validator: `function validatorName(control, FormControl): StringMap<string, boolean> { return isPassed? null : {errorName: true}}`
* Observers:
    * `formControl.valueChanges.subscribe( (value: any) => { ... });`
    * `formGroup.valueChanges.subscribe( (form: any) => { ... });`
* two-way binding: **NOT recommended** `<input [formControl]="formGroupName.controls['controlName']" [(ngModel)]="bindingVariableName">`

### Dependency Injection: Service/Singleton & Factory
* Dependency Injections uses interface to make mock instance possible for unit test purpose, as well as several other benefits.
    * Singleton implementation: Service, a 'global' Singleton object, for example UserService for current user.
        * `import { Injectable } from '@angular/core'; import { Interfaces } from ''; @Injectable() export class ... [implements interface]{ }`, interface is optional.
    * Factory: a function that can return any object when injected, accepting arguments in Injectable's constructor.
        * `import { Injectable } from '@angular/core'; import { Interfaces } from ''; @Injectable() export class ... { constructor(private/public paramterInterfaceImplementation: Interface) {} ...}`
* Dependency in Angular consists of:
    * Provider: maps a token to a list of dependencies, telling how to create an object, given a token.
    * Injector: holds a set of bindings, telling what to resolve and inject when creating obejcts.
    * Dependency: what is being injected.
* `ReflectiveInjector`
    * `resolveAndCreate([Injectables])`
    * `get(Injectable)`
* Normal Dependency Injection:
    * Provider: NgModule to register. `import {Injectables} from '';  @NgModule({ imports: [], providors: [Injectables]})`
        * Using Injectable Class: `providers: [Injectables]`, or `providers: [ { provide: tokenName, useClass: className} ]`
        * Using a Value: `providers: [ { provide: tokenNameString, useValue: value} ]`, this helps for switching global paths like API_URL.
        * Using Factory: `import {Interfaces} from '';  import {Injectables} from '';  providers: [ { provide: tokenName, useFactory: () => { const paramterInterfaceImplementation: Interface = ...; return new Injectable(paramterInterfaceImplementation)} } ]`
            * If Injectable also has dependencies: `import {Interfaces} from '';  import {Injectables} from '';  providers: [ { provide: tokenName, deps: [Dependencies], useFactory: (dep: DepClass, ...) => { const paramterInterfaceImplementation: Interface = ...; return new Injectable(paramterInterfaceImplementation)} } ]`
    * Injector: decorators to specify. `import {Injectable} from ''; export class ... { constructor(...) {} }` (Singleton)
        * Using Injectable Class: `constructor(private/public injectable: Injectable) {}`, private/public here is a shorthand for declaring this.injectable and pass in constructor.
        * Using a Value: `constructor(@Inject(tokenNameString) injectable: type) {}`
        * Using Factory: same as a Class.

### HTTP (Observables)
* @angular/http: `import { HttpModule, Http, Response, RequestOptions, Headers} from '@angular/http'`
* Inject Http: `constructor(private http: Http){}`
* Async http request: Angular uses Observables. (RxJs)
    * `Http.request()` returns Observable.
    * `Observable.subscribe()` handles response, `.subscribe(onSuccess, onError, onCompletion)`, 2nd and 3rd handler can be omitted.
    * `Observable.subscribe()` handles response.
        *  Streaming operators: `.map()`, `.filter()`, `.debounceTime()`, `.do()`, `.switch()`

### Routing
* 2 methods:
    * hash-based routing: `/#/....`
    * HTML5: `history.pushState`
* Import: `import { RouterModule, Routes } from '@angular/router'`
* Define Routes: `const routes: Routes = [ { path: '...', component: componentName, redirectTo: '...', canActivate: [], children: childrenRoutes } ]`
* Install Routes: `@NgModule { ..., imports: [ RouterModule.forRoot(routes), ...] }`
    * add `<base href="/">` to index.html head.
    * Or `@NgModule { ..., providers: [ { provide: APP_BASE_HREF, useValue: '/' }, ...] }`
    * for any children routes, in their child module, use `RouterModule` instead of `RouterModule.forRoot(...)`
* Use Routes in page: 
    * link: `<a [routerLink]="['/path']"></a>`
    * outlet: `<router-outlet></router-outlet>`
* Router Parameters: `path/:param`
    * Import in component: `import { ActivatedRoute } from '@angular/router'`
    * component constructor: `constructor( private route: ActivatedRoute) { route.params.subscribe(params => { this.id = params['id']; }) }`
    * component constructor: `constructor( private route: ActivatedRoute) { route.queryParams.subscribe(params => { this.query = params['query'] || ''; }) }`
    * link:  `<a [routerLink]="['/path', param]"></a>`
* Manually Navigate: `this.router.navigate(['path'], { queryParams: {query: query} }).then();`
    * Benefit: persisting the search term.
* Ng Serve takes HTML5 routing, however, it is better to use hash-based routing.
* Router Hooks:
    * Create Ng Guard: `ng generate guard logged-in`
        * This guard takes adventage of `CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot`
    * Configure Router: 
        * import: `import { LoggedInGuard }`, `import { AUTH_PROVIDERS }`
        * providers: `[AUTH_PROVIDERS, LoggedInGuard ]`
        * Routes Definition: `{path: 'protected', component: ProtectedComponent, canActivate: [ LoggedInGuard ] }`
        * **Notice**: client-side route protection should be considered a form of _user-experience_ and **not** one of security.
        * **TODO**: you must protect it with serverside authentication. That is, require an API key (or auth token) from the user which is validated by the server on every request for data.
* Redirecting and linking nested routes: use abstract path like `['/rootSiblings']` or `[/.../...]`

## Architacture Notes
### Global:
* All query/modifications push to server (NodeJS): host routings as RESTful API.
* All application routing push to front end (Angular): host a static folder on NodeJS.

### Server-Side:
* RESTful API see object/collections from the application view point, not database view point. Operations on a single API collection may touch multiple database collections.
* RESTful API also provides special/complex functionalitys defined by this request path. In this case the method is at designer preference. _I'll still follow the API default method rules, unless I need a secured query for which I must use POST method._
* Route protection requires an API key (or auth token) from the user which is validated by the server on every request for data.

### Client-Side:
* All view (responsive) push to Bootstrap.
* All interactive/animation push to Angular.
* Route protection is only a form of _user-experience_ and **not** one of security.

### Angular:
* To setup a global setting which can be accessed many places in app: Use Service.
* For easier testing: use ng command each time creating things. Commands: https://github.com/angular/angular-cli/wiki

### Bootstrap:
