# 初识 Angular 之（二）数据的展现 

通过把 Angular 组件的属性绑定到 HTML 模板的控件上，我们可以展示数据。

本小节中，我们将会创建一个英雄列表组件。我们将展示英雄名字的列表，并根据条件来决定是否在列表下方展示一条信息。

最终的 效果是这样的：

![Final UI](https://angular.io/generated/images/guide/displaying-data/final.png)

[在线例子](https://angular.io/generated/live-examples/displaying-data/eplnkr.html) /  [下载示例代码](https://angular.io/generated/zips/displaying-data/displaying-data.zip) 演示了这一节所有的提到的代码片段和语法。



## 1.用插值表达式展示组件的属性

通过插值表达式来绑定属性名，是展示组件属性最简单的方式。使用双花括号的插值表达式{{myHero}} ，我们可以把属性名`myHero` 放置在视图模板中。

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

模板是ES6标准规定的以反引号（\`） 括起来的多行字符串。反引号（\`）与普通的单引号（'）不同，它允许多行书写，这使得代码可读性更好。

Angular 会自动从组件中获取`title` 和 `myHero` 这两个属性的值，把这些值传递给浏览器。 当这些属性改变时，Angular 会更新显示的值。

更准确地说，更新发生在一些与视图有关的异步事件之后，比如说键盘事件、计时器结束、或者一个 HTTP 请求。

**注意**，我们不会直接调用 `new`  来创建`appComponent` 类的实例。Angular 会为我们创建这个实例。它是怎么做到的呢？

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

组件的模板可在两个地方中选择一个存放。我们可以使用`template`属性来定义模板为行内模板，或者在一个单独的  HTML 文件中定义模板，然后使用 `@Component` 装饰器的 `templateUrl` 属性，把它链接到组件的`元数据`中。

选择行内模板还是单独的 HTML 文件取决于 个人的代码风格、开发环境 或者 公司机构的代码规范。本章节的示例代码使用的是 行内的 HTML 模板， 因为这个模板很小。没有额外的 HTML 文件，这个 demo 更简单。

以上两种风格，模板数据绑定有相同的方法访问到组件的属性。




## 3.构造函数 or 变量实例化？

虽然，这个例子使用 `变量赋值`来初始化组件，你也可以通过`构造函数`来声明并实例化属性。

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

为了简便起见，本节中的示例代码，会更多地使用精简的`变量赋值`的形式。




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

现在，在 模板中使用 Angular 的`*ngFor`指令来展示英雄列表的每一项内容。

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

```typescript
<li *ngFor="let hero of heroes">
  {{ hero }}
</li>
```

** 不要忘了`ngFor` 前面的星号`*`， 它是这个指令语法必不可少的组成部分。 更多的内容，请查阅 [模板语法章节](https://angular.io/guide/template-syntax#ngFor) **。

注意 在`*ngFor` 指令的双引号之间的 `hero` 是模板的输入变量。更多关于模板输入变量的信息，请查阅模板语法章节的 [微语法部分](https://angular.io/guide/template-syntax#microsyntax) 。

Angular 为列表中的每一项复制了`<li>`标签， 把 `hero` 变量设置为当前循环的值。Angular 把这个变量作为 双花括号里插值表达式的上下文。

在这个例子中，`ngFor` 展示了一个数组， 但`ngFor`  可用于循环任何`可迭代对象`的。

现在，英雄们出现在一个无序列表中：

![After ngfor](https://angular.io/generated/images/guide/displaying-data/hero-names-list.png)



## 5.为数据创建类

这个应用的代码直接在组件里定义数据，这不是最好的实现方式。当然，在一个简单的 demo 的，这样写没什么问题。

现在，这里绑定的是一个字符型的数组。在实际的应用中，绑定的大多是特殊的对象。

为了将绑定改为使用特殊的对象，我们把这个英雄名字的数组改写成 `Hero` 对象的数组。 为此，我们需要一个 `Hero` 类。

在这个`app`文件夹里创建一个新的文件，命名为`hero.ts`， 代码如下：

src/app/hero.ts 

```typescript
export class Hero {
  constructor(
    public id: number,
    public name: string) { }
}
```

我们定义了一个类，它有一个构造函数和两个属性（`id` 和 `name` ）。

它也许看起来不像是一个带有属性的类，但它确实是有属性的。这个构造函数参数的声明使用了 `TypeScript` 简写形式。

想想最初的参数：

src/app/hero.ts (id)

```typescript
public id: number,
```

这种简写语法做了许多事情：

- 声明构造函数的参数及其类型。

- 声明公开的同名的属性。

- 创建类的实例时，根据相应的参数初始化属性。

  ​

### 5.1 使用 Hero 类

在导入`Hero` 后，`AppComponent.heroes` 属性返回一个`Hero` 类型的数组。

src/app/app.component.ts (heroes)

```typescript
heroes = [
  new Hero(1, 'Windstorm'),
  new Hero(13, 'Bombasto'),
  new Hero(15, 'Magneta'),
  new Hero(20, 'Tornado')
];
myHero = this.heroes[0];
```

下一步，更新`模板`。现在，模板会展示 hero 的 `id` 和 `name`。我们需要修改一下，变成只显示 hero 的`name`属性。 

src/app/app.component.ts (template)

```typescript
template: `
  <h1>{{title}}</h1>
  <h2>My favorite hero is: {{myHero.name}}</h2>
  <p>Heroes:</p>
  <ul>
    <li *ngFor="let hero of heroes">
      {{ hero.name }}
    </li>
  </ul>
`.
```

显示效果看起来一样，但是代码变得更加干净。



## 6.`*ngIf` 有条件地展示数据

有时，应用只在某个特定的条件下，才需要显示视图或者视图的一部分。

让我们来改一下例子：如果超过3个 hero，才显示一条消息。

Angular 的`ngIf` 指令 基于 true 或false 的条件判断来插入或者移除一个元素。

为了看到它实际效果，我们在模板的底部增加一个段落：

src/app/app.component.ts (message)

```typescript
<p *ngIf="heroes.length > 3">There are many heroes!</p>
```

** 不要忘了`ngIf` 前面的星号`*`， 它是这个指令语法必不可少的组成部分。 更多的内容，请查阅 [模板语法章节的ngIf部分]((https://angular.io/guide/template-syntax#ngIf) ) **。

在双引号里的模板表达式` "heroes.length >3"` 看起来很像 `typescript`。 当组件的 heroes 列表有超过3个 hero 时， Angular  会在 DOM 增加 一个段落，这条消息就会出现。如果小于或等于3个 hero， 那么 Angular 会就忽略这个段落，也就不显示这条消息。更多内容，请查阅模板语法章节的 [模板表达式部分](https://angular.io/guide/template-syntax#template-expressions) 。

Angular 不是显示或者隐藏这条消息。它是从DOM 里新增或者移除了这个`<p>`元素。这样做可以提高性能，尤其是在大型项目中需要包含或者排除 大量的有许多数据绑定的 HTML 时。

试一试吧。因为数组里有4个成员，这条消息会显示出来。回到 `app.component.ts` 中，删除或者注释掉 hero 数组中的一个成员。 浏览器会自动刷新，那条消息应该会消失。



## 7.总结

现在我们知道如何使用：

- 使用双花括号的插值表达式 `{{ }} `来展示组件的属性。
- `*ngFor` 指令用来展示数组。
- TypeScript 类，用来定义组件的数据类型以及展示这个数据的属性。
- `*ngIf`  指令基于表达式的布尔值来决定是否展示一段 HTML 代码。

最终的代码是这样的：

src/app/app.component.ts

```typescript
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

```typescript
export class Hero {
  constructor(
    public id: number,
    public name: string) { }
}
```

src/app/app.module.ts

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule }  from '@angular/platform-browser';

import { AppComponent } from './app.component';

@NgModule({
  imports: [
    BrowserModule
  ],
  declarations: [
    AppComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }

```


main.ts

```typescript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';

platformBrowserDynamic().bootstrapModule(AppModule);

```