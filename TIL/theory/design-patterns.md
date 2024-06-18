# Design Patterns out in the wild 

  I was tired of seeing some unrelated examples when it comes to explaining design patterns like cars,students etc. And looked for a technical explanation which I couldn't find it all in a single place. And below are the list of examples for the design pattern that I have seen/used so far in the modern software/web.

- **Singleton** - Initializing DB, Redis, firebase instance
- **Factory** - Initializing like with options createConfig() for dev/prod/staging env
- **Builder** - constructor with many params to use builder pattern. In Jest, we use builder pattern via expect().toBe();
- **Abstract Factory** - For example we are writing a application for both online/offline we can define a factory like this: Connectivity -> ConnectivityLocal or Remote
- **Bridge Pattern** - Capacitor Plugin For Android/IOS/Web (Platform Independent) 
- **Prototype** - cloned object instead of creating new 
- **Adapter** - In modern web dev, if you want to deploy to different hosting sites like cloudflare/vercel, you will use the adapter as target in vite that converts your build to appropriate api of the hosting environment. 
- **Proxy** - new Proxy() in js to change the few language features of js (get/set) etc. 
- **Decorator** - python has @decorator; Decorators dynamically alter the functionality of a function, method, or class without having to directly use subclasses or change the source code of the function being decorated
- **Observer**(like pub/sub) + **Iterator** (Rxjs)
- **Strategy** - It may prove useful for specifying policies in framework design. Using STRATEGY, clients may parameterize the implementation. For example, error-handling is typically delegated to the application. Such a design may be realized by letting the client provide a strategy to be invoked upon an error. By those means, the error may be handled in an application specific manner, which may stretch between simply ignoring the error to logging it or even reboot the system (Patterns in C book)
- **Mediator** - Middleware (Auth, CORS etc)
