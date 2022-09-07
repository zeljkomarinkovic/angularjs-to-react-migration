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


## View-independent business logic: Services



## Accessing the backend


