# 初识 Angular 之（二）数据的展现 

通过把 Angular 组件的属性绑定到 HTML 模板中的控件上，我们可以展示数据。

本小节中，我们将会创建一个英雄列表组件。我们将展示英雄名字的列表，并根据条件来决定是否在列表下方展示一条信息。

最终的 UI 看起来像这样：

![Final UI](https://angular.io/generated/images/guide/displaying-data/final.png)

[在线例子](https://angular.io/generated/live-examples/displaying-data/eplnkr.html) /  [下载示例代码](https://angular.io/generated/zips/displaying-data/displaying-data.zip) 演示了这一节所有的提到的代码片段和语法。



## 1.用插值表达式展示组件的属性

展示组件的属性最简单的方式是通过插值表达式来绑定属性名。使用双花括号的插值表达式{{myHero}} ，我们可以把属性名放置在视图模板中。

（通过 Angular CLI 命令：`ng  new  displaying-data` ）创建一个名为`displaying-data` 的新项目，然后修改`app.component.ts`文件，改成如下代码所示：

src/app/app.component.ts

```
import { Component } from '@angular/core'; 
@Component({  
	selector: 'app-root',
	template: `<h1>{{title}}</h1>
			   <h2>My favorite hero is: {{myHero}}</h2>
			`
})
export class AppComponent {  
  title = 'Tour of Heroes'; 
  myHero = 'Windstorm';
}
```

我们添加了两个属性（`title` 和 `myHero`）到原先空的组件中。

通过使用插值表达式，更改后的模板展示组件的两个属性：

src/app/app.component.ts (template)

```
template: `
  <h1>{{title}}</h1>
  <h2>My favorite hero is: {{myHero}}</h2>
  `
```

模板是ES6标准的以反引号（\`) 括起来的多行字符串。反引号（\`)与普通的单引号（\'）不同，它允许多行书写，这使得代码可读性更好。

Angular 自动从组件中获取`title` 和 `myHero`	两个属性的值，把这些值传递给浏览器。 当这些属性改变时，Angular 会更新显示的值。

更准确地说，更新发生在一些与视图有关的异步事件之后，比如说键盘事件、计时器结束、或者一个 HTTP 请求。

注意，我们不会直接调用 `new`  来创建`appComponent` 类的实例。Angular 会为我们创建这个实例。它是怎么做到的呢？

`@Component` 装饰器中的 CSS `selector` 指定了一个名为`<app-root>`的元素。这个元素在`index.html`文件中是一个占位符：

src/index.html (body)

```
<body>
  <app-root>loading...</app-root>
</body>
```

当我们在`main.ts`中设置`bootstrap`为`AppComponent`类后，Angular 会在`index.html` 中寻找`<app-root>` ， 找到后，会实例化`AppComponent` ，并把它渲染到这个标签内。

运行这个项目（`npm start`）。 应该能够看到如下所示的内容：

![Title and Hero](https://angular.io/generated/images/guide/displaying-data/title-and-hero.png)

接下来的部分内容，回顾这个应用里的代码选择。



## 2.行内模板 or 模板文件？

组件的模板可在两个地方中选择一个存放。我们可以使用`template`属性来定义模板为行内模板，或者在一个单独的	HTML 文件中定义模板，然后使用 `@Component` 装饰器的 `templateUrl` 属性，把它链接到组件的元数据中。

选择行内模板还是单独的 HTML 文件取决于 个人的代码风格、开发环境 或者 公司机构的代码规范。这里示例代码使用的是 行内的 HTML 模板， 因为这个模板很小，没有额外的 HTML 文件，这个 demo 更简单。

以上两种风格，模板数据绑定有相同的方法访问到组件的属性。




## 3.构造函数 or 变量实例化？

虽然，这个例子使用 变量赋值来初始化组件，你也可以通过构造函数来声明并实例化属性。

src/app/app-ctor.component.ts (class)

```typescript
export class AppCtorComponent {
  title: string;
  myHero: string;

  constructor() {
    this.title = 'Tour of Heroes';
    this.myHero = 'Windstorm';
  }
}
```

为了简便起见，本节中的示例代码，会更多地使用精简的“变量赋值”的形式。




## 4.`*ngFor` 显示数组的属性

为了展示一个英雄列表，我们增加一个英雄名字的数组到组件中，并重新定义`myHero`作为数组中的第一项。

src/app/app.component.ts (class)

```typescript
export class AppComponent {
  title = 'Tour of Heroes';
  heroes = ['Windstorm', 'Bombasto', 'Magneta', 'Tornado'];
  myHero = this.heroes[0];
}
```


Now use the Angular `ngFor` directive in the template to display each item in the `heroes` list.

现在在 模板中使用 Angular 的`*ngFor`指令来展示英雄列表的每一项内容。

src/app/app.component.ts (template)

```typescript
template: `
  <h1>{{title}}</h1>
  <h2>My favorite hero is: {{myHero}}</h2>
  <p>Heroes:</p>
  <ul>
    <li *ngFor="let hero of heroes">
      {{ hero }}
    </li>
  </ul>
`
```

我们使用了无序列表。在`<li>`元素中的`*ngFor`是 Angular 的重复指令。它标记`<li>`元素（以及它的子节点）作为重复的模板。

src/app/app.component.ts (li)

```
<li *ngFor="let hero of heroes">
  {{ hero }}
</li>
```

Don't forget the leading asterisk (*) in `*ngFor`. It is an essential part of the syntax. For more information, see the [Template Syntax](https://angular.io/guide/template-syntax#ngFor) page.

Notice the `hero` in the `ngFor` double-quoted instruction; it is an example of a template input variable. Read more about template input variables in the [microsyntax](https://angular.io/guide/template-syntax#microsyntax) section of the [Template Syntax](https://angular.io/guide/template-syntax) page.

Angular duplicates the `` for each item in the list, setting the `hero` variable to the item (the hero) in the current iteration. Angular uses that variable as the context for the interpolation in the double curly braces.

In this case, `ngFor` is displaying an array, but `ngFor` can repeat items for any [iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) object.

Now the heroes appear in an unordered list.

![After ngfor](https://angular.io/generated/images/guide/displaying-data/hero-names-list.png)



## 5.为数据创建一个类

The app's code defines the data directly inside the component, which isn't best practice. In a simple demo, however, it's fine.

At the moment, the binding is to an array of strings. In real applications, most bindings are to more specialized objects.

To convert this binding to use specialized objects, turn the array of hero names into an array of `Hero` objects. For that you'll need a `Hero` class.

Create a new file in the `app` folder called `hero.ts` with the following code:

src/app/hero.ts (excerpt)

```
export class Hero {
  constructor(
    public id: number,
    public name: string) { }
}
```

You've defined a class with a constructor and two properties: `id` and `name`.

It might not look like the class has properties, but it does. The declaration of the constructor parameters takes advantage of a TypeScript shortcut.

Consider the first parameter:

src/app/hero.ts (id)

```
public id: number,
```

That brief syntax does a lot:

- Declares a constructor parameter and its type.
- Declares a public property of the same name.
- Initializes that property with the corresponding argument when creating an instance of the class.

### 5.1 使用 Hero 类

After importing the `Hero` class, the `AppComponent.heroes` property can return a *typed* array of `Hero` objects:

src/app/app.component.ts (heroes)

```
heroes = [
  new Hero(1, 'Windstorm'),
  new Hero(13, 'Bombasto'),
  new Hero(15, 'Magneta'),
  new Hero(20, 'Tornado')
];
myHero = this.heroes[0];
```

Next, update the template. At the moment it displays the hero's `id` and `name`. Fix that to display only the hero's `name` property.

src/app/app.component.ts (template)

```
template: `
  <h1>{{title}}</h1>
  <h2>My favorite hero is: {{myHero.name}}</h2>
  <p>Heroes:</p>
  <ul>
    <li *ngFor="let hero of heroes">
      {{ hero.name }}
    </li>
  </ul>
`
```

The display looks the same, but the code is clearer.

## 6.`*NgIf`有条件地展示数据

Sometimes an app needs to display a view or a portion of a view only under specific circumstances.

Let's change the example to display a message if there are more than three heroes.

The Angular `ngIf` directive inserts or removes an element based on a *truthy/falsy* condition. To see it in action, add the following paragraph at the bottom of the template:

src/app/app.component.ts (message)

```
content_copy<p *ngIf="heroes.length > 3">There are many heroes!</p>
```

Don't forget the leading asterisk (*) in `*ngIf`. It is an essential part of the syntax. Read more about `ngIf` and `*` in the [ngIf section](https://angular.io/guide/template-syntax#ngIf) of the [Template Syntax](https://angular.io/guide/template-syntax) page.

The template expression inside the double quotes, `*ngIf="heroes.length > 3"`, looks and behaves much like TypeScript. When the component's list of heroes has more than three items, Angular adds the paragraph to the DOM and the message appears. If there are three or fewer items, Angular omits the paragraph, so no message appears. For more information, see the [template expressions](https://angular.io/guide/template-syntax#template-expressions) section of the [Template Syntax](https://angular.io/guide/template-syntax) page.

Angular isn't showing and hiding the message. It is adding and removing the paragraph element from the DOM. That improves performance, especially in larger projects when conditionally including or excluding big chunks of HTML with many data bindings.

Try it out. Because the array has four items, the message should appear. Go back into `app.component.ts"` and delete or comment out one of the elements from the hero array. The browser should refresh automatically and the message should disappear.

## 7.总结

Now you know how to use:

- **Interpolation** with double curly braces to display a component property.
- **ngFor** to display an array of items.
- A TypeScript class to shape the **model data** for your component and display properties of that model.
- **ngIf** to conditionally display a chunk of HTML based on a boolean expression.

Here's the final code:

src/app/app.component.ts

```typescript
​	import { Component } from '@angular/core'; 
  import { Component } from '@angular/core';

  import { Hero } from './hero';

  @Component({
    selector: 'my-app',
    template: `
    <h1>{{title}}</h1>
    <h2>My favorite hero is: {{myHero.name}}</h2>
    <p>Heroes:</p>
    <ul>
      <li *ngFor="let hero of heroes">
        {{ hero.name }}
        </li>
    </ul>
    <p *ngIf="heroes.length > 3">There are many heroes!</p>
  `
  })
  export class AppComponent {
    title = 'Tour of Heroes';
    heroes = [
      new Hero(1, 'Windstorm'),
      new Hero(13, 'Bombasto'),
      new Hero(15, 'Magneta'),
      new Hero(20, 'Tornado')
    ];
    myHero = this.heroes[0];
  }
```


src/app/hero.ts




src/app/app.module.ts





main.ts
