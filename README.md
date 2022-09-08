# Migrating from AngularJS to React

## Setup

### Package mangers

Three major players exist in the field of package managers today:

#### npm
[npm](https://github.com/npm/cli) - a JavaScript package manager

npm is the world's largest software registry. Open source developers from every continent use npm to share and borrow packages, and many organizations use npm to manage private development as well.

npm consists of three distinct components:

-   the website
-   the Command Line Interface (CLI)
-   the registry

Use the [_website_](https://npmjs.com/) to discover packages, set up profiles, and manage other aspects of your npm experience. For example, you can set up [organizations](https://www.npmjs.com/features) to manage access to public or private packages.

The [_CLI_](https://docs.npmjs.com/cli/npm) runs from a terminal, and is how most developers interact with npm.

The [_registry_](https://docs.npmjs.com/misc/registry) is a large public database of JavaScript software and the meta-information surrounding it.

Npm is shipped with Node.js, so no extra steps is needed. Besides downloading the [Node.js installer](https://nodejs.org/en/download/) for your OS, it has become common practice to use CLI tools for managing software versions.

---

#### Yarn
[Yarn](https://github.com/yarnpkg/berry) - Yarn is a modern package manager split into various packages.

Yarn is a package manager for your code. It allows you to use and share code with other developers from around the world. Yarn does this quickly, securely, and reliably so you don't ever have to worry.

Yarn allows you to use other developers' solutions to different problems, making it easier for you to develop your software. If you have problems, you can report issues or contribute back on [GitHub](https://github.com/yarnpkg/berry), and when the problem is fixed, you can use Yarn to keep it all up to date.

Code is shared through something called a **package**. A package contains all the code being shared as well as a `package.json` file (called a **manifest**) which describes the package.

You can Yarn in different ways, e.g., as an npm package or via Corepack. [Corepack](https://nodejs.org/api/corepack.html) was created by the folks of Yarn Berry. The initiative was originally named [package manager manager (pmm)](https://github.com/nodejs/TSC/issues/904) 🤯and [merged with Node](https://github.com/nodejs/node/pull/35398) in LTS v16.

With the help of Corepack, you don’t have to install npm’s alternative package managers “separately” because Node includes Yarn Classic, Yarn Berry, and pnpm binaries as shims. These shims allow users to run Yarn and pnpm commands without having to explicitly install them first, and without cluttering the Node distribution.


---

#### pnpm (performant npm)
[pnpm (performant npm)](https://github.com/pnpm/pnpm) - Fast, disk space efficient package manager

pnpm uses a content-addressable filesystem to store all files from all module directories on a disk. When using npm or Yarn, if you have 100 projects using lodash, you will have 100 copies of lodash on disk. With pnpm, lodash will be stored in a content-addressable storage, so:

1.  If you depend on different versions of lodash, only the files that differ are added to the store. If lodash has 100 files, and a new version has a change only in one of those files, `pnpm update` will only add 1 new file to the storage.
2.  All the files are saved in a single place on the disk. When packages are installed, their files are linked from that single place consuming no additional disk space. Linking is performed using either hard-links or reflinks (copy-on-write).

As a result, you save gigabytes of space on your disk and you have a lot faster installations! If you'd like more details about the unique `node_modules` structure that pnpm creates and why it works fine with the Node.js ecosystem

You can install pnpm as an npm package with `$ npm i -g pnpm`. You can also [install pnpm with Corepack](https://pnpm.io/installation#using-corepack): 

`$ corepack prepare pnpm@6.24.2 --activate`.

---

#### Conclusion

The current state of package managers is great. We have virtually attained feature parity among all major package managers. But still, they do differ under the hood quite a bit.

Virtually, we’ve achieved feature-parity among all package managers, so most likely we’ll decide which package manager to use based on non-functional requirements, like installation speed, storage consumption, or how it meshes with our existing workflow.

Despite this parity, though, package managers differ under the hood. Traditionally, npm and Yarn have installed dependencies in a flat [`node_modules` folder](https://docs.npmjs.com/cli/v8/configuring-npm/folders#node-modules). But this dependency resolution strategy is not free of criticism.

Thus, pnpm has introduced some new concepts to store dependencies more efficiently in a nested `node_modules` folder. Yarn Berry goes even further by ditching `node_modules` completely with its Plug’n’Play (PnP) mode.

pnpm looks like npm at first because their CLI usage is similar, but managing dependencies is much different; pnpm’s method leads to better performance and the best disk-space efficiency. Yarn Classic is still very popular, but it’s considered legacy software and support might be dropped in the near future. Yarn Berry PnP is the new kid on the block, but hasn’t fully realized its potential to revolutionize the package manager landscape once again


---

###### References
<sub> [LogRocket blog](https://blog.logrocket.com/javascript-package-managers-compared) </sub>

---

### Build tools

#### create-react-app
[CRA](https://create-react-app.dev)

Create React App is an officially supported way to create single-page React applications. It offers a modern build setup with no configuration.

You **don’t** need to install or configure tools like webpack or Babel. They are preconfigured and hidden so that you can focus on the code.

Create a project, and you’re good to go.

-   **One Dependency:** There is only one build dependency. It uses webpack, Babel, ESLint, and other amazing projects, but provides a cohesive curated experience on top of them.
    
-   **No Configuration Required:** You don't need to configure anything. A reasonably good configuration of both development and production builds is handled for you so you can focus on writing code.
    
-   **No Lock-In:** You can “eject” to a custom setup at any time. Run a single command, and all the configuration and build dependencies will be moved directly into your project, so you can pick up right where you left off.

---

###### References 
<sub>[CRA repo](https://github.com/facebook/create-react-app)</sub>

---

#### Vite 

[Vite](vitejs.dev) - Next Generation Frontend Tooling

Vite (French word for "quick", pronounced [`/vit/`](https://cdn.jsdelivr.net/gh/vitejs/vite@main/docs/public/vite.mp3), like "veet") is a new breed of frontend build tooling that significantly improves the frontend development experience. It consists of two major parts:

-   A dev server that serves your source files over [native ES modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules), with [rich built-in features](https://vitejs.dev/guide/features.html) and astonishingly fast [Hot Module Replacement (HMR)](https://vitejs.dev/guide/features.html#hot-module-replacement).
    
-   A [build command](https://vitejs.dev/guide/build.html) that bundles your code with [Rollup](https://rollupjs.org/), pre-configured to output highly optimized static assets for production.
    

In addition, Vite is highly extensible via its [Plugin API](https://vitejs.dev/guide/api-plugin.html) and [JavaScript API](https://vitejs.dev/guide/api-javascript.html) with full typing support.


---
###### references 
<sub>[Vite documentation](https://vite.dev/guide)</sub>

---

#### Overview

##### Server Side Rendering

| CRA | Vite|
| ---------------------- | ---------------------- |
| CRA doesn't support SSR out of the box.  However, you can still configure it. It just takes more effort to setup SSR with your preferred server and configuration. The development team doesn't have plans to support this in the near future. They [suggest other tools](https://github.com/facebook/create-react-app/blob/2da5517689b7510ff8d8b0148ce372782cb285d7/README.md#popular-alternatives) for this use case. | A lot plugins that will allow server side rendering  |

---

##### Configurability

I think this is point where these tools are very different and your decision can depend on this factor

| CRA | Vite|
| ---- | ------ |
| Create React App doesn't leave you a lot of room to configure it.  Configurations like webpack config cannot be changed unless you stray away from normal CRA way (eject, rescripts, rewired, craco). Basically, you have to use what's configured in `react-scripts` which is the core of CRA. | Almost everything is [configurable](https://vitejs.dev/config) and extendable with a lot of vite plugins |

---

##### Maintainability

| CRA | Vite|
| ---- | ------ |
| CRA is very opinionated.  If you keep updated with the releases of CRA, it's not hard to maintain. | Vite is well maintained with regular updates from big community |

---

##### Typescript

| CRA                                                                                                                              | Vite                                                                                                                                                    |
| -------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
|Supports out of the box. You can initialize CRA app with typescript with <br> `npx create-react-app my-app --template typescript` | Supports typescript out of the box. You can initialize vite app with typescript on installation or just make new `touch tsconfig.json`  config file |

---

#### Performance

---
`npm install`

First thing we usually do after creating new project is install packages. Measurement done with `time` command `time npm install`. Command is run on fresh installment of both tool.

| name | time npm install                                                                                                |
| ---- | --------------------------------------------------------------------------------------------------------------- |
| CRA  | ![image](https://user-images.githubusercontent.com/23746207/189117348-f53a5eab-77fa-4189-b381-3121c46b7580.png) |
| Vite | ![image](https://user-images.githubusercontent.com/23746207/189117711-5fc33e3d-5ef0-4956-83aa-ae07c8979da6.png) |

---
`npm run build`

The production build is another topic worth measuring

| name | time npm run build                                                                                              |
| ---- | --------------------------------------------------------------------------------------------------------------- |
| CRA  | ![image](https://user-images.githubusercontent.com/23746207/189117201-21cd1d9f-130f-4e46-867e-aaa0e7a6a8fb.png) |
| Vite | ![image](https://user-images.githubusercontent.com/23746207/189117568-859a961b-b907-45ab-ac2c-d9c6edbd3581.png) |

---

#### Conclusion
 
From the above comparison results, Vite is really speedy and provides an awesome development experience. It benefits both development and production.

---

### Packages that we probably going to need/use

| function     | name                            |
| ------------ | ------------------------------- | 
| HTTP-client  | Axios or Fetch api              |
| Router       | react-router                    |
| Translations | react-translate + react-i18next |
| Forms        | Formik                          |

## Conceptual Overview

This section briefly touches on all of the important parts of AngularJS using a simple example. 

| Concept               | Description                                                                                                                     | React alternative              |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| [Template](https://docs.angularjs.org/guide/concepts#template)            | HTML with additional markup                                                                                                      | JSX                            | 
| [Directives](https://docs.angularjs.org/guide/concepts#directive)          | extend HTML with custom attributes and elements                                                                                  | JSX + React Components         | 
| [Model](https://docs.angularjs.org/guide/concepts#model)               | the data shown to the user in the view and with which the user interacts                                                         |                                | 
| [Scope](https://docs.angularjs.org/guide/concepts#scope)               | context where the model is stored so that controllers, directives and expressions can access it                                  |                                | 
| [Expressions](https://docs.angularjs.org/guide/concepts#expression)         | access variables and functions from the scope                                                                                    |                                | 
| [Compiler](https://docs.angularjs.org/guide/concepts#compiler)            | parses the template and instantiates directives and expressions                                                                  |                                | 
| [Filter](https://docs.angularjs.org/guide/concepts#filter)              | formats the value of an expression for display to the user                                                                       |                                | 
| [View](https://docs.angularjs.org/guide/concepts#view)                | what the user sees (the DOM)                                                                                                     |                                | 
| [Data Binding](https://docs.angularjs.org/guide/concepts#databinding)        | sync data between the model and the view                                                                                         |                                | 
| [Controller](https://docs.angularjs.org/guide/concepts#controller)          | the business logic behind views                                                                                                  |                                | 
| [Dependency Injection](https://docs.angularjs.org/guide/concepts#di)| Creates and wires objects and functions                                                                                          |                                | 
| [Injector](https://docs.angularjs.org/guide/concepts#injector)            | dependency injection container                                                                                                   |                                | 
| [Module](https://docs.angularjs.org/guide/concepts#module)              | a container for the different parts of an app including controllers, services, filters, directives which configures the Injector |                                | 
| [Service](https://docs.angularjs.org/guide/concepts#service)             | reusable business logic independent of views                                                                                     | React Components + React hooks | 


### Data binding

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

### Adding UI logic: Controllers

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

### View-independent business logic: Services

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

### Accessing the backend

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
