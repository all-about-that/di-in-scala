
DI(Dependency injection)Its a popular concept for decoupling client and server implementations.
DI encourages you to use the constructor patterns which means passing the target dependency as 
a param when the creation source object happens.
DI in scala can be achieved by 2 ways:
1. Frameworks
    a. subcut 
    b. scaldi 
    c. guice 
    d. Macwire
    e. Grafter
    f.  Airframe

2. Pure Scala

    a. Cake pattern =>Notes: its an anti pattern. https://kubuszok.com/2018/cake-antipattern/ 
    b. Reader Monad => Cats Notes: Remove dependencies from function arguments, and remodel the functions as partially-curried on those dependencies. 
        pass them to monadic functions with run() method(not via function arguments).But it has some performance overhead and it makes the scope of dependencies ambiguous.

    c. Dependency Injection in Functional Programming : This approach needs to use IO Monad library like Cats Effect.




| Dynamic Runtime Binding /Compile time dependency check | Type safety | Auto-wiring | Constructor injection             | Lazy/eager evaluation switch     | Provider Bindings | Life-Cycle management                                                                                                                              | Additional notes                                                                            |                                                                                                                                                                                                                                                                                                                                                            |
|--------------------------------------------------------|-------------|-------------|-----------------------------------|----------------------------------|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                                                        |             |             |                                   |                                  |                   |                                                                                                                                                    |                                                                                             |                                                                                                                                                                                                                                                                                                                                                            |
|                                                        |             |             |                                   |                                  |                   |                                                                                                                                                    |                                                                                             |                                                                                                                                                                                                                                                                                                                                                            |
| Pure scala                                             | Compile     | Yes         | Yes                               | Yes(Manual argument passing)     | Lazy only         | Limited need to use implicits                                                                                                                      | Need to use IO monad library like Cats                                                      |                                                                                                                                                                                                                                                                                                                                                            |
|                                                        |             |             |                                   |                                  |                   |                                                                                                                                                    |                                                                                             |                                                                                                                                                                                                                                                                                                                                                            |
| MacWire                                                | Compile     | Yes         | Yes                               | Yes                              | Lazy only         | Use wireWith                                                                                                                                       | Yes(inject interceptor with using reflection)                                               | Use wire keyword to create dependency                                                                                                                                                                                                                                                                                                                      |
|                                                        |             |             |                                   |                                  |                   |                                                                                                                                                    |                                                                                             | No need for lazy vals.                                                                                                                                                                                                                                                                                                                                     |
| Google Guice                                           | Dynamic     | No          | Yes                               | Yes(Requires @Inject annotation) | Both              | Yes Need to define special classes called Provider.:                                                                                               | Yes but needs an extension called airlift                                                   |                                                                                                                                                                                                                                                                                                                                                            |
|                                                        |             |             |                                   |                                  |                   | https://github.com/google/guice/wiki/InjectingProviders                                                                                            | https://github.com/airlift/airlift/tree/master/bootstrap/src/main/java/io/airlift/bootstrap |                                                                                                                                                                                                                                                                                                                                                            |
|                                                        |             |             |                                   |                                  |                   | And @provider annotation                                                                                                                           |                                                                                             |                                                                                                                                                                                                                                                                                                                                                            |
| Scaldi                                                 | Dynamic     | No          | Yes (Need to extends with Module) | Yes                              | Both              | Yes use toProvider                                                                                                                                 | Yes                                                                                         | Scaldi uses implicit Injector parameter, which is necessary for the implementation of annotation/reflection-free injection mechanism. Scaldi also has several features that I haven't seen in guice, like conditional bindings, propertiy injector or macro for constructor injector (which is similar to macwire, but uses scaldi's injection mechanism). |
|                                                        |             |             |                                   |                                  |                   |                                                                                                                                                    |                                                                                             |                                                                                                                                                                                                                                                                                                                                                            |
|                                                        |             |             | Example:                          |                                  |                   | Example: https://github.com/Mironor/play-silhouette-mongodb-seed/blob/9ea66fd80b66f92f39a6499600efb731cd07f0f6/app/utils/di/SilhouetteModule.scala |                                                                                             |                                                                                                                                                                                                                                                                                                                                                            |
|                                                        |             |             | class AkkaModule extends Module   |                                  |                   |                                                                                                                                                    |                                                                                             |                                                                                                                                                                                                                                                                                                                                                            |




Reader Monad:

Plain construction injection:

val userRepo = new UserRepoImpl
val permRepo = new PermissionRepoImpl
val repo = Repo(userRepo, permRepo) 

val createUserResp = UserHandlerWithConstructor.createUser(repo, User(1001, "lambda", "admin"))


Injection using monad:
A type parameter, a constructor that takes an element of that type, and a flatMap method

val userRepo = new UserRepoImpl
val permRepo = new PermissionRepoImpl
val repo = Repo(userRepo, permRepo) 

val createUserResp = UserHandlerWithMonad.createUser(User(1001, "lambda", "admin")).run(repo) 
Signature of function: def createUser(user: User): Reader[Repo, Long]






References:
https://github.com/scaldi/scaldi
https://scala.libhunt.com/categories/647-modularization-and-dependency-injection
https://gist.github.com/gvolpe/1454db0ed9476ed0189dcc016fd758aa

https://groups.google.com/forum/#!topic/scaldi/TYU36h7kGqk

Providers: https://github.com/google/guice/wiki/InjectingProviders

P.S: Oleg Ilyenko has passed away so not sure about the maintenance of scaldi. Not to mention he was creator of sangria too :sad:
