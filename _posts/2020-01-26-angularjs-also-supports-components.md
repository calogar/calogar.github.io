---
title: "My honest opinion: AngularJS"
date: 2020-01-26 13:47:00 +0800
categories: [JavaScript, AngularJS, Angular, Opinion]
tags: [Components, AngularJS, Angular]
toc: false
---

Since it was released in 2010, AngularJS, which should not be confused with Angular or Angular 2+, started to rivalize with jQuery in the implementation of Web Applications. Unlike jQuery, it is based in the Model-View-Whatever pattern, in which "Whatever" can mean "Controller", "ViewModel" or any compatible concept.

Classic AngularJS consists on defining an HTML view, attaching a Controller to it and defining wich data will be flowing from one to the other. This, plus some useful features like double data binding or dependency injection made it suitable for both simple and complex applications.

But six years later, in 2016, Angular was released in order to adopt a completely different approach. Why did this happen?

## An abrupt change of concepts

AngularJS is a good JS library that has given a lot of good ideas to the community, but in my opinion, **it has some lack of code encapsulation and, as a result, it is harder to reuse**. It is interesting to be able to attach more that one controller to the same view, but it also makes the code less decoupled.

As it is hard to separate concerns, it can also lead to **harder-to-read code**. One example of this is when you try to access the scope of the parent controller by using `$scope.$parent`. This can get hard to maintain when we start abusing this feature by accessing properties three, four or more levels above. Furthermore, if the suddently want to eliminate one of the levels in our controller architecture the developer must be very careful to update all the `$parent` references accordingly.

For me, I think **components are the natural evolution** of this concepts. They are more easy to read once you change your way of thinking, because **they are closer to how the DOM is actually implemented**.

## Components in AngularJS

Interesting enough, since AngularJS 1.5 you can actually define components just like Angular or React do. Take the following example:

```javascript
function DynamicTableCtrl() {    
    var ctrl = this; 
    ctrl.fireEdit = function (i) { this.onAction({ item: i, action: 'edit' }) };
}

App.component('dynamicTable', {
    templateUrl: './dynamic-table.html',
    controller: [ DynamicTableCtrl ],
    // "<": Input params (one way data binding).
    // "&": Output params (callbacks).
    bindings: {
        items: '<',
        onAction: '&',
    }
});
```

Although the syntax is quite different from Angular, all the concepts are equivalent. Here is an example about how the same component could be implemented in Angular.

```javascript
@Component({
    selector   : 'dynamicTable',
    templateUrl: './dynamic-table.component.html'
})
export class DynamicTableComponent implements OnInit, OnDestroy
{
    @Input() items: any;
    @Output() onAction: EventEmitter;
}
```