# 初识 Angular 之（三）模板语法 

Angular 应用通过  组件类的实例 （组件）及其 模板 来管理用户所见的视图和用户的行为。

基于你的 MVC 或 MVVM 开发经验， 你也许很熟悉 组件 和 模板 这两个概念。在 Angular 中， 组件充当 控制器或者视图模型，二模板则展示视图。

本章是关于 Angular 模板语法 的详尽的技术参考手册。本章将讲解模板语法的基本原则，并且将覆盖整个文档中出现的大部分语法。

Many code snippets illustrate the points and concepts, all of them available in the [Template Syntax Live Code](https://angular.io/generated/live-examples/template-syntax/eplnkr.html) / [download example](https://angular.io/generated/zips/template-syntax/template-syntax.zip) .

## 1.HTML in templates

HTML is the language of the Angular template. Almost all HTML syntax is valid template syntax. The `` element is a notable exception; it is forbidden, eliminating the risk of script injection attacks. In practice, `` is ignored and a warning appears in the browser console. See the [Security](https://angular.io/guide/security) page for details.

Some legal HTML doesn't make much sense in a template. The ``, ``, and `` elements have no useful role. Pretty much everything else is fair game.

You can extend the HTML vocabulary of your templates with components and directives that appear as new elements and attributes. In the following sections, you'll learn how to get and set DOM (Document Object Model) values dynamically through data binding.

Begin with the first form of data binding—interpolation—to see how much richer template HTML can be. 

------

## 2.Interpolation ( {﻿{...}} )

You met the double-curly braces of interpolation, `{{` and `}}`, early in your Angular education.

src/app/app.component.html

```
content_copy<p>My current hero is {{currentHero.name}}</p>
```

You use interpolation to weave calculated strings into the text between HTML element tags and within attribute assignments.

src/app/app.component.html

```
content_copy<h3>
  {{title}}
  <img src="{{heroImageUrl}}" style="height:30px">
</h3>
```

The text between the braces is often the name of a component property. Angular replaces that name with the string value of the corresponding component property. In the example above, Angular evaluates the `title` and `heroImageUrl` properties and "fills in the blanks", first displaying a bold application title and then a heroic image.

More generally, the text between the braces is a **template expression** that Angular first **evaluates** and then **converts to a string**. The following interpolation illustrates the point by adding the two numbers:

src/app/app.component.html

```
content_copy<!-- "The sum of 1 + 1 is 2" -->
<p>The sum of 1 + 1 is {{1 + 1}}</p>
```

The expression can invoke methods of the host component such as `getVal()`, seen here:

src/app/app.component.html

```
content_copy<!-- "The sum of 1 + 1 is not 4" -->
<p>The sum of 1 + 1 is not {{1 + 1 + getVal()}}</p>
```

Angular evaluates all expressions in double curly braces, converts the expression results to strings, and links them with neighboring literal strings. Finally, it assigns this composite interpolated result to an **element or directive property**.

You appear to be inserting the result between element tags and assigning it to attributes. It's convenient to think so, and you rarely suffer for this mistake. Though this is not exactly true. Interpolation is a special syntax that Angular converts into a [property binding](https://angular.io/guide/template-syntax#property-binding), as is explained [below](https://angular.io/guide/template-syntax#property-binding-or-interpolation).

But first, let's take a closer look at template expressions and statements.

------

## 3.Template expressions

A template **expression** produces a value. Angular executes the expression and assigns it to a property of a binding target; the target might be an HTML element, a component, or a directive.

The interpolation braces in `{{1 + 1}}` surround the template expression `1 + 1`. In the [property binding](https://angular.io/guide/template-syntax#property-binding) section below, a template expression appears in quotes to the right of the `=`symbol as in `[property]="expression"`.

You write these template expressions in a language that looks like JavaScript. Many JavaScript expressions are legal template expressions, but not all.

JavaScript expressions that have or promote side effects are prohibited, including:

- assignments (`=`, `+=`, `-=`, ...)
- `new`
- chaining expressions with `;` or `,`
- increment and decrement operators (`++` and `--`)

Other notable differences from JavaScript syntax include:

- no support for the bitwise operators `|` and `&`
- new [template expression operators](https://angular.io/guide/template-syntax#expression-operators), such as `|`, `?.` and `!`.

### 3.1Expression context

The *expression context* is typically the *component* instance. In the following snippets, the `title` within double-curly braces and the `isUnchanged` in quotes refer to properties of the `AppComponent`.

src/app/app.component.html

```
content_copy{{title}}
<span [hidden]="isUnchanged">changed</span>
```

An expression may also refer to properties of the *template's* context such as a [template input variable](https://angular.io/guide/template-syntax#template-input-variable) (`let hero`) or a [template reference variable](https://angular.io/guide/template-syntax#ref-vars) (`#heroInput`).

src/app/app.component.html

```
content_copy<div *ngFor="let hero of heroes">{{hero.name}}</div>
<input #heroInput> {{heroInput.value}}
```

The context for terms in an expression is a blend of the *template variables*, the directive's *context* object (if it has one), and the component's *members*. If you reference a name that belongs to more than one of these namespaces, the template variable name takes precedence, followed by a name in the directive's *context*, and, lastly, the component's member names.

The previous example presents such a name collision. The component has a `hero` property and the `*ngFor` defines a `hero` template variable. The `hero` in `{{hero.name}}` refers to the template input variable, not the component's property.

Template expressions cannot refer to anything in the global namespace. They can't refer to `window` or `document`. They can't call `console.log` or `Math.max`. They are restricted to referencing members of the expression context.

### 3.2Expression guidelines

Template expressions can make or break an application. Please follow these guidelines:

- [No visible side effects](https://angular.io/guide/template-syntax#no-visible-side-effects)
- [Quick execution](https://angular.io/guide/template-syntax#quick-execution)
- [Simplicity](https://angular.io/guide/template-syntax#simplicity)
- [Idempotence](https://angular.io/guide/template-syntax#idempotence)

The only exceptions to these guidelines should be in specific circumstances that you thoroughly understand.

#### 3.2.1 No visible side effects

A template expression should not change any application state other than the value of the target property.

This rule is essential to Angular's "unidirectional data flow" policy. You should never worry that reading a component value might change some other displayed value. The view should be stable throughout a single rendering pass.

#### 3.2.2 Quick execution

Angular executes template expressions after every change detection cycle. Change detection cycles are triggered by many asynchronous activities such as promise resolutions, http results, timer events, keypresses and mouse moves.

Expressions should finish quickly or the user experience may drag, especially on slower devices. Consider caching values when their computation is expensive.

#### 3.2.3 Simplicity

Although it's possible to write quite complex template expressions, you should avoid them.

A property name or method call should be the norm. An occasional Boolean negation (`!`) is OK. Otherwise, confine application and business logic to the component itself, where it will be easier to develop and test.

#### 3.2.4 Idempotence

An [idempotent](https://en.wikipedia.org/wiki/Idempotence) expression is ideal because it is free of side effects and improves Angular's change detection performance.

In Angular terms, an idempotent expression always returns *exactly the same thing* until one of its dependent values changes.

Dependent values should not change during a single turn of the event loop. If an idempotent expression returns a string or a number, it returns the same string or number when called twice in a row. If the expression returns an object (including an `array`), it returns the same object *reference* when called twice in a row. 

------

## 4.Template statements

A template **statement** responds to an **event** raised by a binding target such as an element, component, or directive. You'll see template statements in the [event binding](https://angular.io/guide/template-syntax#event-binding) section, appearing in quotes to the right of the `=` symbol as in `(event)="statement"`.

src/app/app.component.html

```
content_copy<button (click)="deleteHero()">Delete hero</button>
```

A template statement *has a side effect*. That's the whole point of an event. It's how you update application state from user action.

Responding to events is the other side of Angular's "unidirectional data flow". You're free to change anything, anywhere, during this turn of the event loop.

Like template expressions, template *statements* use a language that looks like JavaScript. The template statement parser differs from the template expression parser and specifically supports both basic assignment (`=`) and chaining expressions (with `;` or `,`).

However, certain JavaScript syntax is not allowed:

- `new`
- increment and decrement operators, `++` and `--`
- operator assignment, such as `+=` and `-=`
- the bitwise operators `|` and `&`
- the [template expression operators](https://angular.io/guide/template-syntax#expression-operators)

### 4.1 Statement context

As with expressions, statements can refer only to what's in the statement context such as an event handling method of the component instance.

The *statement context* is typically the component instance. The *deleteHero* in `(click)="deleteHero()"` is a method of the data-bound component.

src/app/app.component.html

```
content_copy<button (click)="deleteHero()">Delete hero</button>
```

The statement context may also refer to properties of the template's own context. In the following examples, the template `$event` object, a [template input variable](https://angular.io/guide/template-syntax#template-input-variable) (`let hero`), and a [template reference variable](https://angular.io/guide/template-syntax#ref-vars) (`#heroForm`) are passed to an event handling method of the component.

src/app/app.component.html

```
content_copy<button (click)="onSave($event)">Save</button>
<button *ngFor="let hero of heroes" (click)="deleteHero(hero)">{{hero.name}}</button>
<form #heroForm (ngSubmit)="onSubmit(heroForm)"> ... </form>
```

Template context names take precedence over component context names. In `deleteHero(hero)` above, the `hero` is the template input variable, not the component's `hero` property.

Template statements cannot refer to anything in the global namespace. They can't refer to `window` or `document`. They can't call `console.log` or `Math.max`.

### 4.2 Statement guidelines

As with expressions, avoid writing complex template statements. A method call or simple property assignment should be the norm.

Now that you have a feel for template expressions and statements, you're ready to learn about the varieties of data binding syntax beyond interpolation.