---
layout: post
title: "Architectural Pattern: Apple MVC's variation"
excerpt: "Disadvantages using the variation of the Model View Controller that Apple recommends in their guidelines..."
tags: [Architectural, Pattern, Business Logic, Test, Decoupled, Controller, View, Model]
image:
  feature: city.png
---
> **Abstract**: Architectural Pattern, MVC, Test, Decoupled, Controller, View, Model

The traditional MVC design pattern **is a common way to organize your code*. There're three main  oles: model, view and controller. So, all the **entities are decoupled** with their role, and they have a way to communicate with each other. **However**, Apple uses a [variation of MVC](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html#//apple_ref/doc/uid/TP40010810-CH14-SW14). By using the Apple's MVC pattern, you only create a **Massive View Controller**, because the **View is totally tied with the Controller**.
<br/><br/>
The **View** and the **Controller** are in the same file. The **UIViewController** is composed from the **View** and the **Controller** 😁, all the view's life cycle is there: viewDidLoad, viewWillAppear... and all the business logic. So, we end up creating a **Massive View Controllers** with many responsibilities: Responsibility for the data source, delegates, interaction with our API REST, Core Data, Socket or any other repository.

<br/>
That's why I stopped using the basic architectural pattern that Apple uses in their guidelines. I used to using it, until I saw better pattern designs. I'm gonna talk about them during the next weeks. **This post only covers the MVC that Apple manages in their guidelines**.

<br/>
With the Apple's MVC it's hard to see the [Single Responsible Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle), hard to test the code (it's easier to test it with other architecture patterns such as MVVM or Viper) and hard to reuse the code. We want the **Model**, the **View** and the **Controller** to be decoupled. That means that we don't one entity to know about the other ones. This way, **we can reuse the code** (this would be the perfect scenario)

<br/>
<h2>Cocoa MVC</h2>
<figure>
    <a href="/images/MVC.png"><img src="/images/MVC.png"></a>
</figure>

<br/>
- The view sends interactions to the controller.<br/>
- The controller is the class, in which the **business logic** takes place and have the possibility of updating the model or not (or it can also send an API Request to see the result in the View)<br/>
- The controller is the **mediator** between the view and the model.<br/>
- The model notifies the controller the new changes, and these changes are updated in the View.<br/>

<br/>
**The controller is the least reusable code**, because it provides code to interact with both the view and model. The view and the model are decoupled. They don't know about each other, only about the controller.

<br/>
**But this is only in theory. In practice**, the next figure shows the **MVC** variation that Apple uses in their guidelines.

<h2>Realistic Cocoa MVC</h2>
<figure>
    <a href="/images/MVC-2.png"><img src="/images/MVC-2.png"></a>
</figure>

<br/>
The picture illustrates that the View and the Controller are the same, are tightly coupled (as I mentioned before)

<br/>
In fact who many of you have used this code before?


{% gist MoralAlberto/2941884da168524023ef %}

<br/>
The last snippet shows the knowledge from View towards the Model, **so the MVC's rules explained before are totally broken**. The View and the Model must be decoupled, the controller must be the mediator, but Apple uses the View and the Controller in the same class (UIViewController), so, all this logic are smashed.
The Controller is the only one that knows about the View and the Model, so **the Controller must configure the Publication Cell and not the View**. This increases our code within the ViewController, tending to have a Massive View Controller.

<br/>
With this bad distribution, creating tests for that massive class and mocks for the view will be tedious. In further blog posts,  I'll explain more about MVVM and how easy it is testing in that kind of architecture pattern. (with [Nimble](https://github.com/Quick/Nimble) and [Quick](https://github.com/Quick/Quick)).

<br/>
<h2>Summary</h2>
- **Separate de UI from the business logic.**
- **The View and Controller are totally tied in UIViewController.**
- **Bad testability.**

<br/>
<br/>
