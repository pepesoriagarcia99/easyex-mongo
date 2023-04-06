# Express-core

What is express-core? It is an **extension** that modifies the original syntax of express.

- It is inspired by spring boot.
- It makes use of the proposal of decorators ([plugin-proposal-decorators](https://babeljs.io/docs/babel-plugin-proposal-decorators)) from babel.


## Decorators
### @App
App is a **class** decorator.
Receives ip, port and apiRoot as configuration parameters.
This decorator instantiates the class to which it is applied to launch its constructor, so you can add additional configuration.
It also adds ip, port, apiRoot and the **express server instance** as class parameters.

```
@App({ ip : '0.0.0.0', port : 9000, apiRoot : '/' })
class Main {

  constructor() {
    this.server.app.use(formData.parse({ autoClean : true }));

    console.log('port: ', this.port);
  }
}
```
### @Controller
Controller is a **class** decorator.
Receives a path parameter that would be the parent path, the **path** is **required**.
The operation of the controller system is simple. It looks for all the files that contain the **@Controller** decorator and the instances.

> Support for **Get**, **Post**, **Delete**, **Put** , **Ws** methods.
> Just to decorate methods.


```
@Controller('/test')
class TestController {

  @Get('/ping')
  ping(req, res) {
    return res.success(200).json({ message : 'pong' });
  }
}
```

#### Middleware
To make a stack of different methods in the same route we can use the **Use**.
In the following example we see a /user/list access point with a role validation middleware.

```
@Controller('/user')
class UserController {

  @AutoWired(UserService) userService;

  @Get('/list')
  @Use(protect(['admin']))
  userList(req, res) {
    const users = this.userService.getAllUsers();

    res.success().json(users);
  }
}
```

### @Service
Service is a **class** decorator.

The **@Service** decorator instantiates the class for further injection into other classes.
The **@AutoWired** decorator used on fields injects the value of the instantiated class.

```
@Service
class ProductsService {

  getAllProducts() {
    return [
      { id   : 1, name : '' }
    ];
  }
}

// --------------------------

@Controller('/store')
class StoreController {

  @AutoWired(ProductsService) productsService;

  @Get('/products')
  @Use(protect(['client']))
  products(req, res) {
    const products = this.productsService.getAllProducts();

    res.success().json(products);
  }
}
```

### @Logger
Logger is a **class** decorator.
Insert the entire Logger module to the class.

```
@Logger
class Service {
  constructor() {
    this.$logger.info(''Message);
  }
}
```

> The **App**, **Controller** and **Service** decorators already contain it by default

### Addons
Some of the additional functionalities that the library contains are listed.
#### Banner
The banner functionality is added at the beginning of the project.
If a **banner.txt** file does not exist in the root of the project, use the one with the default library.

#### Logger
The **App**, **Controller**, and **Service** decorators add this functionality.

Logger can generate trace and access files.
Configurations:
* Allows its activation and deactivation (Whether you want to generate an output file or not).
* Allows different output levels **('info', 'debug', 'error', 'warn')**.
* Allows you to configure the path of the *.log and the name.
* Allows you to set a deletion interval.

```
this.$logger.info('New message');
this.$logger.error('Error message', error);
this.$logger.warn('Warn message');
```

#### Response
The response contains new methods that simplify responses a bit.

```
res.success(200).json({ message : 'pong' });
res.success(204);

res.badRequest().json({ message : 'Error' });
res.badRequest().end();

res.unauthorized().end();
res.notFound().end();
res.serverError().end();
```
