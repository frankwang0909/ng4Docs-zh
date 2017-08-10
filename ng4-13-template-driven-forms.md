# 表单 Forms

Forms are the mainstay of business applications. You use forms to log in, submit a help request, place an order, book a flight, schedule a meeting, and perform countless other data-entry tasks.

表单是商业应用的支柱。我们使用表单来登录、提交帮助请求、下单、订机票、安排会议以及执行无数其他的需要数据输入的任务。

In developing a form, it's important to create a data-entry experience that guides the user efficiently and effectively through the workflow.

在开发表单时，高效地引导用户完成整个流程的数据输入体验是很重要的。

Developing forms requires design skills (which are out of scope for this page), as well as framework support for *two-way data binding, change tracking, validation, and error handling*, which you'll learn about on this page.

开发表单需要设计技巧（这超出了本章节的范畴）以及框架对于数据双向绑定、变化检查、验证、错误处理的支持（我们将在本节学习这些内容）。

This page shows you how to build a simple form from scratch. Along the way you'll learn how to:

本节将告诉你如何从零开始创建一个简单的表单。接下来，我们将会学习如何：

- Build an Angular form with a component and template.

  创建一个带有组件和模板的 Angular 表单。
- Use `ngModel` to create two-way data bindings for reading and writing input-control values.

  使用 `ngModel` 来双向绑定 input 输入框的值。
- Track state changes and the validity of form controls.

  追踪表单控件的状态变化和有效性。
- Provide visual feedback using special CSS classes that track the state of the controls.

  使用追踪控件状态的特殊 CSS 样式类来提供视觉上的反馈。
- Display validation errors to users and enable/disable form controls.

  向用户展示验证错误信息以及启用或禁用表单控件。
- Share information across HTML elements using template reference variables.

  通过使用 模板引用变量（template reference variables）在 HTML 元素间分享信息。

在 Plunker 上可以运行[在线例子](https://angular.io/generated/live-examples/forms/eplnkr.html)或者[下载](https://angular.io/generated/zips/forms/forms.zip)这些示例代码。



## 模板驱动式表单 Template-driven forms 

You can build forms by writing templates in the Angular [template syntax](https://angular.io/guide/template-syntax) with the form-specific directives and techniques described in this page.

我们可以用 Angular 模板语法的表单特定指令和本节介绍的技术来编写模板，从而构建表单。

*You can also use a reactive (or model-driven) approach to build forms. However, this page focuses on template-driven forms.*

*我们也可以使用响应式（或者说模型驱动式）方法来构建表单。但在本节，我们只专注于模板驱动式表单。*

You can build almost any form with an Angular template—login forms, contact forms, and pretty much any business form. You can lay out the controls creatively, bind them to data, specify validation rules and display validation errors, conditionally enable or disable specific controls, trigger built-in visual feedback, and much more.

使用 Angular 模板，我们可以构建几乎所有表单：登录表单、联系表单以及几乎任何的商业报表。我们可以有创意地布局这些控件、给它们绑定数据、指定验证规则、展示验证错误信息、有条件地启用或者禁用指定的表单控件、触发内置的视觉反馈等等。

Angular makes the process easy by handling many of the repetitive, boilerplate tasks you'd otherwise wrestle with yourself.

Angular 通过处理许多我们原本需要绞尽脑汁去处理的重复而呆板的工作，使得处理表单的过程变得更容易。

You'll learn to build a template-driven form that looks like this:

我们将学习构建类似于这样的模板驱动式表单：

![Clean Form](https://angular.io/generated/images/guide/forms/hero-form-1.png)

The *Hero Employment Agency* uses this form to maintain personal information about heroes. Every hero needs a job. It's the company mission to match the right hero with the right crisis.

*英雄职业介绍所* 使用这个表单来维护英雄的个人信息。每个英雄都需要工作。这家公司的使命就是为英雄匹配合适的工作。

Two of the three fields on this form are required. Required fields have a green bar on the left to make them easy to spot.

在这个三栏的表单中，有两栏是必填项。为了使必填项更显眼，必填项左侧有绿色的边框。

If you delete the hero name, the form displays a validation error in an attention-grabbing style:

如果我们删除英雄的名字， 这个表单将以醒目的样式来展示验证错误信息：

![Invalid, Name Required](https://angular.io/generated/images/guide/forms/hero-form-2.png)

Note that the *Submit* button is disabled, and the "required" bar to the left of the input control changes from green to red.

注意，此时 Submit 按钮 被禁用了。必填项输入框的左侧绿色边框变成红色。

You can customize the colors and location of the "required" bar with standard CSS.

我们可以用标准的 CSS 来自定义必选项的样式颜色和位置。

You'll build this form in small steps:

我们将按照以下步骤来构建这个表单。

1. Create the `Hero` model class. 

   创建一个 hero 模型类（实体类）。

2. Create the component that controls the form. 

   创建一个控制这个表单的组件。

3. Create a template with the initial form layout. 

   创建一个具有初始表单布局的模板。

4. Bind data properties to each form control using the `ngModel` two-way data-binding syntax. 

   使用 `ngModel` 双向绑定语法，为每个表单控件绑定数据属性。

5. Add a `name` attribute to each form-input control.

    给每一个 表单输入控件增加一个 `name` 属性。

6. Add custom CSS to provide visual feedback. 

   增加自定义 CSS 来提供视觉上的反馈。

7. Show and hide validation-error messages. 

   显示或者隐藏验证错误消息。

8. Handle form submission with *ngSubmit*. 

   用 `ngSubmit` 来处理表单提交。

9. Disable the form’s *Submit* button until the form is valid. 

   禁用表单的 提交按钮，直到表单是有效的（即所有表单控件都符合通过验证规则）为止。

## 设置 Setup 

Follow the [setup](https://angular.io/guide/setup) instructions for creating a new project named angular-forms.

创建一个名为`angular-forms` 的新项目（Angular CLI 命令：ng new angular-forms）。

## 创建 Hero 模型类 Create the Hero model class

As users enter form data, you'll capture their changes and update an instance of a model. You can't lay out the form until you know what the model looks like.

当用户输入表单数据时，我们捕获数据的变化，并且更新（数据）模型的实例。我们无法设计表单，除非我们知道这个表单的模型。

A model can be as simple as a "property bag" that holds facts about a thing of application importance. That describes well the `Hero` class with its three required fields (`id`, `name`, `power`) and one optional field (`alterEgo`).

模型可以是个简单的“属性包”，用来存放任何应用重要的信息。我们这里 使用三个必选的字段 (`id`、`name`、`power`) 和一个可选的字段 (`alterEgo` ) 来描述 `Hero`  类是很合适的。

In the `app` directory, create the following file with the given content:

在 `app` 目录下，创建一个包含以下内容的文件。

src/app/hero.ts

```typescript
export class Hero {

  constructor(
    public id: number,
    public name: string,
    public power: string,
    public alterEgo?: string
  ) {  }

}
```

It's an anemic model with few requirements and no behavior. Perfect for the demo.

这个模型（实体类）只有少量的需求（属性），没有任何行为（方法）。对于演示来说，它非常合适。

The TypeScript compiler generates a public field for each `public` constructor parameter and automatically assigns the parameter’s value to that field when you create heroes.

当我们创建 hero 的实例时，TypeScript 编译器会为每一个带有`public` 修饰符的构造函数参数创建一个公共的字段，并且会自动将参数的值赋值给每个字段。

The `alterEgo` is optional, so the constructor lets you omit it; note the question mark (?) in `alterEgo?`.

`alterEgo` 是可选的，所以构造函数允许你省略它。注意在`alterEgo?` 里的问号（这个问号表示这个参数是可选的）。

You can create a new hero like this:

我们可以像这样实例化一个 hero：

src/app/hero-form.component.ts (SkyDog)

```typescript
let myHero =  new Hero(42, 'SkyDog',
                       'Fetch any object at any distance',
                       'Leslie Rollover');
console.log('My hero is called ' + myHero.name); // "My hero is called SkyDog"
```



## 创建表单组件 Create a form component

An Angular form has two parts: an HTML-based *template* and a component *class* to handle data and user interactions programmatically. Begin with the class because it states, in brief, what the hero editor can do.

Angular 表单由两部分组成：基于 HTML 的模板 和 有计划地处理数据以及用户交互的组件类。先从 这个组件类开始，简单地说，是因为它声明了 hero 编辑器能够做什么。

Create the following file with the given content:

创建包含以下代码的文件：

src/app/hero-form.component.ts (v1)

```typescript
import { Component } from '@angular/core';

import { Hero }    from './hero';

@Component({
  selector: 'hero-form',
  templateUrl: './hero-form.component.html'
})
export class HeroFormComponent {

  powers = ['Really Smart', 'Super Flexible',
            'Super Hot', 'Weather Changer'];

  model = new Hero(18, 'Dr IQ', this.powers[0], 'Chuck Overstreet');

  submitted = false;

  onSubmit() { this.submitted = true; }

  // TODO: Remove this when we're done
  get diagnostic() { return JSON.stringify(this.model); }
}
```

There’s nothing special about this component, nothing form-specific, nothing to distinguish it from any component you've written before.

这个组件没有特别的，没有表单专有的特性，也没有什么可以让它与我们之前写过的组件区别开来的特性。

Understanding this component requires only the Angular concepts covered in previous pages.

理解这个组件只需要我们在之前章节涉及的 Angular 概念。

- The code imports the Angular core library and the `Hero` model you just created.

  导入 Angular 核心库文件 以及我们刚刚创建的 Hero 模型（实体类）。
- The `@Component` selector value of "hero-form" means you can drop this form in a parent template with a `<hero-form>` tag.

  @component` 选择器的值是 ”hero-form" 意味着 我们可以把这表单以 `<hero-form>` 标签的形式放置在一个父模板中。
- The `templateUrl` property points to a separate file for the template HTML.

  `templateUrl` 属性 指向一个单独的 HTML 模板文件。
- You defined dummy data for `model` and `powers`, as befits a demo.

  为了我们为 `model` 和 `powers` 定义了假的数据，适合用来做演示。

Down the road, you can inject a data service to get and save real data or perhaps expose these properties as inputs and outputs (see [Input and output properties](https://angular.io/guide/template-syntax#inputs-outputs) on the [Template Syntax](https://angular.io/guide/template-syntax)page) for binding to a parent component. This is not a concern now and these future changes won't affect the form.

将来，我们可以注入数据服务，来获取、保存真实的数据，或者为了绑定父组件把这些属性作为输入和输出（参见 模板语法一节的输入和输出属性部分） 暴露出去。

- You added a `diagnostic` property to return a JSON representation of the model. It'll help you see what you're doing during development; you've left yourself a cleanup note to discard it later.

  我们添加了一个 `diagnostic` 属性，将这个模型（实体类）以 JSON 格式返回。它能帮我们看到我们在开发中正在做什么。我们还留了一条备注（TODO），提醒之后需要删除掉这个属性。

### 为什么要使用单独的模板文件 Why the separate template file?

Why don't you write the template inline in the component file as you often do elsewhere?

为什么在这个组件文件中我们不像之前那样使用行内模板呢？

There is no "right" answer for all occasions. Inline templates are useful when they are short. Most form templates aren't short. TypeScript and JavaScript files generally aren't the best place to write (or read) large stretches of HTML, and few editors help with files that have a mix of HTML and code.

没有对所有场景都正确的回答。当行内模板很简短时，它是实用的。但大部分模板都不短。 TypeScript 和 JavaScript 文件通常不适合用来写（或者读）大段的 HTML，并且很少编辑器能够协助处理混合了大量 HTML 和代码的文件。

Form templates tend to be large, even when displaying a small number of fields, so it's usually best to put the HTML template in a separate file. You'll write that template file in a moment. First, revise the `app.module.ts` and `app.component.ts` to make use of the new `HeroFormComponent`.

表单模板通常很大，即便是展示一小部分的字段，所以把 HTML 模板放在单独的文件中通常是最好的做法。我们等会将会编写这个模板文件。为了使用新的组件 `HeroFormComponent` ，我们得先修改 `app.module.ts` 和 `app.component.ts` 文件。



## 修改 app.module.ts Revise *app.module.ts*

`app.module.ts` defines the application's root module. In it you identify the external modules you'll use in the application and declare the components that belong to this module, such as the `HeroFormComponent`.

`app.module.ts` 定义了应用的根模块。在根模块中，我们指定了将在这个应用中使用到的外部模块，并且声明了属于这个根模块的组件，比如 `HeroFormComponent` 。

Because template-driven forms are in their own module, you need to add the `FormsModule` to the array of `imports` for the application module before you can use forms.

由于模板驱动式表单在它们自己的模块中，在使用表单之前，我们需要添加 `FormsModule`  到应用模块的 imports 数组中。

Replace the contents of the "QuickStart" version with the following:

src/app/app.module.ts

```typescript
import { NgModule }  from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { FormsModule } from '@angular/forms'; 
import { AppComponent } from './app.component';
import { HeroFormComponent } from './hero-form.component'; 

@NgModule({  
	imports: [BrowserModule, FormsModule],
	declarations: [AppComponent, HeroFormComponent],  
	bootstrap: [AppComponent]
})

export class AppModule { }

```

There are three changes:

有以下3处修改：

1. You import `FormsModule` and the new `HeroFormComponent`. 

   导入 `FormsModule` 和 `HeroFormComponent` 。 

2. You add the `FormsModule` to the list of `imports` defined in the `@NgModule` decorator. This gives the application access to all of the template-driven forms features, including `ngModel`. 

   把`FormsModule` 添加到 `@NgModule`  装饰器 里的 `imports` 列表中。这将让应用能够使用所有模板驱动式表单的功能，包括 `ngModel` 。

3. You add the `HeroFormComponent` to the list of `declarations` defined in the `@NgModule` decorator. This makes the `HeroFormComponent` component visible throughout this module.

   把`HeroFormComponent` 添加到 `@NgModule`  装饰器 里的 `declarations` 列表中。这能让 组件 `HeroFormComponent`  在这个模块中都是可见的。

​If a component, directive, or pipe belongs to a module in the `imports` array, *don't* re-declare it in the `declarations` array. If you wrote it and it should belong to this module, *do* declare it in the `declarations` array.

如果一个组件、指令或者管道从属于 `imports` 数组中的一个模块，则不需要在 `declarations`  数组中重复声明。如果是我们自己编写且从属于这个模块的组件、指令或者管道，则需要在 `declarations`  数组中声明。

##  修改 app.component.ts  Revise *app.component.ts*

`AppComponent` is the application's root component. It will host the new `HeroFormComponent`.

`AppComponent` 是应用的根组件。它将存放新的 `HeroFormComponent` 组件。

Replace the contents of the "QuickStart" version with the following:

用以下内容替换 "快速开始" 版本的 app.component.ts 文件的内容：

src/app/app.component.ts

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'my-app',
  template: '<hero-form></hero-form>'
})
export class AppComponent { }
```

There are only two changes. The `template` is simply the new element tag identified by the component's `selector` property. This displays the hero form when the application component is loaded. You've also dropped the `name` field from the class body.

只有两处改动。`template` （的属性`<hero-form>` ）是个新的元素标签，由组件的 `selector` 属性指定。

当应用的组件加载后，它会展示 这个 hero 表单。

## [**](https://angular.io/guide/forms#create-an-initial-html-form-template)Create an initial HTML form template

Create the template file with the following contents:

src/app/hero-form.component.html

```
content_copy<div class="container">    <h1>Hero Form</h1>    <form>      <div class="form-group">        <label for="name">Name</label>        <input type="text" class="form-control" id="name" required>      </div>       <div class="form-group">        <label for="alterEgo">Alter Ego</label>        <input type="text" class="form-control" id="alterEgo">      </div>       <button type="submit" class="btn btn-success">Submit</button>     </form></div>
```

The language is simply HTML5. You're presenting two of the `Hero` fields, `name` and `alterEgo`, and opening them up for user input in input boxes.

The *Name* `` control has the HTML5 `required` attribute; the *Alter Ego* `` control does not because `alterEgo` is optional.

You added a *Submit* button at the bottom with some classes on it for styling.

*You're not using Angular yet*. There are no bindings or extra directives, just layout.

In template driven forms, if you've imported `FormsModule`, you don't have to do anything to the `` tag in order to make use of `FormsModule`. Continue on to see how this works.

The `container`, `form-group`, `form-control`, and `btn` classes come from [Twitter Bootstrap](http://getbootstrap.com/css/). These classes are purely cosmetic. Bootstrap gives the form a little style.

ANGULAR FORMS DON'T REQUIRE A STYLE LIBRARY

Angular makes no use of the `container`, `form-group`, `form-control`, and `btn` classes or the styles of any external library. Angular apps can use any CSS library or none at all.

To add the stylesheet, open `index.html` and add the following link to the ``:

src/index.html (bootstrap)

```
content_copy<link rel="stylesheet"
      href="https://unpkg.com/bootstrap@3.3.7/dist/css/bootstrap.min.css">
```

## [**](https://angular.io/guide/forms#add-powers-with-ngfor)Add powers with **ngFor*

The hero must choose one superpower from a fixed list of agency-approved powers. You maintain that list internally (in `HeroFormComponent`).

You'll add a `select` to the form and bind the options to the `powers` list using `ngFor`, a technique seen previously in the [Displaying Data](https://angular.io/guide/displaying-data) page.

Add the following HTML *immediately below* the *Alter Ego* group:

src/app/hero-form.component.html (powers)

```
content_copy<div class="form-group">
  <label for="power">Hero Power</label>
  <select class="form-control" id="power" required>
    <option *ngFor="let pow of powers" [value]="pow">{{pow}}</option>
  </select>
</div>
```

This code repeats the `` tag for each power in the list of powers. The `pow` template input variable is a different power in each iteration; you display its name using the interpolation syntax.

## [**](https://angular.io/guide/forms#two-way-data-binding-with-ngmodel)Two-way data binding with *ngModel*

Running the app right now would be disappointing.

![Early form with no binding](https://angular.io/generated/images/guide/forms/hero-form-3.png)

You don't see hero data because you're not binding to the `Hero` yet. You know how to do that from earlier pages. [Displaying Data](https://angular.io/guide/displaying-data) teaches property binding. [User Input](https://angular.io/guide/user-input) shows how to listen for DOM events with an event binding and how to update a component property with the displayed value.

Now you need to display, listen, and extract at the same time.

You could use the techniques you already know, but instead you'll use the new `[(ngModel)]` syntax, which makes binding the form to the model easy.

Find the `` tag for *Name* and update it like this:

src/app/hero-form.component.html (excerpt)

```
content_copy<input type="text" class="form-control" id="name"
       required
       [(ngModel)]="model.name" name="name">
TODO: remove this: {{model.name}}
```

You added a diagnostic interpolation after the input tag so you can see what you're doing. You left yourself a note to throw it away when you're done.

Focus on the binding syntax: `[(ngModel)]="..."`.

You need one more addition to display the data. Declare a template variable for the form. Update the `` tag with `#heroForm="ngForm"` as follows:

src/app/hero-form.component.html (excerpt)

```
content_copy<form #heroForm="ngForm">
```

The variable `heroForm` is now a reference to the `NgForm` directive that governs the form as a whole.

### [**](https://angular.io/guide/forms#the-ngform-directive)The *NgForm* directive

What `NgForm` directive? You didn't add an [NgForm](https://angular.io/api/forms/NgForm) directive.

Angular did. Angular automatically creates and attaches an `NgForm` directive to the `` tag.

The `NgForm` directive supplements the `form` element with additional features. It holds the controls you created for the elements with an `ngModel` directive and `name` attribute, and monitors their properties, including their validity. It also has its own `valid` property which is true only *if every contained control* is valid.

If you ran the app now and started typing in the *Name* input box, adding and deleting characters, you'd see them appear and disappear from the interpolated text. At some point it might look like this:

![ngModel in action](https://angular.io/generated/images/guide/forms/ng-model-in-action.png)

The diagnostic is evidence that values really are flowing from the input box to the model and back again.

That's *two-way data binding*. For more information, see [Two-way binding with NgModel](https://angular.io/guide/template-syntax#ngModel) on the the [Template Syntax](https://angular.io/guide/template-syntax) page.

Notice that you also added a `name` attribute to the `` tag and set it to "name", which makes sense for the hero's name. Any unique value will do, but using a descriptive name is helpful. Defining a `name` attribute is a requirement when using `[(ngModel)]` in combination with a form.

Internally, Angular creates `FormControl` instances and registers them with an `NgForm` directive that Angular attached to the `` tag. Each `FormControl` is registered under the name you assigned to the `name` attribute. Read more in the previous section, [The NgForm directive](https://angular.io/guide/forms#ngForm).

Add similar `[(ngModel)]` bindings and `name` attributes to *Alter Ego* and *Hero Power*. You'll ditch the input box binding message and add a new binding (at the top) to the component's `diagnostic` property. Then you can confirm that two-way data binding works *for the entire hero model*.

After revision, the core of the form should look like this:

src/app/hero-form.component.html (excerpt)

```
content_copy{{diagnostic}}
<div class="form-group">
  <label for="name">Name</label>
  <input type="text" class="form-control" id="name"
         required
         [(ngModel)]="model.name" name="name">
</div>

<div class="form-group">
  <label for="alterEgo">Alter Ego</label>
  <input type="text"  class="form-control" id="alterEgo"
         [(ngModel)]="model.alterEgo" name="alterEgo">
</div>

<div class="form-group">
  <label for="power">Hero Power</label>
  <select class="form-control"  id="power"
          required
          [(ngModel)]="model.power" name="power">
    <option *ngFor="let pow of powers" [value]="pow">{{pow}}</option>
  </select>
</div>
```

- Each input element has an `id` property that is used by the `label` element's `for` attribute to match the label to its input control.
- Each input element has a `name` property that is required by Angular forms to register the control with the form.

If you run the app now and change every hero model property, the form might display like this:

![ngModel in action](https://angular.io/generated/images/guide/forms/ng-model-in-action-2.png)

The diagnostic near the top of the form confirms that all of your changes are reflected in the model.

*Delete* the `{{diagnostic}}` binding at the top as it has served its purpose.

## [**](https://angular.io/guide/forms#track-control-state-and-validity-with-ngmodel)Track control state and validity with *ngModel*

Using `ngModel` in a form gives you more than just two-way data binding. It also tells you if the user touched the control, if the value changed, or if the value became invalid.

The *NgModel* directive doesn't just track state; it updates the control with special Angular CSS classes that reflect the state. You can leverage those class names to change the appearance of the control.

| State                            | Class if true | Class if false |
| -------------------------------- | ------------- | -------------- |
| The control has been visited.    | `ng-touched`  | `ng-untouched` |
| The control's value has changed. | `ng-dirty`    | `ng-pristine`  |
| The control's value is valid.    | `ng-valid`    | `ng-invalid`   |

Temporarily add a [template reference variable](https://angular.io/guide/template-syntax#ref-vars) named `spy` to the *Name* `` tag and use it to display the input's CSS classes.

src/app/hero-form.component.html (excerpt)

```
content_copy<input type="text" class="form-control" id="name"
  required
  [(ngModel)]="model.name" name="name"
  #spy>
<br>TODO: remove this: {{spy.className}}
```

Now run the app and look at the *Name* input box. Follow these steps *precisely*:

1. Look but don't touch.
2. Click inside the name box, then click outside it.
3. Add slashes to the end of the name.
4. Erase the name.

The actions and effects are as follows:

![Control State Transition](https://angular.io/generated/images/guide/forms/control-state-transitions-anim.gif)

You should see the following transitions and class names:

![Control state transitions](https://angular.io/generated/images/guide/forms/ng-control-class-changes.png)

The `ng-valid`/`ng-invalid` pair is the most interesting, because you want to send a strong visual signal when the values are invalid. You also want to mark required fields. To create such visual feedback, add definitions for the `ng-*` CSS classes.

*Delete* the `#spy` template reference variable and the `TODO` as they have served their purpose.

## [**](https://angular.io/guide/forms#add-custom-css-for-visual-feedback)Add custom CSS for visual feedback

You can mark required fields and invalid data at the same time with a colored bar on the left of the input box:

![Invalid Form](https://angular.io/generated/images/guide/forms/validity-required-indicator.png)

You achieve this effect by adding these class definitions to a new `forms.css` file that you add to the project as a sibling to `index.html`:

src/forms.css

```
content_copy.ng-valid[required], .ng-valid.required  {
  border-left: 5px solid #42A948; /* green */
}

.ng-invalid:not(form)  {
  border-left: 5px solid #a94442; /* red */
}
```

Update the `` of `index.html` to include this style sheet:

src/index.html (styles)

```
content_copy<link rel="stylesheet" href="styles.css">
<link rel="stylesheet" href="forms.css">
```

## [**](https://angular.io/guide/forms#show-and-hide-validation-error-messages)Show and hide validation error messages

You can improve the form. The *Name* input box is required and clearing it turns the bar red. That says something is wrong but the user doesn't know *what* is wrong or what to do about it. Leverage the control's state to reveal a helpful message.

When the user deletes the name, the form should look like this:

![Name required](https://angular.io/generated/images/guide/forms/name-required-error.png)

To achieve this effect, extend the `` tag with the following:

- A [template reference variable](https://angular.io/guide/template-syntax#ref-vars).
- The "*is required*" message in a nearby ``, which you'll display only if the control is invalid.

Here's an example of an error message added to the *name* input box:

src/app/hero-form.component.html (excerpt)

```
content_copy<label for="name">Name</label>
<input type="text" class="form-control" id="name"
       required
       [(ngModel)]="model.name" name="name"
       #name="ngModel">
<div [hidden]="name.valid || name.pristine"
     class="alert alert-danger">
  Name is required
</div>
```

You need a template reference variable to access the input box's Angular control from within the template. Here you created a variable called `name` and gave it the value "ngModel".

Why "ngModel"? A directive's [exportAs](https://angular.io/api/core/Directive) property tells Angular how to link the reference variable to the directive. You set `name` to `ngModel` because the `ngModel` directive's `exportAs` property happens to be "ngModel".

You control visibility of the name error message by binding properties of the `name` control to the message `` element's `hidden` property.

src/app/hero-form.component.html (hidden-error-msg)

```
content_copy<div [hidden]="name.valid || name.pristine"
     class="alert alert-danger">
```

In this example, you hide the message when the control is valid or pristine; "pristine" means the user hasn't changed the value since it was displayed in this form.

This user experience is the developer's choice. Some developers want the message to display at all times. If you ignore the `pristine` state, you would hide the message only when the value is valid. If you arrive in this component with a new (blank) hero or an invalid hero, you'll see the error message immediately, before you've done anything.

Some developers want the message to display only when the user makes an invalid change. Hiding the message while the control is "pristine" achieves that goal. You'll see the significance of this choice when you add a new hero to the form.

The hero *Alter Ego* is optional so you can leave that be.

Hero *Power* selection is required. You can add the same kind of error handling to the `` if you want, but it's not imperative because the selection box already constrains the power to valid values.

Now you'll add a new hero in this form. Place a *New Hero* button at the bottom of the form and bind its click event to a `newHero` component method.

src/app/hero-form.component.html (New Hero button)

```
content_copy<button type="button" class="btn btn-default" (click)="newHero()">New Hero</button>
```

src/app/hero-form.component.ts (New Hero method)

```
content_copynewHero() {
  this.model = new Hero(42, '', '');
}
```

Run the application again, click the *New Hero* button, and the form clears. The *required* bars to the left of the input box are red, indicating invalid `name` and `power` properties. That's understandable as these are required fields. The error messages are hidden because the form is pristine; you haven't changed anything yet.

Enter a name and click *New Hero* again. The app displays a *Name is required* error message. You don't want error messages when you create a new (empty) hero. Why are you getting one now?

Inspecting the element in the browser tools reveals that the *name* input box is *no longer pristine*. The form remembers that you entered a name before clicking *New Hero*. Replacing the hero object *did not restore the pristine state* of the form controls.

You have to clear all of the flags imperatively, which you can do by calling the form's `reset()` method after calling the `newHero()` method.

src/app/hero-form.component.html (Reset the form)

```
content_copy<button type="button" class="btn btn-default" (click)="newHero(); heroForm.reset()">New Hero</button>
```

Now clicking "New Hero" resets both the form and its control flags.

## [**](https://angular.io/guide/forms#submit-the-form-with-ngsubmit)Submit the form with *ngSubmit*

The user should be able to submit this form after filling it in. The *Submit* button at the bottom of the form does nothing on its own, but it will trigger a form submit because of its type (`type="submit"`).

A "form submit" is useless at the moment. To make it useful, bind the form's `ngSubmit` event property to the hero form component's `onSubmit()` method:

src/app/hero-form.component.html (ngSubmit)

```
content_copy<form (ngSubmit)="onSubmit()" #heroForm="ngForm">
```

You'd already defined a template reference variable, `#heroForm`, and initialized it with the value "ngForm". Now, use that variable to access the form with the Submit button.

You'll bind the form's overall validity via the `heroForm` variable to the button's `disabled` property using an event binding. Here's the code:

src/app/hero-form.component.html (submit-button)

```
content_copy<button type="submit" class="btn btn-success" [disabled]="!heroForm.form.valid">Submit</button>
```

If you run the application now, you find that the button is enabled—although it doesn't do anything useful yet.

Now if you delete the Name, you violate the "required" rule, which is duly noted in the error message. The *Submit* button is also disabled.

Not impressed? Think about it for a moment. What would you have to do to wire the button's enable/disabled state to the form's validity without Angular's help?

For you, it was as simple as this:

1. Define a template reference variable on the (enhanced) form element.
2. Refer to that variable in a button many lines away.

## [**](https://angular.io/guide/forms#toggle-two-form-regions-extra-credit)Toggle two form regions (extra credit)

Submitting the form isn't terribly dramatic at the moment.

An unsurprising observation for a demo. To be honest, jazzing it up won't teach you anything new about forms. But this is an opportunity to exercise some of your newly won binding skills. If you aren't interested, skip to this page's conclusion.

For a more strikingly visual effect, hide the data entry area and display something else.

Wrap the form in a `` and bind its `hidden` property to the `HeroFormComponent.submitted` property.

src/app/hero-form.component.html (excerpt)

```
content_copy<div [hidden]="submitted">
  <h1>Hero Form</h1>
  <form (ngSubmit)="onSubmit()" #heroForm="ngForm">

     <!-- ... all of the form ... -->

  </form>
</div>
```

The main form is visible from the start because the `submitted` property is false until you submit the form, as this fragment from the `HeroFormComponent` shows:

src/app/hero-form.component.ts (submitted)

```
content_copysubmitted = false;

onSubmit() { this.submitted = true; }
```

When you click the *Submit* button, the `submitted` flag becomes true and the form disappears as planned.

Now the app needs to show something else while the form is in the submitted state. Add the following HTML below the `` wrapper you just wrote:

src/app/hero-form.component.html (excerpt)

```
content_copy<div [hidden]="!submitted">
  <h2>You submitted the following:</h2>
  <div class="row">
    <div class="col-xs-3">Name</div>
    <div class="col-xs-9  pull-left">{{ model.name }}</div>
  </div>
  <div class="row">
    <div class="col-xs-3">Alter Ego</div>
    <div class="col-xs-9 pull-left">{{ model.alterEgo }}</div>
  </div>
  <div class="row">
    <div class="col-xs-3">Power</div>
    <div class="col-xs-9 pull-left">{{ model.power }}</div>
  </div>
  <br>
  <button class="btn btn-primary" (click)="submitted=false">Edit</button>
</div>
```

There's the hero again, displayed read-only with interpolation bindings. This `` appears only while the component is in the submitted state.

The HTML includes an *Edit* button whose click event is bound to an expression that clears the `submitted` flag.

When you click the *Edit* button, this block disappears and the editable form reappears.

## [**](https://angular.io/guide/forms#conclusion)Conclusion

The Angular form discussed in this page takes advantage of the following framework features to provide support for data modification, validation, and more:

- An Angular HTML form template.
- A form component class with a `@Component` decorator.
- Handling form submission by binding to the `NgForm.ngSubmit` event property.
- Template-reference variables such as `#heroForm` and `#name`.
- `[(ngModel)]` syntax for two-way data binding.
- The use of `name` attributes for validation and form-element change tracking.
- The reference variable’s `valid` property on input controls to check if a control is valid and show/hide error messages.
- Controlling the *Submit* button's enabled state by binding to `NgForm` validity.
- Custom CSS classes that provide visual feedback to users about invalid controls.

The final project folder structure should look like this:

angular-forms

src

app

app.component.ts

app.module.ts

hero.ts

hero-form.component.html

hero-form.component.ts

main.ts

tsconfig.json

index.html

node_modules ...

package.json

Here’s the code for the final version of the application:

hero-form.component.tshero-form.component.htmlhero.tsapp.module.tsapp.component.tsmain.tsindex.htmlforms.css`content_copyimport { Component } from '@angular/core'; import { Hero }    from './hero'; @Component({  selector: 'hero-form',  templateUrl: './hero-form.component.html'})export class HeroFormComponent {   powers = ['Really Smart', 'Super Flexible',            'Super Hot', 'Weather Changer'];   model = new Hero(18, 'Dr IQ', this.powers[0], 'Chuck Overstreet');   submitted = false;   onSubmit() { this.submitted = true; }   newHero() {    this.model = new Hero(42, '', '');  }}`