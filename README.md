# spring-multi-module-oauth-sso
An example of a multi-module web app using spring-security-oauth based on [this article series](http://spring.io/blog/2015/02/03/sso-with-oauth2-angular-js-and-spring-security-part-v) published by [Dave Syer](http://spring.io/team/dsyer).

## Introduction

The project is organized as a multi-module Java Maven project:
- An oauth-server module cloned from this repository: https://github.com/dsyer/spring-security-angular/tree/master/oauth2/authserver. This server runs in port 9999.
- Two oauth-client modules, two twin webapps that can be accessed at http://localhost:9991/client1 and http://localhost:9992/client2.

Also, a [Redis server](http://redis.io/) is needed to store sessions. [Spring Session](http://projects.spring.io/spring-session/) is used to connect to Redis to persist sessions.

Both client webapps have a public `/index.html` and a secure `/private.html` pages. In order to access the private zone, the user must login, which is done via the oAuth server running at port 9999.

The goal is to allow the user login into one of the client web apps and be automatically logged into the other. This would allow to divide a traditional web application into modules that share the user login.

# Try it

Follow these steps to try it right from the source code:

1. Clone this repo or download the zip file and Import into Eclipse as an Existing Maven Project. Note that 3 projects will be imported: the oAuth server and the 2 client web apps.
2. Download [Redis database](http://redis.io/download), unzip it, `cd` into Redis directory and run `make` to compile it.
3. Run `src/redis-server` to start Redis in port `6379`.
4. Launch oAuth server project from Eclipse by right-clicking on `AuthserverApplication` class -> `Run As..` -> `Java Application`.
5. Set a breakpoint in method `createSessionCookie()` in class `org.springframework.session.web.http.CookieHttpSessionStrategy`, right after the `TODO` comment:

```java
        sessionCookie.setPath(cookiePath(request));
        // TODO set domain?

        if(sessionIds.isEmpty()) {
```

**More on this later.**

6. Launch both client projects the same way, but pick `Debug As..` this time.
7. Go to http://localhost:9991/client1. The execution will stop at the breakpoint. Open Eclipse's `Display View` and execute this line of code: `sessionCookie.setPath("/")` every time it stops there, then continue the execution with `F8`.
8. Follow the `Login` link and authenticate with `user:password`.
9. Now click follow the `Private` link. You're in!
10. Go to http://localhost:9992/client2 and repeat step 7.
11. As you've already logged in client1 web app, you can go directly to client2's `Private` page!

So, **why the breakpoint stuff??**. In short, to store a **domain-wide `SESSION` Cookie in the browser with the same `Path` for both client webapps**. Otherwise, [Spring Session]() creates a `Cookie` with different `Path`s for each webapp, thus preventing Single-Sign-On. Disable the breakpoint and restart the web applications to see what happens.

There's already [an issue in Spring Session project](https://github.com/spring-projects/spring-session/issues/155) to configure `CookieHttpSessionStrategy` in order to set a custom `Path`. Let's hope it makes it into [Spring Session 1.0.1](http://projects.spring.io/spring-session/) release. 
