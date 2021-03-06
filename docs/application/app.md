---
id: app
title: App
sidebar_label: App
---

appolo `app` contains all the application context the injector and the http server.

it can be inject to any class and exists on every request

```typescript
var {createApp}  from 'appolo';
(function async (){
  let app = await createApp().launch();
})()
```

can be injected
```typescript
@define()
export class FooManager{
    @inject() app:App
}
```
or accessed from  `req` `controller` or `middleware`
```typescript
@define()
export class FooManager{
   @controller()
   export class LoginController extends Controller{
       @post("/login/")
       public async loginUser(req:IRequest,res:IResponse){
           //req.app.options
       }
   }
}
```

## Usage
### options
#### `app.options:IOptions`
getter returns the launch option
```typescript
app.options.port // 8080
```
### set
#### `app.set(name: keyof IOptions, value: any)`
set  any launch option
app.set("port",8080) // 8080

### env
#### `app.env: IEnv`
geter return the current env
```typescript
app.env.name // production
```
### parent
#### `app.parent:IApp`
return the parent app in loaded in module
### injector
#### `app.injector :Injector`
return app injector instance
```typescript
app.injector.get("someObject")
```
### launch
#### `app.launch():Promise<App>`
launch the app and return the app instance when finished

```typescript
var {createApp}  from 'appolo';
(function async (){
  let app = await createApp().launch();
})()
```
### exported
#### `app.exported :{ fn: Function, path: string,define:Define }[]`
return array of all the exported classes the where found in the src folder
- fn - class constructor
- path - file path of the class
- define the injector definition object
```typescript
app.use(fn:Middleware):App
add middleware to the request
app.use(bodyParser.json())
    .use(function (req:IRequest, res: IResponse, next: NextFn) {
        res.setHeader("Access-Control-Allow-Origin", "*");
        next();
    });
```
### module
#### `app.module(module:Module):Promise<void>`
register custom module

```typescript
await app.module(async function(env:any,inject:appolo.Injector){
    let myModuleObject = {data:'test'};
    await toSomeThing();
    inject.addObject('myModuleObject',myModuleObject);
});
```
### reset
#### `app.reset():Promise<void>`
reset the app clean all assets and closes the server
### server
#### `server(): http.Server | https.Server`
return the http server instance 

### handle
#### `app.handle(request: IncomingMessage, response: ServerResponse)`
the function handles the http request useful for tests
```typescript
import * as request from 'supertest';
it('should run request', async () => {
    let res = await request(app.handle).get('/someurl')
})
```
