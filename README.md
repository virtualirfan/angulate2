angulate2
===========
[![Scala.js](https://www.scala-js.org/assets/badges/scalajs-0.6.5.svg)](https://www.scala-js.org)
<!--[![Build Status](https://travis-ci.org/jokade/angulate2.svg?branch=master)](https://travis-ci.org/jokade/angulate2)-->

[Scala.js](http://www.scala-js.org/) bindings for [Angular 2](http://www.angular.io). The goal is to provide an API/ experience very similar to the [TypeScript API](https://angular.io/docs/ts/latest/guide/cheatsheet.html) of Angular 2.

**WARNING: This is work in progress (alpha status)!**  
Most of the features of Angular2 are still missing and the API may change at any time without notice!

For Scala.js bindings to [Angular 1.x](https://angularjs.org) see [scalajs-angulate](https://github.com/jokade/scalajs-angulate).

There is a separate repository with [Examples](https://github.com/jokade/angulate2-examples).


Getting Started
---------------
### SBT Settings
Add the following lines to your `project/plugins.sbt`:
```scala
resolvers += Resolver.sonatypeRepo("snapshots")

addSbtPlugin("de.surfice" % "sbt-angulate2" % "0.1-SNAPSHOT")
```
and this to your `build.sbt`:
```scala
enablePlugins(Angulate2Plugin)
```
The current version of angulate2 is built for Angular2-beta.1 and Scala.js 0.6.5+.

### Defining a Component and Bootstrapping
```scala
import angulate2._

// Components are defined by annotating a class with @Component
// (with the same semantics as the Angular 2 TypeScript API)
@Component(
  selector = "my-app",
  template = "<h1>Hello, {{name}}!</h1>"
)
class AppComponent {
  // all public members are available in the template
  val name = "Alice"
}
```
```html
<!DOCTYPE html>
<html>
<head>
  <title>My first angulate2 App</title>
  <script src="https://code.angularjs.org/2.0.0-beta.1/Rx.umd.min.js"></script>
  <script src="https://code.angularjs.org/2.0.0-beta.1/angular2-polyfills.min.js"></script>
  <!-- Note: the current minified version of angular2-all doesn't work :( -->
  <script src="https://code.angularjs.org/2.0.0-beta.1/angular2-all.umd.js"></script>
  <script src="target/scala-2.11/firstapp-fastopt.js"></script>
  <!-- Note: generated by angulate2 - must be included AFTER firstapp-fastopt.js -->
  <script src="target/scala-2.11/annotations.js"></script>
  <script>
    document.addEventListener('DOMContentLoaded',function() {
      // start the application
      ng.platform.browser.bootstrap(AppComponent);
    });
  </script>
</head>
<body>
  <my-app></my-app>
</body>
</html>
```
**Important:** Don't forget to load the file `target/scala-2.11/annotations.js` **after** the JS files generated by Scala.js! 

### Dependency Injection
```scala
import angulate2._

@Injectable
class FriendsService {
  val names = js.Array("Aarav","Martín","Shannon","Ariana","Kai")
}

@Component(
  selector ="display",
  template = "<p>Friends: {{names}}</p>",
  providers = @@[FriendsService]   // inject FriendsService into this component
)
class DisplayComponent(friends: FriendsService) {
  val names = friends.names.mkString(", ")
}
```

License
-------
This code is open source software licensed under the [MIT License](http://opensource.org/licenses/MIT).
