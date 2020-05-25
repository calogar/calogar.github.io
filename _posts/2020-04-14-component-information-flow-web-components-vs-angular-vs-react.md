---
title: "Component data flow: Web Components vs Angular vs React"
date: 2020-04-14 17:03:00 +0800
categories: [JavaScript, Web Components, Angular, React]
tags: [Components, Properties, Angular, Web Components, LitElement, React]
toc: false
---

All libraries, frameworks and standards that implement Component-based architectures need a way to effectively provide and retrieve data from a component. This is a key aspect to provide encapsulation: a component will only know about the data it requires to perform, and it will only output the results that are relevant to the rest of the application.

That being said, the Web Component standard, the Angular framework and the React library all implement this concept differently. This post is intended to give a general overview of the three implementations in order to compare them.

## Web Components

Web Components is a new web standard that allows us to develop components directly into the browser. In this example I will consider **LitElement**, the last Web Component implementation by the Polymer team, which efficiently defines the flow of data in components.

#### Attributes and Properties

All classic HTML elements have attributes, like the `href` in an anchor element, that provides the information that the element needs to work. As components are conceptually HTML elements defined by the developer, Web Components must implement attributes **just as a classic element does**.

```html
<user-detail name="John" surname="Doe" details="{'id': 2}"></user-detail>
```

To produce this result, LitElement takes a similar approach to React. **lit-html**, its templating system, allows inserting the values at the moment the template is being created.

```javascript
class UserPage extends LitElement {
  // ...
  render() {
    let name = 'John';
    return html`<user-detail name=${name}></user-detail>`;
  };
}
```

Finally, to use this information inside the component, in LitElement we must define **properties** that will match the attributes.

```javascript
class UserDetail extends LitElement {
  static get properties() { return {
    name:    { type: String },
    surname: { type: String },
    details: { type: Object }
  };
  // ...
}
```

Or, if we use **Typescript**:

```typescript
class UserDetail extends LitElement {
  @property() name: String;
  @property() surname: String;
  @property() details: Object;
  // ...
}
```

Either way, LitElement allows the developer to configure this binding between the attributes and the properties. The attributes can be **observed** by the properties, which means that **the properties values will change when the attributes are modified**. In the same way, the attributes can be **reflected** from the properties, which means that **when we update a property, its corresponding attribute will also receive this change**.

#### Events and Custom Events

As LitElement implements a standard, it also attempts to integrate as much as possible with the rest of the WEB APIs. This means that data can also flow outside of the component by handling HTML events like `'click'` or by **defining our own custom events**.

Binding an event to a callback involves a special markup in the template, like `@click`. The native Javascript method `document.addEventListener` can also be used in this context.

```javascript
class MyElement extends LitElement {
  // ...
  render() {
    return html`<button @click="${this.handleClick}">Click me</button>`;
  }
}
```

But we can also define custom events with data payloads by creating a `CustomEvent`. With this, we can define more useful events that adapt to the component's domain, like `'user-edit'`, `'user-delete'`, etc.

```javascript
class UserDetail extends LitElement {
  // ...
  editUser(userData) {
    let event = new CustomEvent('user-edit', { detail: userData });
    this.dispatchEvent(event);
  }
}
```

We fire the custom event with `dispatchEvent`, and the beauty of Web Components allows us to **listen to it as if it was a native event**. We could use `document.addEventListener` or attach a callback to `@user-edit` just as we did with `@click`.

## Angular

Compared to other Frontend options, Angular is a full-fledged framework that includes Typescript by default.  Like Web Components, Angular uses **two different syntaxes** that are used to explicitly specify the way the data flows.

#### Attributes

Angular uses **brackets to specify input attributes, and parentheses for output attributes**.

```html
<app-user-detail [name]="currentName" (onEdit)="onEditCallback($event)"></app-user-detail>
```

To achieve double data binding, which existed by default in AngularJS, the two syntaxes can be combined like in the `ngModel` directive.

```html
<input type="text" [(ngModel)]="username" />
```

#### @Input and @Output

Inside the component class, Angular defines its properties explicitly, as Web Components do. For this, **the `@Input` and `@Output` decorators are used**.

```typescript
@Component({...})
export class UserDetailComponent {
  @Input() name: String;
  @Output() onEdit: EventEmitter<User>;
  // ...
}
```

We can see that Angular implements input properties very similarly to Web Components, but instead of creating and dispatching a `CustomEvent`, it uses an **`EventEmitter`** instead. However, the concept and usage is very similar.

```typescript
@Component({...})
export class UserDetailComponent {
  // ...
  editUser(userData: User) {
    this.onEdit.emit(userData);
  }
}
```

## React

**React unifies input and output properties as the concept of `props`.** It uses JSX instead of HTML to declare the view part, and the way it attaches its attributes is closer to Web Components than to Angular.

```javascript
class UserPage extends Component {
  // ...
  render() {
    let name = 'John';
    return <UserDetail name={name} />;
  );
}
```

#### Assigning props with JSX

JSX allows React to be extremely flexible when assigning `props` to a component. There is no need to interpolate strings like in LitElement, so **props can be massively assigned** to a component by using the ES6 spread operator.

In the following example, the `UserPage` component pushes all the props it has to its child, `UserDetail`.

```javascript
class UserPage extends Component {
  // ...
  render() {
    return <UserDetail { ...this.props } />;
  );
}
```

The main difference between this approach and the others is that **properties are not explicitly declared inside the component**: everything contained inside the `props` object is a valid property. This is useful for injecting dependencies inside components, but if desired, the accepted props and its types can be declared explicitly by using **propTypes**.

#### Using callbacks as props

There is no need to create a `CustomEvent` or an `EventEmitter` in React. As the the view is not built from templates or interpolated strings, **functions can be directly passed to child components**, just like we pass strings, objects or arrays.

Therefore, instead of emitting or dispatching events, a child component in React only needs to execute the callback that is has been provided to. An important thing to note is that these callback functions must be bound to the context of the parent component, so the parent state is the one that can be updated.

## To sum up

Reaching this point we can see that, although implementations differ, the three solutions are not that far from each other. In essence, **the three of them map the element attributes to the attributes of a Javascript class** (or to the parameters of a function, which can also be done in React). **They also attempt to somehow represent output as events, like the native ones, and assign them a callback**.

These similarities are what allow us, the developers, to see the bigger picture, understand the concepts behind each implementation, and finally adapt to the new Frontend solutions that will arrive in the future.