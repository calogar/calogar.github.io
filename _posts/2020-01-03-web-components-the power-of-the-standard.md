---
title: "Web Components: The power of the standard"
date: 2020-01-03 17:21:00 +0800
categories: [JavaScript, "Web Components"]
tags: ["Web Components", "Components", ReactJS, Angular]
toc: false
---

Recently I had the privilege of assisting to one of Abdón Rodriguez ([@abdonrd](https://twitter.com/abdonrd)) talks, which was about the state of the art of Web Components. If you have used Angular 2, React, Vue or any other component-based framework, you should already have a notion about how component arquitectures work - if not, here is a brief summary.

## What is a Component?

Components is all about **replicating the structure of the DOM by defining new types of elements**. If you open Youtube and inspect the website, you can find code like the following:

```html
<ytm-app id="app" class="sticky-player">
	<ytm-mobile-topbar-renderer id="header-bar" class="sticky-player">...</ytm-mobile-topbar-renderer>
	<ytm-pivot-bar-renderer role="tablist" class></ytm-pivot-bar-renderer>
	<div class="page-container">
		<ytm-browse>
			<ytm-single-column-browse-results-renderer>
			</ytm-single-column-browse-results-renderer>
		</ytm-browse>
	</div>
	<!-- ... -->
</ytm-app>
```

¿Is this HTML? Of course it is, but the element ytm-app is certainly not a classic HTML tag, but **a new component**, which encapsulates its own behavior and can be attached to any part of the DOM.

**Components are clean, they abstract their inner logic and can be reutilized and composed easily.** This has been a tendency in web development for quite a long time, to the point that frameworks like AngularJS, which initially worked with different concepts, now also includes support for defining components.

Broadly speaking, defining a component means:

* Creating an HTML (or HTMl-like) template.
* Attaching the template to a class, an object or a function which provides the behavior.
* Optionally declaring styling that will be scoped to this specific component.
* Linking everything to a tag name, like `<app-player>`.

This is true for all major frameworks, but that said, it is also true that all frameworks differ in their implementations. Angular uses Observables and predefined directives to render HTML, React has the concept of "props" and "state" and prefers JSX over classic HTML... all those things **make impossible to take a component defined in one framework and just use it in another**.

But we would certainly want to do that. **And that is why Web Components exist.**

## The power of Web Components

[**Web Components are a standard**](https://www.webcomponents.org/introduction). They take the common concepts of the frameworks and create a way of defining components which can be accepted by all frameworks because it is native to the browser.

This is a powerful concept. You can literally create a Web Component, paste it in any up-to-date browser and it will just work. Of course there will be some older versions that will not have support for this feature yet, but that can be solved by importing the correct polyfill. It is also important to note that, as of 2020, **Web Components are broadly supported in all major browsers** and polyfills are getting more and more unnecessary.

## Then, are Web Frameworks useless?

Of course not. In fact, **it is the combination of Web Frameworks and Web Components what shines the most**. This is because Web Components are created to solve a very specific task: they define a generic component architecture, but nothing else. What about routing, lazy loading, module bundling and many other things that Frameworks already do for us? We can (and should) use them. We can create an Angular app with Angular components or with Web Components. But if we want to define our components in a way that is understandable for every possible JavaScript Web Application, then Web Components is the way to go.

