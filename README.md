# Directives-in-Angular-2.0
# A sneak peek in to Directives in Angular 2.0

## Disclaimer
* Angular 2.0 is still in alpha state (as of 26-May-2015) and is evolving at a very fast rate with breaking changes between most of the releases. So it is not suitable for production application and I would recommend Angular 1.x for such applications. 
* Also I am not an expert in Angular 2.0 and I am not a part of Angular team. I am just another guy trying to learn and trying to share my learnings. 
* The examples in the below article are written using Angular 2.0.0-alpha.25 and I will try to keep the below code samples up to date if possible. 

## Angular 2.0
"Angular is a development platform for building mobile and desktop web applications. Angular includes a wealth of essential features such as mobile gestures, animations, filtering, routing, data binding, security, internationalization, and beautiful UI components. It's extremely modular, lightweight, and easy to learn." - angular.io

If you want to know more about Angular 2.0, visit the official http://angular.io and for learning resources refer [this](https://github.com/timjacobi/angular2-education) which is a collection of links to various learning resources by @timjacobi.

## Introduction to Directives in Angular 2.0
There are lot of tutorials on the Internet about creating **Components** (`Components` are basically a sub class of `Directives` and in other words Components are Directives with Views) in Angular 2, however there are very less tutorials or explanations about **Directives** in Angular 2 and the below is my attempt to fill the void. Also most or all of the things discussed here will also applicable for `Components` and also I will use `Components` in my examples frequently, because `Components` are the basic building blocks of the Angular 2.0 applications. My article below is heavily influenced by the API docs in angular.io site. 

And as this article is focused on Directives, I assume you have basic understanding of the Angular 2.0 and its glossary of terms like, Components, Annotations(Decorators), DI, Change Detection etc.

### What is a Directive?
<!-- A directive can be an existing DOM element or an attribute or an custom angular element, whose default behavior is altered. -->
Directives allows you to attach behavior to DOM elements. It is that simple! I will try to explain the various properties of Directives using some examples. Directives are defined using @Directive decorator in ES6, Typescript and with a controller class. The controller class defines the behavior of a directive.

#### Example 1 (selector)

Let us have a look at a simple example to understand this better. Let us create an simple directive called 'red', which attached to any DOM element as an attribute, will change the color of its contents to red. Here is the code to create the directive.

```javascript
@Directive({
  selector: '[red]',
})

class RedDec {
  constructor(el: ElementRef) {
    el.domElement.style.color = 'red';
  }
}
```

A directive consists of a single directive annotation and a controller class. `@Directive` is the annotation used to define a directive in Angular 2.  When the directive's `selector` matches elements in the DOM, angular instantiates directives for each matched element. In the controller class `RedDec`, we define the behavior of the directive. Here we are using the elements `style.color` property to set the color to red.
Now in your HTML code you can use the `red` directive like below and the `Hello World` text will be rendered in red color.

```html
<h1 red> Hello World</h1>
```

Here you may have multiple questions like why the `selector` is using `[]` and what the heck is this `el:ElementRef`? Let us go one by one. First `selector` uses CSS selector approach to match the elements in the DOM.


>From angular.io docs page

>Angular only allows directives to trigger on CSS selectors that do not cross element boundaries.

>`selector` may be declared as one of the following:

>```javascript
>element-name: select by element name.
>.class: select by class name.
>[attribute]: select by attribute name.
>[attribute=value]: select by attribute name and value.
>:not(sub_selector): select only if the element does not match the sub_selector.
>selector1, selector2: select if either selector1 or selector2 matches.
>```

Now I hope you would have got the answer to the first question. As our directive is basically an attribute, we have used `[red]` for the `selector`. We can also combine some of the above to further refine the selection matching, for example we can use `input[type=text]` to select only the input elements of type text.

I will try to answer the second question at a later stage, as we have to dwell little bit deep in to dependency injection. But for now we are trying to get an reference to the host DOM element by dependency injection.

TODO: Update this example to alpha.26
> [Here](http://plnkr.co/edit/dFShDzOAU3TdLQbKYcbc?p=preview) is the plnkr link to the full working code for the above example.

Alright, now we have a basic understanding of the Angular 2.0 Directives and we go a bit further and try to expand on the above example.

#### Example 2 (properties)

So in the previous example if we add the `red` attribute to any DOM element, the content of the element color is displayed in red. What if we want to add an `color` attribute to an element and change the elements color based on the value supplied to the attribute.

```javascript
@Directive({
  selector: '[color]',
  properties: ['colour : color']
})

class ColorDec {
  el: ElementRef;
  
  constructor(el: ElementRef) {
    this.el = el;
  }
  
  set colour(colour){
    this.el.domElement.style.color = colour;
  }
}
```

Here we have a similar `selector` which selects the elements with `color` attribute on them, additionally we have a `properties` property which defines a set of directiveProperty to bindingProperty key-value pairs:

* directiveProperty specifies the component property where the value is written.
* bindingProperty specifies the DOM property where the value is read from.

In our example above `colour` (not `color`) is the directive property which binds to and reads its value from `color` bindings property on the DOM element. Whenever the `color` attribute value changes  in the DOM, it triggers the `set colour` method on the directive and the `colour` property is set to the new value.

Now we can use the above directive in HTML like,

```html
<h1 color="blue"> Hello World</h1>
```

and this will change the color of this h1 contents to the assigned color, here it is blue.

Angular core team simplified the syntax for the properties binding. Now `properties` is not an object('name' : 'value' pair), it is converted into an array(List) of string. Refer this [commit](https://github.com/angular/angular/commit/d7df853bde30ffe97045eff649240284ae6ffdf8).  If the directive property and binding property are of same name, then no need to type them twice, just type them once and angular knows both directive property and binding property are of same name, use it like this:

```javascript
....
properties: ['color', 'name']
```

if you want to bind them with different names specify it like this

```javascript
....
properties: ['text: tooltip']
```

> [Here](http://plnkr.co/edit/kNuXAQEPie1HNXQeGrZe?p=preview) is the plnkr link to the full working code for the above example.

#### Example 3 (events and hostListeners)
In the previous example we have created a custom directive with a color property to which if you assign a value to the color attribute for a DOM element, it will change its DOM element color to the assigned value (if the assigned value is a valid color). What if we want to assign a color dynamically to an DOM element. Lets have look at the below example:

```javascript
@Directive({
  selector: 'input[color]',
  events: ['colorChange'],
  hostListeners: {
    'input': 'updateColor($event)'
  }
})
export class ColorDec{
  colorChange: EventEmitter;
  constructor(){
   this.colorChange = new EventEmitter();
  }
  updateColor($event) {
    $event.preventDefault();
    $event.stopPropagation();
    var color = $event.target.value;
    this.colorChange.next({$event, color});
  }
}
```

Basically we are selecting any input element which is having color attribute present in them using the `selector: 'input[color]'` property. Next we are defining a new custom event, the directive can emit with `events: ['colorChange']` property and then we are setting up a host listener with ` hostListeners: {'input': 'updateColor($event)'}` that will react to an `input` event on the element and execute the provided method `updateColor($event)` which is defined in the controller class.

>From angular.io docs page

>The hostListeners property defines a set of event to method key-value pairs:

>event1: the DOM event that the directive listens to.
>statement: the statement to execute when the event occurs. If the evaluation of the statement returns false, then preventDefaultis applied >on the DOM event.
>To listen to global events, a target must be added to the event name. The target can be window, document or body.

>When writing a directive event binding, you can also refer to the following local variables:

>$event: Current event object which triggered the event.
>$target: The source of the event. This will be either a DOM element or an Angular directive. (will be implemented in later release)

>Syntax:

>```javascript
>@Directive({
>  hostListeners: {
>    'event1': 'onMethod1(arguments)',
>    'target:event2': 'onMethod2(arguments)',
>    ...
>  }
>}
>```

Let's have a closer look at how the custom event `colorChange` is emitted by the directive. First we are declaring the event `colorChange` as a type of `EventEmitter`. `EventEmitter` is nothing but an sub class of Observable. Then we are instantiating a new `EventEmitter` and assigning it to the variable `colorChange` in the constructor function. Whenever an input event is fired by the input element, the `updateColor` method is invoked and this method has a line `this.colorChange.next({$event, color});`, this is where the custom event is fired with `$event` and `color` value. Now we can bind to this event like a native DOM event and execute some methods like this:

```javascript
@Component({
  selector: 'color-output'
})
@View({
  template: `<p>Color entered is {{color}}</p>`,
    directives: [ColorDec]
})
export class ColorOutput {
  color: string;
  el: ElementRef;
  constructor(el:ElementRef) {
    this.color = '';
    this.el=el;
  }
  getColor({$event, color}){
    this.color = color;
    this.el.domElement.style.color = this.color;
  }
}

@Component({
  selector: 'app'
})
@View({
  template: `
    <input color (color-change)="output.getColor($event)">
    <color-output #output></color-output>
    `,
  directives: [ColorDec, ColorOutput]
    
})
export class App{
  constructor(){
    
  }
  
}
```

This is very important and powerful, because this is one of the ways we can communicate between Directives and Component or between one Component and another Component in Angular 2. 

**Important** - One important thing to note here is that, we have defined the custom event with the name `colorChange` (Notice the came case), however because HTML is case insensitive, Angular will normalize the event name to `color-change`. So in HTML we have to bind the event with `(color-change)="doSomething()"`. (Thanks to @choeller).

**Note** - One more thing to note in the above code is that, if you want to use any custom directives or components in another component, we need to specify them in the `directives` property on the View annotation.

[Here](http://plnkr.co/edit/XXLKhcPPMrs1fOVawQrC?p=preview) is the plnkr link to the full working code for the above example.

#### Example 4 (hostProperties)
Let us deviate from our colors example and have a look at different simple example which uses `hostProperties` property of the Directive.
Sometimes there may be a need to trim white spaces (both before and after some text) the user enters in a text box. This can be established by the following code.

```javascript
@Directive({
  selector: 'input[trimmed]',
  hostListeners: {'input': 'onChange($event)'},
  hostProperties: {'value': 'value'}
})

export class TrimmedInput{
  value: string;
  constructor(){
    this.value='';
  }
  onChange($event){
    this.value = $event.target.value.trim();
    console.log(this.value);
  }
}
```

In the above code we have created a directive with `selector: input[trimmed]`, so it will match any input element with `trimmed` attribute attached to it. We have also attached a `hostListener` to the directive, which will execute `onChange()` method every time `input` event triggered on the host element. So the important part is `hostProperties`, which contains a name: value pair. It basically binds the directive property to the DOM property. Here it is  ` 'value' : 'value' `, this means that whenever the `value` property of the Directive changes, the `value` property on the DOM element gets updated. This is exactly opposite to `properties` property in the directive.
Here is the HTML portion of the above

```html
 <input trimmed type="text">
```
[Here](http://plnkr.co/edit/3HqEy27v7E0ut7kSF4Nt?p=preview) is the plnkr link to the full working code for the above example.

#### Example 5 (lifeCycle)
There are 5 lifecycle methods available for the components and directives namely onChange, onInit, OnCheck, onAllChangesDone and onDestroy. For a complete explanation about these methods have a look at the docs page [here](https://angular.io/docs/js/latest/api/annotations/onChange-const.html). What I felt missing in the docs page was a complete example showing, at what stages these methods will get called. Here is one fictious example I put together to reason about lifecycle methods.
Let us take an example of parent and child component to better understand the order of invocation.

```javascript
// Parent Component - has parent property bound to value of the input
@Component({
  selector: 'parent-comp',
  properties: ['parent'],
  lifecycle: [onCheck, onInit, onChange, onAllChangesDone]
})
@View({
  template: `
    I am parent component
    <content></content>
    `
})
export class ParentComp{
  // parent: string;
  constructor(){
    console.log('Parent');
  }
// Notify a directive when it has been checked the first itme.
// This method is called right after the directive's bindings have been checked, and before any of its children's bindings have been checked.
// It is invoked only once.
  onInit(){
    console.log('I am from Parent Component\'s onInit method');
  }
//Notify a directive when it has been checked.
// This method is called right after the directive's bindings have been checked, and before any of its children's bindings have been checked.
// It is invoked every time even when none of the directive's bindings has changed.
  onCheck(){
    console.log('I am from Parent Component\'s onCheck method');
  }
// Notify a directive when any of its bindings have changed.
// This method is called right after the directive's bindings have been checked, and before any of its children's bindings have been checked.
// It is invoked only if at least one of the directive's bindings has changed.
  onChange(changes){
    console.log('I am from Parent Component\'s onChange method');
    console.log(changes);
  }
  onAllChangesDone(){
    console.log('I am from Parent Component\'s onAllChangesDone method');
  }
}
// Child Component
@Component({
  selector: 'child-comp',
  properties: ['child'],
  lifecycle: [onCheck, onInit, onChange, onAllChangesDone]
})
@View({
  template: `I am child component`
})
export class ChildComp{
  constructor(){
    console.log('Child');
  }
  onInit(){
    console.log('I am from Child Component\'s onInit method');
  }
  onCheck(){
    console.log('I am from Child Component\'s onCheck method');
  }
  onChange(changes){
    console.log('I am from Child Component\'s onChange method');
    console.log(changes);
  }
  onAllChangesDone(){
    console.log('I am from Child Component\'s onAllChangesDone method');
  }
}
```

What we have here is one parent component with a `parent` property and one child component having `child` property. The template for the parent component is having `I am parent component` text and `<content></content>` tag to pull the contents from its child. It has 4 of the `lifecycle` methods attached to it, each one logging to the console. The child component's template has `I am child component` text. It also has 4 of the `lifecycle` methods attached to it, each one logging to the console. Here is the `app` component

```javascript
@Component({
  selector: 'app'
})
@View({
  template: `
    <input type="text" #parent placeholder="" (keyup)><br>
    <parent-comp [parent]="parent.value"><br>
      <child-comp child></child-comp>
    </parent-comp>
  `,
  directives: [ParentComp, ChildComp]
})
class App {
  constructor() {
    console.log('app');
  }
}
```

This is the root component and it uses an input element and the parent component with nested child component. Here we have bound the value of the input element to the `parent` property on the parent component. If you run the app now, we can see a series of messages getting logged in to the console.

```
1. I am from Parent Component's onChange method
2. I am from Parent Component's onInit method
3. I am from Parent Component's onCheck method

4. I am from Child Component's onChange method
   I am from Child Component's onInit method
   I am from Child Component's onCheck method
5. I am from Child Component's onAllChangesDone method

6. I am from Parent Component's onAllChangesDone method
...
```
1. Parent component `onChange` method is invoked. It is called after the parent component have been instantiated and this is used to render the initial state
2. Parent component `onInit` method is invoked. It is invoked only once after the bindings have been checked and before any of its childrens bindings have been checked
3. Parent component `onCheck` method is invoked. This method is invoked every time even when none of the component's bindings has changed and before any of its childrens bindings have been checked
4. Now its time for child component, the same sequence as of parent component is repeated
5. Child component's `onAllChangesDone` method is invoked. This will be invoked when the bindings of all its children have been changed. Because child component does  not have any grand child this is invoked in this stage
6. Parent component's `onAllChangesDone` method is invoked. This is invoked because all of its children's bindings have been changed

The above steps are lifecycle methods invocation during instantiation, however if you want to understand the sequence of lifecycle methods invocation after  change to the property, have a look at the below plunker.

[Here](http://plnkr.co/edit/sk6Xi7L6a7ob9xiRJaSJ?p=preview) is the plnkr link to the full working code for the above example.

TODO:

1. Create seperate folders for each examples with ES6, Typescript and possibly ES5 codes
2. Explain about ElementInjector (angular.io docs page has good example)?
3. Explain a little bit about Component and bootstrap
4. Make this tutorial generic to both Component and Directive 
5. Change the onChange method name in Example 4 as it may collide with onChange lifecycle method
6. Provide a complete real world (atleast a sample Todo) app to utilize most of the things discussed here