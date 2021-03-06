---
layout: docs
title: How to Use Services
permalink: /docs/use/service_registry/
---

## Registering a Service

[Services](/docs/concepts/services/) can be registered in the for the following:

* [Aspects](/docs/concepts/aspects/) - used when the application is running
* [Workbenches](/docs/concepts/workbenches/) - used when a Workbench is running
* [Tests](/docs/concepts/testing/) - used when executing tests

The benefit of this is that you can register different services for these scenarios. For example, you may register a test mock service that enables assertions in tests in the Tests scenario and you may register a dummy service in a Workbench scenario to facilitate the development of a [Blade](/docs/concepts/blades/).

Services can be registered through code or configuration and can also be deregistered via code.

## Code

Services are instances of service classes which are identified by a name. They are registered with the `ServiceRegistry` passing the name and an instance of the class.

The following service may be defined in `myapp/libs/mylib/MyService.js`. This defines that the full require path of the class will be `mylib/MyService`:

```js
function MyService() {
}

MyService.prototype.doSomething = function() {
  console.log( 'something' );
};
```

In your initialisation code (`App.js` for an Aspect or `index.html` for a Workbench) you can register the service:

```js
var MyService = require( 'mylib/MyService' );
ServiceRegistry.registerService( 'my.something-service', new MyService() );
```

## XML configuration

`aliases.xml` files can be found in a few locations within a BRJS application:

* `<aspect>/resources/`
* `<blade>/workbench/resources/`
* `*/tests/test-unit/js-test-driver/resources/`

These allow you to set up the services that are registered for the given alias (service) name:

```xml
<aliases xmlns="http://schema.caplin.com/CaplinTrader/aliases" useScenario="dev">
  <alias name="my.something-service" class="mylib.MyService"/>
</aliases>
```

The `useScenario="dev"` attribute is only present in test and development scenarios

<div class="alert alert-info">
  <p>Note: the <code>alias</code> element will allow be updated to support a <code>requirePath</code> attribute to be consistent with the BRJS use of <code>require</code>. <a href="https://github.com/BladeRunnerJS/brjs/issues/724">See GitHub for more details</a></p>
</div>

In the example above a new instance of the `mylib/MyService` is created and registered with the ServiceRegistry with the `my.something-service` identifier.

## Accessing a Service

The MyService that was registered earlier can be accessed via the logical name we gave it, `my.something-service`, anywhere in the application or test code:

```js
var ServiceRegistry = require( 'br/ServiceRegistry' );
var myService = ServiceRegistry.getService( 'my.something-service' );
myService.doSomething();
```

A more convenient short-hand for the above code is as follows:

```js
var myService = require( 'service!my.something-service' );
myService.doSomething();
```

If a service is defined in an `aliases.xml` but is not retrieved from the ServiceRegistry in any application or test code, the BRJS dependency analysis system will notice this and therefore not register it with the ServiceRegistry. See [Dependency Analysis](http://bladerunnerjs.org/docs/concepts/dependency_analysis/) for more information.

## De-registering a Service

The service with the identifier my.something-service can be removed from the ServiceRegistry using the deregisterService function.

```js
var ServiceRegistry = require( 'br/ServiceRegistry' );
ServiceRegistry.deregisterService( 'my.something-service' );
```

## Checking if a Service is Registered

It is possible to check if a service with and identifier (e.g. `my.something-service`) is registered as follows:

```js
var ServiceRegistry = require( 'br/ServiceRegistry' );
var isRegistered = ServiceRegistry.isServiceRegistered( 'my.something-service' );
```

## Using different services in different situations

Often, you want to run one set of services in the application, but then a different set when running tests, perhaps composed primarily of test doubles. BladeRunner supports 'scenarios' to enable you to specify different aliases for particular situations. Since the services are picked up from aliases, this enables you to configure a different set of services.

Typically, you'll use the default scenario when you're running the application, and use a different scenario when running tests.

You can configure the scenario you are using by setting the `useScenario` attribute on the top level tag in an `aliases.xml` file. The `aliases.xml` file goes in a resources folder in your `js-test-driver` directory if you are changing the scenario for a test (although it could potentially go in an aspect or a workbench).

```xml
<aliases usescenario="test" xmlns="http://schema.caplin.com/CaplinTrader/aliases">
  <!--  extra overrides go here if necessary -->
</aliases>
```

## Where Next?
<!-- TODO: enable this link once the docs have been updated since the URLs will change
Read the [JavaScript API documentation](http://apidocs.bladerunnerjs.org/latest/js/index.html#br.ServiceRegistry.html).
-->
You can see an example of a service being defined and used within an application in the [Knockout BRJS Todo MVC example app](https://github.com/BladeRunnerJS/brjs-todomvc-knockout). The service is defined in the `todomvc` library found in the `libs/todomvc` directory.
