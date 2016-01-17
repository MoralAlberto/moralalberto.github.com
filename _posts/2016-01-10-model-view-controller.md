---
layout: post
title: "Architectural Pattern: Apple MVC's variation"
excerpt: "Disadvantages using the variation of the Model View Controller that Apple recommends in their guidelines..."
tags: [Architectural, Pattern, Business Logic, Test, Decoupled, Controller, View, Model]
image:
  feature: city.png
---
> **Abstract**: Architectural Pattern, MVC, Test, Decoupled, Controller, View, Model

The traditional MVC design pattern **is a common way to organize code**, there're three roles: model, view and controller. So all the **entities are decoupled** with their role, and a way to communicate with each other, **BUT** Apple uses a [variation of MVC](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html#//apple_ref/doc/uid/TP40010810-CH14-SW14). Using this pattern, only creates a _mess_ in our code, because the **View is totally tied with the Controller**.
<br/><br/>
The **View** and the **Controller** are in the same file. The **UIViewController** is composed from the **View** and the **Controller** 😁, all the view's life cycle is there: viewDidLoad, viewWillAppear... and all the business logic. So, we finish creating a **Massive View Controllers** with a lot of responsibilities. Responsible of the datasource, delegates, interacting with our repositories like: API REST, Core Data, Socket etc.

<br/>
I quit using the basic architectural pattern that apple uses in their guidelines, I used to, until I see better pattern designs, I'll gonna explain it during the next weeks. **This post only covers the MVC that apple uses in their guidelines**.

<br/>
With the apple's MVC It's hard to see the [Single Responsible Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle), it's hard to test, it's hard to reuse code. We want that the **Model**, the **View** and the **Controller**, to be decoupled, It means that, we want one entity unknown about the others, in this way **we can reuse code**. (this would be the perfect scenario)

<br/>
<h2>Traditional MVC</h2>
<figure>
    <a href="/images/MVC.png"><img src="/images/MVC.png"></a>
</figure>

<br/>
- The view sends user interactions to the controller.<br/>
- The controller is where the **business logic** take place and can update the model or not (or maybe send an API Request to see the result in the View)<br/>
- The controller is the **mediator** between the view and the model.<br/>
- The model notifies to the controller for new changes, and these changes are updated in the View.<br/>

<br/>
**The controller is the least reusable code**, because it have code to interact with the view, and with the model. The view and the model are decoupled. They didn't know about each other, only the controller.

<br/>
But in practice, the next figure show the **MVC** variation that apple uses.

<h2>Variation MVC</h2>
<figure>
    <a href="/images/MVC-2.png"><img src="/images/MVC-2.png"></a>
</figure>

<br/>
The picture shows that the View and the Controller are the same, are tightly coupled (as I mentioned before)



<br/>
In fact who many of you used this code before?


{% gist MoralAlberto/2941884da168524023ef %}

<br/>
The last snippet shows that **the View knows about the Model, so the MVC's rules explained before are totally broken**. The View and the Model must be decoupled, the controller must be the mediator, but apple uses the View and the Controller in the same place (UIViewController), so, all this logic are smashed.
The Controller is the one that only have to know about the View and the Model, so **the Controller must configure the Publication Cell and not the View**. This increases our code within the ViewController, tending to have a massive view controller.

<br/>
With this bad distribution, creating test for that massive class will be tedious, and also creating mocks for the view. I'll explain MVVM and how easy it is test in that kind of architecture pattern (with [Nimble](https://github.com/Quick/Nimble) and [Quick](https://github.com/Quick/Quick)).

<br/>
<h2>Summary</h2>
- **Separate de UI from the business logic.**
- **The View and Controller are coupled.**
- **Bad testability.**

<br/>
<br/>