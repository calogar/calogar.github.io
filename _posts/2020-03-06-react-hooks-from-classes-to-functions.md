---
title: "React Hooks: From classes to functions"
date: 2020-03-06 10:32:00 +0800
categories: [JavaScript, React]
tags: [Components, Hooks, React Router]
toc: false
---

Traditionally, there are to possible ways of creating a component in React. If the component was simple enough, it could be represented as a function that returns the JXS content that must be rendered to the user. But function components had limited capabilities, as they could not use basic features like state management.

But, since React 16.8, **it is possible to write function components with the same capabilities as class components**. This is due to the power of **Hooks**, a new backwards-compatible primitive that is both simple and powerful.

## What are Hooks?

Hooks are functions which start with the 'use' word, like `useState` and `useEffect`. They are only available for **function components**, and provide some benefits against the classic, class-based components.

```jsx
import React, { useState } from 'react';

export function ShowRandom() {

    const [randomNumber, setRandomNumber] = useState(null);

    return <div>
        <h3>Your random number is {randomNumber}</h3>
        <button onClick={() => setRandomNumber(Math.random())}>Get random</button>
    </div>
}
```

This is an example of a function component that uses Hooks to define its own state. In a class-based component, we would read the state from `this.state.randomNumber` and set it with `this.setState({ randomNumber: Math.random() })`.

But with Hooks we can update `randomNumber` by directly calling the `setRandomNumber` function. When the function is invoked again, **the hook will 'remember' the state value, and the `randomNumber` variable will have the correct value**.

## Hooks can reduce complexity

It is common for a component to have dependencies, like Redux or React Router. We usually need a mechanism to receive the props we need (a dispatch action or information about the current url) in a way that keeps both complexity and coupling low.

Patterns like Higher Order Component (HOC) and Render Props can help with this task, but they require us to change the structure or hierarchy of our code. Compared to them, **Hooks are a much more direct way of injecting functionality into our components**, as they are only functions that can be called without the use of props.

An interesting example of this is [React Router](https://reacttraining.com/react-router/), because it uses traditional patterns and it also supports Hooks.

A very common use case of this module is to receive the params of a navigated route. This can be achieved with a **Render Prop** like this.

```jsx
import React from 'react';
import { Switch, Route } from "react-router-dom"
import Shop from "./Shop";

export function AppRouter() {
    return <Switch>
        <Route path="/shop/:item"
            render={router => <Shop item={router.match.params.item} /> }
        />
    </Switch>
```

In this simple example, we are defining a `Switch`, or a route container, with a single `Route` that will navigate to our `Shop` component when the route matches `/shop/:item`.

To make route information available inside the `Shop` component, React Router uses a Render Prop to specify what should be rendered when the route is matched. This allows us to inject all the props we need inside the `Shop` component, although it could be cleaner if we could just specify the component to navigate to.

```jsx
    // ...
    return <Switch>
        <Route path="/shop">
            <Shop />
        </Route>
    </Switch>
```

Luckily, React Router now provides the `useParams` Hook. With this Hook, we can access the `item` param inside the `Shop` **without the necessity of injecting it via props**.

```jsx
import React from 'react';
import { useParams } from "react-router-dom";

export function Shop() {
    let { item } = useParams();
    return <div>
        <h3>Item: {item}</h3>
    </div>
```

## Hooks can help code organisation

When using class-based components, it is mandatory to use lifecycle methods to perform the correct actions at the correct time. It is common to perform  **side effects**, or operations that run outside the scope of the function like HTTP calls, in lifecycle method like `componentDidMount` or `componentDidUpdate`.

The following example is a basic class-based component that fetches products and permissions from the backend. The products could be displayed to the user, and the permissions could specify what actions can be performed on these products.

```jsx
import React, { Component } from "react";

export class ProductList extends Component {

    constructor(props) {
        super(props);
        this.state = {
            permissions: [],
            products: []
        };
    }

    componentDidMount() {
        this.props.loadPermissions().then(permissions => {
            this.setState({ permissions: permissions });
        });
        this.props.loadProducts().then(products => {
            this.setState({ products: products });
        });
    }

    // ...
}
```

The important part of this example is that we are performing two different tasks in the same component: managing products and permissions. As the HTTP calls need to be done inside `componentDidMount`, we code is organised as lifecycle methods dictate.

We only have two side effects here (loading products and permissions), but if we had ten or more, the component would start being difficult to read. But now, **we can use the `sideEffect` Hook to perform side effects inside function components without the need for lifecycle methods**.

We could rewrite the previous component with Hooks like this:

```jsx
export function ProductListHooks(props) {

    // Managing permissions
    const [permissions, setPermissions] = useState([]);
    useEffect(() => {
        props.loadPermissions().then(data => setPermissions(data));
    }, []);

    // Managing products
    const [products, setProducts] = useState([]);
    useEffect(() => {
        props.loadProducts().then(data => setProducts(data));
    }, []);
   
    // ...
}
```

Placing the side effects inside the `effectHook` is similar to placing it inside `componentDidMount` and `componentDidUpdate`. And it also makes our code more organised, as we can now group the state definitions with their respective effects.

There is one last important thing to one in this example. We said that the `effectHook` is equivalent to `componentDidMount` and `componentDidUpdate`, but in our example component we only need to fetch data when the component has mounted. That is why **we added an empty array as the second parameter of the useEffect, so the effect is only executed the first time the function component is invoked**.

To learn more about this and about React Hooks in general, there is no better place than the [official docs](https://reactjs.org/docs/hooks-intro.html). I personally think they are a great addition to an already great library. 