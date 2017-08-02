# 初识 Angular 之（一）架构

Angular 是一个构建客户端应用的框架。它使用 HTML 和 JavaScript 或者可以被编译成 JavaScript（比如 TypeScript） 的语言。

Angular 框架由多个库文件(libraries)组成。其中， 有些是核心库，有些则是可选的。

Angular 应用是这样写的：编写有 Augular 的指令的 HTML 模板（templates），编写组件类（component classes）来管理这些模板，在服务（services）中添加应用逻辑， 再把这些组件和服务封装在模块（modules）中。

然后，通过引导 根模块（root module） 来启动这个应用。Angular 接管应用程序，在浏览器中展示应用的内容，同时根据提供的指令来响应用户的交互行为。

当然，Angular 框架远不止上面提到的内容。 你将在后面学到更多细节。现在，先关注框架的整体的架构。


## 1.Modules 模块

Angular 应用是模块化的，并且 Angular 有自己的模块化系统，叫做 NgModules.

Angular模块（NgModules） 很重要。这里会介绍模块（Modules），在 NgModules 章节会深入讲解模块与Angular模块（NgModules）。

每一个 Angular 应用至少有一个 NgModules 类，即根模块（ root module），通常命名为 AppModule.

小的应用也许只有根模块这一个模块。大部分应用有许多 特性模块（feature modules）。每一个特性模块都是一段紧密结合的代码块，专注于一个应用领域，工作流程或者一组密切关联的功能。

一个 NgModule, 无论是根模块还是特性模块，都是一个带有 @NgModule 装饰器（ decorator）的类。

装饰器是一种函数，它能够修改 JavaScript 类。Angular 有许多装饰器。它们把元数据（metadata）附加到类中，这样 Angular 就知道这些类有什么含义 以及它们应该如何工作。

@NgModule 是一个装饰器函数，它有一个元数据（metadata）对象。这个对象的属性（properties）用于修饰这个模块。主要属性有以下这5个：

1) declarations: 从属于这个模块的视图类（ view classes）。Angular 有3种视图类：组件（components）, 指令（directives） 以及管道（pipes）

2) exports: declarations 的子集，在其他模块的组件模板中可见以及可用的视图类（即暴露给其他模块用的视图类）。

3) imports: 在这个模块的组件模板中需要使用到的类所在的其它模块（即依赖的其他模块）。

4) providers: 服务（service）的创建者，这个模块将这些服务添加到整个全局服务集合中。这些服务在整个应用中可用。

5) bootstrap: 应用的主视图，称作根组件（root component）， 存储着应用其他的视图。只有根模块（root modul）需要设置这个属性。


以下是一个根模块示例：

src/app/app.module.ts

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
@NgModule({
  imports:      [ BrowserModule ],
  providers:    [ Logger ],
  declarations: [ AppComponent ],
  exports:      [ AppComponent ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { }
```


这里导出 AppComponent 只是为了演示如何导出的，在这个例子中不是必须的。根模块不需要导出任何东西，因为其他组件不需要导入这个根模块。

通过引导根模块来启动一个应用程序。在开发期间中，你很可能在 main.ts 文件中像这样引导 AppModule：

src/main.ts

```typescript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.module';

platformBrowserDynamic().bootstrapModule(AppModule);
```


### 1.1 Angular模块（NgModules） vs. JavaScript 模块

Angular模块（NgModule） 是由 @NgModule 修饰的类，它是 Angular 的基本特性。

JavaScript 也有自己的用于管理 JavaScript 对象集合的模块系统。它与 NgModule 模块系统完全不同且毫不相关。

在 JavaScript 中，每个文件都是一个模块，所有定义在这个文件中的对象都从属于这个模块。模块使用关键字 export 把它的某些对象声明为公开的对象。JavaScript 模块使用 import 声明语句 从其他模块中引用这些公开的对象。

这是两种不同但互补的模块系统，在你的应用中都将会使用到。


### 1.2 Angular 库（Angular libraries）

Angular 以 一系列的JavaScript 模块的形式推出市场。你可以把他们当做库模块。

每一个 Angular 库的名字都带有 @angular 前缀。

通过 npm 包管理工具安装这些库，通过 JavaScript 的 import 声明语句来导入他们中的部分内容。

比如，从 @angular/core 库模块中导入 Angular 的 Component 装饰器：

	import { Component } from '@angular/core';


也可以使用 JavaScript 的 import 声明语句 来从 Angular 库模块中导入Angular模块（NgModules）：

	import { BrowserModule } from '@angular/platform-browser';


在之前的 根模块示例中，应用的模块需要使用 BrowserModule 里的素材。为了访问到这些素材，需要把 BrowserModule 添加到 @NgModule 的元数据中：

	imports: [ BrowserModule ]
这样，同时使用了 Angular和 JavaScript 的模块系统。

因为它们使用相同的单词如 imports exports, 很容易搞混淆这两种模块系统。



## 2.组件 Components

一个组件控制着屏幕上的一块区域（即视图）。

我们在类里定义组件的应用逻辑。这个类通过 API 的属性和方法与视图交互。

例如，HeroListComponent 组件类有一个 hero 属性返回一个从服务（service）中获取的 heroes 数组。 HeroListComponent 还有一个 selectHero() 方法。当用户从列表中点击选中一个 hero 时，这个方法设置 selectedHero 属性。

```typescript
export class HeroListComponent implements OnInit {
  heroes: Hero[];
  selectedHero: Hero;

  constructor(private service: HeroService) { }

  ngOnInit() {
    this.heroes = this.service.getHeroes();
  }

  selectHero(hero: Hero) { this.selectedHero = hero; }
}
```

在用户使用应用程序的过程中，Angular 会创建、更新、销毁组件。在应用程序的生命周期的任意时刻，它都可以通过可选的生命周期钩子（lifecycle hooks）来作出响应，比如 上面声明的 ngOnInit() 钩子。




## 3.模板 Templates

我们在组件自带的模板中定义组件的视图。 模板以 HTML 形式存在，它告诉 Angular 如何去渲染组件。

除了有一点点差别，模板看起来像是普通的 HTML。


```html
<h2>Hero List</h2>
<p><i>Pick a hero from the list</i></p>
<ul>
	<li *ngFor="let hero of heroes" (click)="selectHero(hero)">
	{{hero.name}}
	</li>
</ul>
<hero-detail *ngIf="selectedHero" [hero]="selectedHero"></hero-detail>
```

虽然这个模板使用典型的 HTML 元素 比如 `<h2>` 和 `<p>`， 它有些差异。
像 `*ngFor`, `{{hero.name}}`, `(click)`,` [hero]` 以及 `<hero-detail>` 这些代码，它们使用的是 Angular的模板语法。

这个模板最后一行，`<hero-detail>` 标签 是一个自定义的元素，用来展示一个新的组件，即 HeroDetailComponent.

HeroDetailComponent 与 HeroListComponent 组件不同，它用于展示关于某个特定 hero 的事实。
这个 hero 是用户从 HeroListComponent 的列表中选择的。HeroDetailComponent 是 HeroListComponent 的子组件。

注意 `<hero-detail>` 与原生的 HTML 元素 融洽地放置在一起。 在同一个布局中，自定义组件与原生 HTML 完美地混合在一起。



## 4.元数据 Metadata

元数据告诉 Angular 如何处理一个类。

返回去看 HeroListComponent 的代码， 它仅仅是一个类。完全看不到没有框架的痕迹，里面没有 Angular。

事实上，HeroListComponent 真的是仅仅是一个类，直到我们告诉 Angular 它是一个组件。

告诉 Angular HeroListComponent 是一个组件， 把元数据附加到这个类上。

在 TypeScript 中，我们通过 装饰器（decorator）来添加元数据。 以下代码是组件 HeroListComponent 的一些元数据：

```typescript
@Component({
	selector:    'hero-list',
	templateUrl: './hero-list.component.html',
	providers:  [ HeroService ]
})
export class HeroListComponent implements OnInit {
	/* . . . */
}
```

装饰器 @Component 把紧挨着它的类指定为一个组件类。

装饰器 @Component 必须接收一个配置对象作为参数，它包含 Angular 创建并展示这个组件及其视图所需要的信息。

以下是一些最有用的 @Component 的配置选项：

1）selector: CSS 选择器。告诉 Angular 创建这个组件的实例并将这个实例插入到 CSS 选择器（`<hero-list>`标签） 所在的父级 HTML 上。如果一个 app 的 HTML 包含 <hero-list> 标签， Angular 会将组件 HeroListComponent 视图的实例插入到这个标签之间。

2）templateUlr： 这个组件的 HTML 模板所在的相对位置。

3）providers: 组件所需要的服务的依赖注入提供商数组。它告知 Angular 这个组件的构造函数需要 HeroService 服务，以便获取一个 heroes 的列表来展示。

@Component 装饰器里的元数据 告知 Angular 从哪里去获取我们为这个组件指定的构建单元。

模板、元数据和组件一起描绘视图。

我们以相似的方式使用其他元数据装饰器来指导 Angular 的行为。@Injectable 、@Input 和 @Output 是几个比较常用的装饰器。

我们必须在代码中添加元数据，这样 Angular 才知道该做什么。



## 5. 数据绑定 Data binding

不用框架时，我们需要自己把数据传递到 HTML 控件、响应用户的行为并更新数据。自己写这些推送/拉取逻辑的代码枯燥的、易错的，这样的代码读起来像噩梦。任何有经验的 jQuery 程序员都能够作证。

Angular 支持数据绑定，一种使模板各部分与组件各部分相互配合的机制。在模板的 HTML 中添加绑定的标记来告诉 Angular 如何连接这两部分。

![](https://angular.io/generated/images/guide/architecture/databinding.png)

正如这张图片所示，Angular 有4种数据绑定的方式。每种方式都有（数据流动的）方向：可能是从 DOM 到组件，也可能是从 组件到 DOM， 也可能是双向的。

组件 HeroListComponent 的示例模板中 有三种形式：

```html
<li>{{hero.name}}</li>
<hero-detail [hero]="selectedHero"></hero-detail>
<li (click)="selectHero(hero)"></li>
```

1）插值表达式（interpolation）：插值表达式 `{{ hero.name }}` 在 `<li> `元素之间展示组件的 hero.name 属性值。

2）属性绑定（property binding）：属性绑定 `[hero]` 将父组件 HeroListComponent 的 selectedHero 的值传递给向子组件 <hero-detail> 的 hero 属性。

3）事件绑定（event binding）： 当用户点击一个 hero 的名字是，`(click)` 事件绑定调用了组件的 selectHero 方法。


双向绑定是第四种重要的绑定方法。它使用 `[(ngModel)]` 指令，在一个表达式中同时绑定属性和事件。

```html
<input [(ngModel)]="hero.name">
```

在双向绑定中，数据的属性值像属性绑定一样，从组件流向 input 输入框。用户输入值改变，数据从 input　输入框流回到组件，将最后的值重置为组件里的属性的值，正如事件绑定一样。

每次 JavaScript 事件循环，Angular 都会处理所有的数据绑定，从应用组件的根部到所有的子组件。

数据绑定在组件与其模板的通讯中起着重要的作用。

![](https://angular.io/generated/images/guide/architecture/component-databinding.png)



数据绑定在父、子组件的通讯中也起着重要的作用。

![](https://angular.io/generated/images/guide/architecture/parent-child-binding.png)



## 6. 指令 Directives

Angular 模板是动态的。当 Angular 渲染模板时，它会根据指令（directives）给的指示去动态地改变 DOM.

指令是带有 @Directive 装饰器的类。组件是带有模板的指令。 @Component 装饰器实际上是  拓展了模板化功能的 @Directive 装饰器。

虽然从技术上来说，组件是指令，但组件对于 Angular 应用来说非常地特别和重要，所以这个架构速览 把组件和指令分开来论述。

还有两种指令：结构指令和属性指令。

它们在元素标签中看起来像属性（attribute）那样工作。有时指令仅仅是以名字形式存在，更多地时候是作为赋值或者绑定的目标。

结构指令可以通过增加、移除、替换 DOM 元素来改变布局。

以下示例代码使用两种内置的结构指令：

src/app/hero-list.component.html (structural)

```html
<li *ngFor="let hero of heroes"></li>
<hero-detail *ngIf="selectedHero"></hero-detail>
```

`*ngFor `告知 Angular 在每一个 `<li>` 标签中展示 heroes 列表中的 hero.

只有当 selectedHero 存在时，`*ngIf` 才会把 HeroDetail 组件包含进来。

属性指令（Attribute directives）改变已存在元素的外观或行为。在模板中，它们看起来像是普通的 HTML 属性，故得名属性指令。

`ngModel` 指令使用双向的数据绑定。它是典型的属性指令。 通过设置元素显示的值以及响应 change 事件，`ngModel`指令改变已存在元素（尤其是 input）的行为。

src/app/hero-detail.component.html (ngModel)

```html
<input [(ngModel)]="hero.name">
```

Angular 还有许多指令。有些可以改变布局结构（比如 ngSwitch），有些则可以改变 DOM 元素和组件的外观（比如 ngStyle、ngClass）。

当然，我们还可以编写自定义的指令。像 HeroListComponent 的组件，可以看做是一种自定义指令。



## 7. 服务 Services

服务（Service）是一个很宽泛的类别，包括应用需要的任何值、函数、或者功能。

几乎任何东西都可以是服务。服务是一个典型的类，它作用有限且定义明确。它应该很好地完成特定的任务。

例如：

- logging service 日志服务
- data service 数据服务
- message bus 消息总线
- tax calculator 税收计算器
- application configuration 应用配置

关于服务，没有什么Angular特性。没有关于服务的定义，没有服务的基类，也没有地方注册服务。

然而，服务对于任何 Angular 应用都是至关重要的。组件是服务的主要使用者。

以下是一个在浏览器控制台打印日志的服务类的示例代码：

src/app/logger.service.ts (class)

```
content_copyexport class Logger {
  log(msg: any)   { console.log(msg); }
  error(msg: any) { console.error(msg); }
  warn(msg: any)  { console.warn(msg); }
}
```

这个 HeroService 使用 `Promise` 来获取 heroes 数据。它基于 Logger 服务和另外一个处理服务器通讯的枯燥工作的 BackendService 服务。

src/app/hero.service.ts (class)

```
content_copyexport class HeroService {
  private heroes: Hero[] = [];

  constructor(
    private backend: BackendService,
    private logger: Logger) { }

  getHeroes() {
    this.backend.getAll(Hero).then( (heroes: Hero[]) => {
      this.logger.log(`Fetched ${heroes.length} heroes.`);
      this.heroes.push(...heroes); // fill cache
    });
    return this.heroes;
  }
}
```

到处都有服务。

组件类应该是精简的。它们不从服务器中获取数据，不验证用户的输入或者直接在控制台打印日志。它们授权服务来处理这些任务。

组件的工作只是实现用户体验，不做其他的事情。它周旋于视图（由模板渲染）和应用逻辑（通常包含一些模型的概念）。 好的组件展示数据绑定的属性和方法。它授权服务处理其他重要的事情。

Angular 并不强制要求执行这些原则。它不会抱怨你用 3000 行代码写一个 kitchen sink 组件。

Angular 帮我们遵循这些原则，它使得在服务中考虑应用逻辑很容易，并且通过`依赖注入（dependency injection）`使得服务在组件中可用。




## 8. 依赖注入 Dependency injection

依赖注入 是提供类的新的实例的一种方式，这个实例有它所需的所有依赖。大多数依赖都是服务。 Angular 使用依赖注入来为新的组件提供它们所需要的服务。

Angular 通过查看组件构造函数的参数的类型来识别组件所需要的服务。例如，HeroListComponent 的构造函数需要一个 HeroService 类型的参数。

src/app/hero-list.component.ts (constructor)

```typescript
content_copyconstructor(private service: HeroService) { }
```

当 Angular 创建组件时，它会先向注入器（ injector） 请求组件所需要的服务。

注入器维护着一个之前创建的服务实例的容器。如果被请求的服务实例不在这个容器中，注入器在返回服务给 Angular 之前，会创建这个服务的实例并把它添加到容器中。当所有请求的服务都被解决并返回后，Angular 会调用组件的构造函数并把这些服务作为参数传入。这就是依赖注入。

HeroService  注入的过程看起来像这样：

![Service](https://angular.io/generated/images/guide/architecture/injector-injects.png)



如果注入器没有 `HeroService`，它怎么知道如何创建一个`HeroService`呢？

简单地说，我们必须在之前就已经注册过了`HeroService`的提供商（ **provider**）。 提供商能够创建并返回服务，通常它就是服务类本身。

我们可以在模块或组件中注册**providers**. 

通常，我们在**根模块**中添加**providers**, 这样服务的同一个实例在应用的每个地方都可用。

src/app/app.module.ts (module providers)

```
content_copyproviders: [
  BackendService,
  HeroService,
  Logger
],
```

或者，添加在`@Component`的元数据中的**providers**属性中，注册在组件层级。

src/app/hero-list.component.ts (component providers)

```
content_copy@Component({
  selector:    'hero-list',
  templateUrl: './hero-list.component.html',
  providers:  [ HeroService ]
})
```

注册在组件层级意味着在这个组件的每一个新的实例中，我们都会获得这个服务的新的实例。

关于依赖注入需要记住的几个要点：

- 依赖注入缠绕在 Angular 框架中，被到处使用。
- 注入器是主要的机制。
  -  注入器维持一个存放它所创建的服务实例的容器。
  -  注入器从 提供商（provider）中创建一个新的服务实例。
- 提供商是一个创建服务的配方。
- 用 注入器注册提供商。



### 结语：

我们已经学习了一个 Angular 应用的八个主要构建单元的基础知识：

- Modules 模块
- Components 组件
- Templates 模板
- Metadata 元数据
- Data binding 数据绑定
- Directives 指令
- Services 服务
- Dependency injection 依赖注入



这是 Angular　应用中其他一切的基础，有了这些基础就足以继续开发。但这没有包含我们所需要知道的所有内容。

以下是其他重要的 Angular 特性和服务。 大部分将涵盖在这份文档中。

- Animations 动画:  不需要深入了解动画技术或者CSS，使用 Angular 动画库给组件添加动画效果。
- Change detection 变化检测: 变化检测文档将会涵盖 Angular 如何决定组件属性值是否已改变，何时更新视图，如何使用**zones**拦截异步活动，以及运行变化检测策略的。
- Events 事件: 事件文档将涵盖通过订阅和发布事件的机制，如何使用组件和服务来唤起事件。
- Forms 表单: 使用 基于HTML的验证和脏检查机制，支持复杂的数据输入情形。
- HTTP: 通过 HTTP 客户端与服务端通讯以获取数据、保存数据以及来唤起 服务端的响应。
- Lifecycle hooks  生命周期钩子:  利用组件的生命周期的关键时刻，从创建到销毁，应用生命周期钩子接口。
- Pipes 管道:  使用模板中的管道来改变展示的值，从而改善用户体验。比如  `currency`这个管道表达式：

> `price | currency:'USD':true`

它将 42.33 的价格显示为 `$42.33`。

- Router 路由:  在客户端应用中，从一个页面导航到另一个页面，从不离开浏览器。

- Testing 测试:  使用 Angular Testing Platform，当应用的各个部分与 Angular 框架交互时，进行单元测试。


