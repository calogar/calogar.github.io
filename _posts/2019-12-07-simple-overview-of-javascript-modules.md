---
title: "Simple Overview of JavaScript Modules"
date: 2019-12-07 19:48:00 +0800
categories: [JavaScript]
tags: [Modules, RequireJS, CommonJS, ES6]
toc: false
---

The concept of "modules" is an essential piece of every curated programming language. When we write code, it is only natural to divide and organize it into **separated, encapsulated pieces, that can be reused in many parts of our apps** and even in other projects maintained by different people.

### Old style modules

JavaScript is somehow a special case because it was originally designed to address simpler, script-related tasks, where defining global variables could be sufficient. In those times, programmers had to define their own modularized code by using module patterns. One way of achieving this is by using **closures**.

```javascript
var bakery = (function() {
    var bakingItems = 0;
    function bake(message) {
        console.log(message);
        bakingItems++;
        console.log(`The oven is baking ${bakingItems} things.`);
        return message;
    }
    return {
        bakeCake: function() {
            return bake("I'm baking a cake!");            
        },
        bakeMuffins: function() {
            return bake("I'm baking muffins!");            
        },
        bakePie: function() {
            return bake("I'm baking a pie!");            
        }
    };
})();
```

By closure, we mean an anonymous function that is inmediately executed when it is created. In this example, the function exposes an object with a series of methods, defining an API. Note that, in JavaScript, a closure is able to **share the same context between the method calls**, as the value of the `bakingItems` variable is being remebered and incremented as expected.

```html
<!DOCTYPE html5>
<html>
  <body>
    <h3>Modules with closures</h3>
    <script src="bakery-closure.js"></script>
    <script>
      bakery.bakeCake();    // I'm baking a cake!    The oven is baking 1 things.
      bakery.bakeMuffins(); // I'm baking muffins!   The oven is baking 2 things.
      bakery.bakePie();     // I'm baking a pie!     The oven is baking 3 things.
    </script>
  </body>
</html>
```

But JavasScript has come a long way, and now it can be used to create full web apps, backend apps and event desktop or mobile apps. As the need arose, we now have **two different implementations of a module system**.

### Sync modules: CommonJS

[CommonJS](https://requirejs.org/docs/commonjs.html) is defined in an synchronous way, which means that **modules are loaded secuentially**. In this case, instead of using a closure, we assing our API object to the `exports` propety of the `module` object.

```javascript
var bakingItems = 0;
function bake(message) {
    console.log(message);
    bakingItems++;
    console.log(`The oven is baking ${bakingItems} things.`);
    return message;
}
module.exports = {
    bakeCake: function() {
        return bake("I'm baking a cake!");            
    },
    bakeMuffins: function() {
        return bake("I'm baking muffins!");            
    },
    bakePie: function() {
        return bake("I'm baking a pie!");            
    }
};
```

Then, in order to import the module into our code, we use the `require` funcion which CommonJS provides.

```javascript
var bakery = require("./bakery-commonjs");

bakery.bakeCake();    // I'm baking a cake!    The oven is baking 1 things.
bakery.bakeMuffins(); // I'm baking muffins!   The oven is baking 2 things.
bakery.bakePie();     // I'm baking a pie!     The oven is baking 3 things.
```

In this case, if we had to import ten modules, CommonJS would load them one by one, which is not really a problem in **server / backend apps like Node**. But in a web / mobile enviroment, this is not the best suitable approach.

### Async modules: RequireJS

[RequireJS](https://requirejs.org/) is born as an implementation of the **Asynchronous Module Definition (AMD) specification**. It allows us to create a module by wrapping the code with the `define` function (and optionally adding some dependencies):

```javascript
define(function() {
    // ... rest of the code, just like in the closure example.
    return {
        bakeCake: function() {
            return bake("I'm baking a cake!");            
        },
        bakeMuffins: function() {
            return bake("I'm baking muffins!");            
        },
        bakePie: function() {
            return bake("I'm baking a pie!");            
        }
    };
});
```

Then, in order to import it we would use the same function name as before: `require`.

```html
<!DOCTYPE html5>
<html>
  <body>
    <h3>Modules with RequireJS</h3>
    <!-- We import RequireJS so we can use it. -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.3.6/require.min.js"></script>
    <script>
      require(["./bakery-requirejs"], function(bakery) {
        bakery.bakeCake();    // I'm baking a cake!    The oven is baking 1 things.
        bakery.bakeMuffins(); // I'm baking muffins!   The oven is baking 2 things.
        bakery.bakePie();     // I'm baking a pie!     The oven is baking 3 things.
      });
    </script>
  </body>
</html>
```

The most interesting part of this approach is that, if we would do something like the following, each line would trigger a new HTTP request to fetch the module:

```javascript
<script src="js/sidebar.js"><script> // Makes HTTP call.
<script src="js/profile.js"><script> // Makes HTTP call.
<script src="js/home.js"><script>    // Makes HTTP call.
```

But RequireJS allows us to require all the modules so **all of them are fetched at the same time**.

```javascript
require(["js/sidebar", "js/profile", "js/home"], function(sidebar, profile, home) {
	// ...
});
```

### The definitive approach: ES6 Modules

With the intention of **unifying module loading in a native way, ES6 ([ECMAScript 2015](https://www.ecma-international.org/ecma-262/6.0/)) brings us a new module system** that is gradually being implemented in all major browsers. It is based on the `import` and `export` keywords, and it is intented to be used in any kind of JavaScript written application.

In order to ensure that these features can be used, it is recommended to use [BabelJS](https://babeljs.io/). Babel compiles our code with ES5 into plain JavaScript that is widely accepted in all browsers. With this tool in hand, we can finally write our module like this:

```javascript
var bakingItems = 0;
function bake(message) {
    console.log(message);
    bakingItems++;
    console.log(`The oven is baking ${bakingItems} things.`);
    return message;
}
export default {
    bakeCake: function() {
        return bake("I'm baking a cake!");            
    },
    bakeMuffins: function() {
        return bake("I'm baking muffins!");            
    },
    bakePie: function() {
        return bake("I'm baking a pie!");            
    }
};
```

Note that in this example we are also using the `default` keyword, which is can be used or omitted to specify the granularity of the imports. Lastly, we can import all kinds of modules with the same unified syntax:

```javascript
import bakery from './bakery-es6';

bakery.bakeCake();    // I'm baking a cake!    The oven is baking 1 things.
bakery.bakeMuffins(); // I'm baking muffins!   The oven is baking 2 things.
bakery.bakePie();     // I'm baking a pie!     The oven is baking 3 things.
```