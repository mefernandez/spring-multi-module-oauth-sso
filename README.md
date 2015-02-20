# spring-multi-module-oauth-sso
An example of a multi-module web app using spring-security-oauth based on [this article series](http://spring.io/blog/2015/02/03/sso-with-oauth2-angular-js-and-spring-security-part-v) published by [Dave Syer](http://spring.io/team/dsyer).

## Introduction

The project is organized as a multi-module Java Maven project:
- An oauth-server module cloned from this repository: https://github.com/dsyer/spring-security-angular/tree/master/oauth2/authserver. This server runs in port 9999.
- Two oauth-client modules, two twin webapps that can be accessed at http://localhost:9991/client1 and http://localhost:9992/client2.

Both client webapps have a public `/index.html` and a secure `/private.html` pages. In order to access the private zone, the user must login, which is done via the oAuth server running at port 9999.

:children_crossing: _work in progress_

The goal is to allow the user login into one of the client web apps and be automatically logged into the other. This would allow to divide a traditional web application into modules that share the user login.
