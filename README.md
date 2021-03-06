angulate2
===========
[![Scala.js](https://www.scala-js.org/assets/badges/scalajs-0.6.5.svg)](https://www.scala-js.org)
<!--[![Build Status](https://travis-ci.org/jokade/angulate2.svg?branch=master)](https://travis-ci.org/jokade/angulate2)-->

[Scala.js](http://www.scala-js.org/) bindings for [Angular 2](http://www.angular.io). The goal is to provide an API/ experience very similar to the [TypeScript API](https://angular.io/docs/ts/latest/guide/cheatsheet.html) of Angular 2.

**WARNING: This is work in progress (alpha status) and some information in this README is out of date!**
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
Extensions
----------
angulate2 provides some features not present in Angular2:

#### @Data annotation
All Scala `case class`es annotated with `@Data` will be represented as pure JavaScript data objects when compiled by Scala.js, with all constructor arguments exported to JavaScript. Hence, classes annotated with `@Data` are no longer Scala case classes at runtime (JavaScript), and it is (currently) not possible to use them for pattern matching, or their `copy` function.

It is possible to use `@Data` annotations on case classes defined in shared code (cross-projects), which allows consistent data representation and JSON serialization (e.g. when using [upickle](https://github.com/lihaoyi/upickle-pprint) on the JVM side). If you want to use the `@Data` annotation in shared code, add
```scala
  libraryDependencies += "de.surfice" %% "angulate2-stubs" % VERSION
```
to the sbt settings of the JVM project.

#### Debugging
Since angulate2 uses Scala macro annotations to transform classes at compile time, it is sometimes useful to inspect the generated code. To enable logging of the generated code to, you can annotate a class with `@debug`. This annotation will also print out a logging statement to the browser console whenever the class is instantiated at runtime. You may disable either of these features by explicitly setting the arguments of `@debug` to false. 


Status and Known Limitations
----------------------------
Angular2 features currently supported by angulate2:
* Core annotations: `@Component`, `@Injectable` (implemented as Scala macro annotations, i.e. the annotated class will be transformed during compilation)
* Http API: basic support for `get`, `post`, `put` and `delete`; `Observable` façade traits and extensions provided by [scalajs-rxjs](https://github.com/jokade/scalajs-rxjs).
* Router API: basic support for @RouteConfig

Beside a lot of Angular2 features currently not accessible from angulate2, there are the following limitations specific to angulate2:
* Classes annotated with macro annotations (`@Component`,`@Injectable`) currently can not have a Scala companion object.


License
-------
This code is open source software licensed under the [MIT License](http://opensource.org/licenses/MIT).
