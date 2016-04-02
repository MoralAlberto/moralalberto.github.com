---
layout: post
title: "MVVM with API example"
excerpt: "An example about Model-View-ViewModel architecture pattern used with a Reactive API."
tags: [MVP, Viper, Binding, MVC, ViewModel, Business Logic, Functional Reactive Programming, API]
image:
  feature: viewmodel.png
---

> **Abstract**: Binding, ViewModel, Business Logic, Functional Reactive Programming, RAC, API

MVVM is another architecture pattern like **MVP**, **Viper** or **MVC**.
<br/>
<br/>
With this "new" architecture **it’s more easy to test**, and reduces the complexity of our View Controllers. **Because, all the business and presentation logic** are moved out to our new View Model class, and also, **we don't care about the lifecycle of the View Controller**. So, all the Massive View Controller are split in two classes (the View Controller and the View model).
<br/>
<br/>
Also mention, that with this architecture it's needed to bind the View Model with the View Controller. For example, when we need to make REST API calls to our server, instead of making this call in our View Controller, we move out this business logic to our View Model. **The View Model handle all the logic, from the request initialization to parse the JSON in a custom object model. When the JSON is correctly parsed, It must be a way to notify our View Controller with the new data just retrieved and update our View with the new information.**
<br/>
<br/>
There are many ways to bind the View Controller with the View Model: you can use delegate pattern, completion blocks or my favourite **Reactive Cocoa**. A few days a go, I created a demo app called [AwesomeHeroes](https://github.com/MoralAlberto/AwesomeHeroes). This demonstration app has the repositories section where is placed all the API stuff. A brief summary, about the most important point within AwesomeHeroes are:
<br/>
<br/>
· MVVM pattern
<br/>
· Functional Reactive Programming (RAC4)
<br/>
· Autolayout
<br/>
· Manual layout (with NSLayoutAnchor and NSLayoutConstraints)
<br/>
· Custom UICollectionViewLayout
<br/>
· Tests (with XCTest, Quick and Nimble)
<br/>
· IBDesignable and IBInspectionable
<br/>
· Carthage and Cocoapods
<br/>
· Dynamic cells
<br/>
· Localization
<br/>
· Separate code in different layers
<br/>
· Code coverage
<br/>
<br/>
AwesomeHeroes app has an API module that works with [Reactive Cocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) and [ObjectMapper](https://github.com/Hearst-DD/ObjectMapper). The API repository architecture is split in different logic parts: the managers, the network stuff, and the parser model. This is a way to create a REST API with one goal: Easy to maintain. You can add more targets, managers or models in an easy way.
<br/>
<br/>
The demo retrieve heroes from Marvel API subscribing to a **cold signal (SignalProducer)** and **parsing the JSON to the correct model ready to be displayed/used in your controller/view**. BUT, all this business logic is done within the API module. But maybe anytime soon I will explain how the api module is distributed.
<br/>
<br/>
I'm gonna explain a brief example about binding our View Controller and the View Model. In our View Controller we need a reference to our View Model. Then, when our view appear, we can sent our request to our View Model. Our View Model handle the request and when he has been finished, then and only then, he notifies the View Controller to refresh the View with the new data.

{% gist MoralAlberto/a4a147d77db29a998c0f8151e0d998b3 %}

When the View appears, we bind our View Model with the View Controller. More specifically a variable called **canRealoadUI**. when **canReloadUI** is changed to TRUE, the View Controller refresh all his content. In this case, he reloads all the new heroes retrieved from Marvel API.
<br/>
<br/>
Directly after binding we are gonna make the API call, and for that, we call a method in charge of handling the API call within our View Model.

{% gist MoralAlberto/f43fe1840d5c030373f88ab914ca96bc %}

The above code shows, how the view model manage the result received from the API repo. When correct data flows in this Signal, we change the value of **canReloadUI**. Then, the View is notified an can reload all his content.
