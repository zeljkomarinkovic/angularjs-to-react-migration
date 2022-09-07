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
    qty: 1,
    cost: 2,
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
          name="qty"
          min={0}
          value={values.qty}
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
        <b>Total:</b> {values.qty * values.cost}
      </div>
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById('app'));
```

## Adding UI logic: Controllers

Let's add some more logic to the example that allows us to enter and calculate the costs in different currencies and also pay the invoice.

```js
/******************
*** invoice1.js ***
*******************/

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
```html
<!----------------
--- index.html ---
------------------>

<div ng-app="invoice1" ng-controller="InvoiceController as invoice">
  <b>Invoice:</b>
  <div>
    Quantity: <input type="number" min="0" ng-model="invoice.qty" required >
  </div>
  <div>
    Costs: <input type="number" min="0" ng-model="invoice.cost" required >
    <select ng-model="invoice.inCurr">
      <option ng-repeat="c in invoice.currencies">{{c}}</option>
    </select>
  </div>
  <div>
    <b>Total:</b>
    <span ng-repeat="c in invoice.currencies">
      {{invoice.total(c) | currency:c}}
    </span><br>
    <button class="btn" ng-click="invoice.pay()">Pay</button>
  </div>
</div>
```

This converted to react would look like simple component. By wrapping input fileds and button we obtain controll over that form and if field is required and empty it will ask for some value or you would not be able to "pay"

```jsx
import { useState } from "react";

function App() {
  const [values, setValues] = useState({
    qty: 1,
    cost: 2,
    inCurr: "EUR",
    currencies: ["USD", "EUR", "CNY"],
    usdToForeignRates: {
      USD: 1,
      EUR: 0.74,
      CNY: 6.09,
    },
  });

  const total = function total(outCurr) {
    return convertCurrency(values.qty * values.cost, values.inCurr, outCurr);
  };

  const convertCurrency = function convertCurrency(amount, inCurr, outCurr) {
    return (
      (amount * values.usdToForeignRates[outCurr]) /
      values.usdToForeignRates[inCurr]
    );
  };

  const pay = function pay() {
    window.alert("Thanks!");
  };

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
          name="qty"
          min={0}
          value={values.quantity}
          onChange={handleChange}
          required={true}
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
          required
        />
        <select
          onChange={handleChange}
          name="inCurr"
          defaultValue={values.inCurr}
        >
          {values.currencies.map((c, index) => (
            <option key={index}>{c}</option>
          ))}
        </select>
      </div>
      <div>
        <b>Total:</b>
        <span ng-repeat="c in invoice.currencies">
          {values.currencies.map((c) => {
            return ` ${c} ${total(c).toFixed(2)}`;
          })}
        </span>
        <br />
        <button className="btn" onClick={pay}>
          Pay
        </button>
      </div>
    </div>
  );
}

export default App;
```

## View-independent business logic: Services

Right now, the `InvoiceController` contains all logic of our example. When the application grows it is a good practice to move view-independent logic from the controller into a [service](https://docs.angularjs.org/guide/services), so it can be reused by other parts of the application as well.

```js
/******************
*** finance2.js ***
*******************/

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

```js
/******************
*** invoice2.js ***
*******************/

angular.module('invoice2', ['finance2'])
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

```html
<!----------------
--- index.html ---
------------------>

<div ng-app="invoice2" ng-controller="InvoiceController as invoice">
  <b>Invoice:</b>
  <div>
    Quantity: <input type="number" min="0" ng-model="invoice.qty" required >
  </div>
  <div>
    Costs: <input type="number" min="0" ng-model="invoice.cost" required >
    <select ng-model="invoice.inCurr">
      <option ng-repeat="c in invoice.currencies">{{c}}</option>
    </select>
  </div>
  <div>
    <b>Total:</b>
    <span ng-repeat="c in invoice.currencies">
      {{invoice.total(c) | currency:c}}
    </span><br>
    <button class="btn" ng-click="invoice.pay()">Pay</button>
  </div>
</div>
```

A direct counterpart to a service in vanilla React is a component. As opposed to Angular, React components don't have to exist in DOM. Similarly to services and components/directives in Angular, the separation of concerns in React can be provided with [container and presentational components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0). Container component can handle business logic, while presentation logic goes to presentational component.

Since React favours functional approach, reused code doesn't necessarily goes to a class and can be expressed with functional composition instead.

Dependency injection pattern is provided with component hierarchy in React. It can be implemented in several common ways to make data (like service instance) available for entire application or a part of it, e.g. via deeply passed props, context API, third-party state management (Redux, MobX).

```jsx
import React, { useState, useEffect } from 'react'

const fetchData = () => fetch(...).then(res => res.json());
const processData = data => ...;
const fetchProcessedData = () => fetchData().then(processData);

const ContainerComponent = () => {
  const [state, setState] = useState({});

  useEffect(() => {
    fetchProcessedData().then((data) => {
      setState({ data });
    });
  }, [fetchProcessedData]);

  return state.data && <PresentationalComponent data={data} />;
};
```

`PresentationalComponent` is injected with a dependency through `data` prop.

The same example would be possible to implement with Angular components but this would result in unwanted DOM elements.

When Redux is used for state management, things like fetching (side effects) are handled by extensions that serve this purpose, e.g. redux-thunk, redux-saga, etc. While synchronous processing is handled by reducers.

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
