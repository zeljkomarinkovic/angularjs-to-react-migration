# Migrating from AngularJS to React

This section briefly touches on all of the important parts of AngularJS using a simple example. 

| Concept               | Description                                                                                                                     | React alternative  |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------- | -----------------  |
| [Template](https://docs.angularjs.org/guide/concepts#template)            | HTML with additional markup                                                                                                      |                   | 
| [Directives](https://docs.angularjs.org/guide/concepts#directive)          | extend HTML with custom attributes and elements                                                                                  |                   | 
| [Model](https://docs.angularjs.org/guide/concepts#model)               | the data shown to the user in the view and with which the user interacts                                                         |                   | 
| [Scope](https://docs.angularjs.org/guide/concepts#scope)               | context where the model is stored so that controllers, directives and expressions can access it                                  |                   | 
| [Expressions](https://docs.angularjs.org/guide/concepts#expression)         | access variables and functions from the scope                                                                                    |                   | 
| [Compiler](https://docs.angularjs.org/guide/concepts#compiler)            | parses the template and instantiates directives and expressions                                                                  |                   | 
| [Filter](https://docs.angularjs.org/guide/concepts#filter)              | formats the value of an expression for display to the user                                                                       |                   | 
| [View](https://docs.angularjs.org/guide/concepts#view)                | what the user sees (the DOM)                                                                                                     |                   | 
| [Data Binding](https://docs.angularjs.org/guide/concepts#databinding)        | sync data between the model and the view                                                                                         |                   | 
| [Controller](https://docs.angularjs.org/guide/concepts#controller)          | the business logic behind views                                                                                                  |                   | 
| [Dependency Injection](https://docs.angularjs.org/guide/concepts#di)| Creates and wires objects and functions                                                                                          |                   | 
| [Injector](https://docs.angularjs.org/guide/concepts#injector)            | dependency injection container                                                                                                   |                   | 
| [Module](https://docs.angularjs.org/guide/concepts#module)              | a container for the different parts of an app including controllers, services, filters, directives which configures the Injector |                   | 
| [Service](https://docs.angularjs.org/guide/concepts#service)             | reusable business logic independent of views                                                                                     |                   | 


## Data binding

In the following example we will build a form to calculate the costs of an invoice in different currencies.

Let's start with input fields for quantity and cost whose values are multiplied to produce the total of the invoice:

```html
<div ng-app ng-init="qty=1;cost=2">
  <b>Invoice:</b>
  <div>
    Quantity: <input type="number" min="0" ng-model="qty">
  </div>
  <div>
    Costs: <input type="number" min="0" ng-model="cost">
  </div>
  <div>
    <b>Total:</b> {{qty * cost | currency}}
  </div>
</div>
```

Data binding in React can be achieved by using a `controlled input`. A controlled input is achieved by binding the value to a `state variable` and a `onChange` event to change the state as the input value changes.

```jsx
import React, { useState } from "react";
import ReactDOM from 'react-dom/client'

function App() {
  const [values, setValues] = useState({
    quantity: 0,
    cost: 0,
  });
  

  const handleChange = (e) => {
    let name = e.target.name;
    let value = e.target.value;

    setValues({ ...values, [name]: value });
  };

  return (
    <div>
      <b>Invoice:</b>
      <div>
        Quantity:{" "}
        <input
          type="number"
          name="quantity"
          min={0}
          value={values.quantity}
          onChange={handleChange}
        />
      </div>
      <div>
        Costs:{" "}
        <input
          type="number"
          name="cost"
          min={0}
          value={values.cost}
          onChange={handleChange}
        />
      </div>
      <div>
        <b>Total:</b> {values.quantity * values.cost}
      </div>
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById('app'));
```

## Adding UI logic: Controllers

Let's add some more logic to the example that allows us to enter and calculate the costs in different currencies and also pay the invoice.

  Edit in Plunker

[invoice1.js](https://docs.angularjs.org/)[index.html](https://docs.angularjs.org/)

```
angular.module('invoice1', [])
.controller('InvoiceController', function InvoiceController() {
  this.qty = 1;
  this.cost = 2;
  this.inCurr = 'EUR';
  this.currencies = ['USD', 'EUR', 'CNY'];
  this.usdToForeignRates = {
    USD: 1,
    EUR: 0.74,
    CNY: 6.09
  };

  this.total = function total(outCurr) {
    return this.convertCurrency(this.qty * this.cost, this.inCurr, outCurr);
  };
  this.convertCurrency = function convertCurrency(amount, inCurr, outCurr) {
    return amount * this.usdToForeignRates[outCurr] / this.usdToForeignRates[inCurr];
  };
  this.pay = function pay() {
    window.alert('Thanks!');
  };
});
```

What changed?

First, there is a new JavaScript file that contains a [controller](https://docs.angularjs.org/guide/controller). More accurately, the file specifies a constructor function that will be used to create the actual controller instance. The purpose of controllers is to expose variables and functionality to expressions and directives.

Besides the new file that contains the controller code, we also added an [`ng-controller`](https://docs.angularjs.org/api/ng/directive/ngController) directive to the HTML. This directive tells AngularJS that the new `InvoiceController` is responsible for the element with the directive and all of the element's children. The syntax `InvoiceController as invoice` tells AngularJS to instantiate the controller and save it in the variable `invoice` in the current scope.

We also changed all expressions in the page to read and write variables within that controller instance by prefixing them with `invoice.` . The possible currencies are defined in the controller and added to the template using [`ng-repeat`](https://docs.angularjs.org/api/ng/directive/ngRepeat). As the controller contains a `total` function we are also able to bind the result of that function to the DOM using `{{ invoice.total(...) }}`.

Again, this binding is live, i.e. the DOM will be automatically updated whenever the result of the function changes. The button to pay the invoice uses the directive [`ngClick`](https://docs.angularjs.org/api/ng/directive/ngClick). This will evaluate the corresponding expression whenever the button is clicked.

In the new JavaScript file we are also creating a [module](https://docs.angularjs.org/guide/concepts#module) at which we register the controller. We will talk about modules in the next section.

The following graphic shows how everything works together after we introduced the controller:

![](https://docs.angularjs.org/img/guide/concepts-databinding2.png)

## View-independent business logic: Services

Right now, the `InvoiceController` contains all logic of our example. When the application grows it is a good practice to move view-independent logic from the controller into a [service](https://docs.angularjs.org/guide/services), so it can be reused by other parts of the application as well. Later on, we could also change that service to load the exchange rates from the web, e.g. by calling the [exchangeratesapi.io](https://exchangeratesapi.io/) exchange rate API, without changing the controller.

Let's refactor our example and move the currency conversion into a service in another file:

  Edit in Plunker

[finance2.js](https://docs.angularjs.org/)[invoice2.js](https://docs.angularjs.org/)[index.html](https://docs.angularjs.org/)

```
angular.module('finance2', [])
.factory('currencyConverter', function() {
  var currencies = ['USD', 'EUR', 'CNY'];
  var usdToForeignRates = {
    USD: 1,
    EUR: 0.74,
    CNY: 6.09
  };
  var convert = function(amount, inCurr, outCurr) {
    return amount * usdToForeignRates[outCurr] / usdToForeignRates[inCurr];
  };

  return {
    currencies: currencies,
    convert: convert
  };
});
```

![](https://docs.angularjs.org/img/guide/concepts-module-service.png)

What changed?

We moved the `convertCurrency` function and the definition of the existing currencies into the new file `finance2.js`. But how does the controller get a hold of the now separated function?

This is where [Dependency Injection](https://docs.angularjs.org/guide/di) comes into play. Dependency Injection (DI) is a software design pattern that deals with how objects and functions get created and how they get a hold of their dependencies. Everything within AngularJS (directives, filters, controllers, services, ...) is created and wired using dependency injection. Within AngularJS, the DI container is called the [injector](https://docs.angularjs.org/guide/di).

To use DI, there needs to be a place where all the things that should work together are registered. In AngularJS, this is the purpose of the [modules](https://docs.angularjs.org/guide/module). When AngularJS starts, it will use the configuration of the module with the name defined by the `ng-app` directive, including the configuration of all modules that this module depends on.

In the example above: The template contains the directive `ng-app="invoice2"`. This tells AngularJS to use the `invoice2` module as the main module for the application. The code snippet `angular.module('invoice2', ['finance2'])` specifies that the `invoice2` module depends on the `finance2` module. By this, AngularJS uses the `InvoiceController` as well as the `currencyConverter` service.

Now that AngularJS knows of all the parts of the application, it needs to create them. In the previous section we saw that controllers are created using a constructor function. For services, there are multiple ways to specify how they are created (see the [service guide](https://docs.angularjs.org/guide/services)). In the example above, we are using an anonymous function as the factory function for the `currencyConverter` service. This function should return the `currencyConverter` service instance.

Back to the initial question: How does the `InvoiceController` get a reference to the `currencyConverter` function? In AngularJS, this is done by simply defining arguments on the constructor function. With this, the injector is able to create the objects in the right order and pass the previously created objects into the factories of the objects that depend on them. In our example, the `InvoiceController` has an argument named `currencyConverter`. By this, AngularJS knows about the dependency between the controller and the service and calls the controller with the service instance as argument.

The last thing that changed in the example between the previous section and this section is that we now pass an array to the `module.controller` function, instead of a plain function. The array first contains the names of the service dependencies that the controller needs. The last entry in the array is the controller constructor function. AngularJS uses this array syntax to define the dependencies so that the DI also works after minifying the code, which will most probably rename the argument name of the controller constructor function to something shorter like `a`.

## Accessing the backend

Let's finish our example by fetching the exchange rates from the [exchangeratesapi.io](https://exchangeratesapi.io/) exchange rate API. The following example shows how this is done with AngularJS:

  Edit in Plunker

[invoice3.js](https://docs.angularjs.org/)[finance3.js](https://docs.angularjs.org/)[index.html](https://docs.angularjs.org/)

```
angular.module('invoice3', ['finance3'])
.controller('InvoiceController', ['currencyConverter', function InvoiceController(currencyConverter) {
  this.qty = 1;
  this.cost = 2;
  this.inCurr = 'EUR';
  this.currencies = currencyConverter.currencies;

  this.total = function total(outCurr) {
    return currencyConverter.convert(this.qty * this.cost, this.inCurr, outCurr);
  };
  this.pay = function pay() {
    window.alert('Thanks!');
  };
}]);
```

What changed? Our `currencyConverter` service of the `finance` module now uses the [`$http`](https://docs.angularjs.org/api/ng/service/$http), a built-in service provided by AngularJS for accessing a server backend. `$http` is a wrapper around [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) and [JSONP](http://en.wikipedia.org/wiki/JSONP) transports.
