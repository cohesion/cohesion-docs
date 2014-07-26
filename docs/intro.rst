Introduction
************

Origins
=======

Cohesion started from me deciding to start a new project in PHP. I didn't have much experience with many PHP frameworks so rather than looking through all the existing frameworks and picking the one that seemed to make the most sense to me, I decided to list all the 'features' I wanted in a framework before trying to find one suitable.

Coming from a long history of programming in a myriad of languages, I realised that one of my main requirements was to have a strong code structure to provide a strong backbone of the application so that it's very easy to maintain in the future. I have managed large teams of software engineers maintaining large code bases and I know how painful it can be to add new features or change existing ones when every engineer decides to implement his own structure.

In going through all the existing frameworks I found that none of them really gave me what I wanted. Probably the closest ones would be `Symfony <http://symfony.com/>`_ and `Laravel <http://laravel.com/>`_ but still they didn't really give me everything I needed. So I set out to develop a simple framework that will give me everything I need and will help make sure that I will be able to continue to easily add more features in the future even when my project has grown to hundreds of classes and will be able to easily go in and optimize the data access etc without having to worry about effecting any business logic etc.

One of the issues I have with many of the other frameworks is that they provide a good foundation for developing "anything" but then let the developers do whatever they want on top of it. They pride themselves as being extremely customizable and letting users choose how they want to use it. While that's fantastic for many people, I want something that will provide more than a foundation, I don't want other people to "use it how ever they want". In going with this theme I've tightened "how" Cohesion can be used down a bit and don't have as many "options".


Philosophy
==========

Cohesion is for people who envision their product continuing to grow and place a high value on maintainability and low `technical debt <http://en.wikipedia.org/wiki/Technical_debt>`_.

Cohesion tries to bring many of the well established development "best practices" into the main development pipeline for PHP projects.

I'm sure Cohesion won't be for everyone, as most people are happy with something they can quickly hack a website on top of and continue just quickly hacking stuff on top.

It provides the framework for you to create a service based architecture for your applications.

Principles
----------

`High Cohesion <http://en.wikipedia.org/wiki/Cohesion_(computer_science)>`_
    High Cohesion is when objects are appropriately focused, manageable and understandable. It basically means that everything within the same component should be strongly related to each other.

`Low Coupling <http://en.wikipedia.org/wiki/Loose_coupling>`_
    A loosely coupled system is one in which each of its components has, or makes use of, little or no knowledge of the definitions of other separate components. Not relying on the implementation of other components means that if any individual component should change then it shouldn't effect any other component.

`Single Responsibility <http://en.wikipedia.org/wiki/Single_responsibility_principle>`_
    The single responsibility principle states that every context (class, function, variable, etc.) should have a single responsibility, and that responsibility should be entirely encapsulated by the context. All its services should be narrowly aligned with that responsibility.

`Dependency Inversion <http://en.wikipedia.org/wiki/Dependency_injection>`_
    Components should "use" abstractions of other libraries / utilities / modules. Then the concrete implementation can be passed to the object as run time. This allows for the interchanging of implementations without having to deal with modifying other components that "use" the class.

`Controller <http://en.wikipedia.org/wiki/Model-view-controller>`_
    The Controller is a component often referenced as a part of MVC frameworks. It's role is to separate the Business Logic and the Presentation Logic.

`Creator <http://en.wikipedia.org/wiki/Factory_pattern>`_
    The factory pattern allows for a specific class that is responsible for creating new instances of objects. This allows for the code to delegate creating of instances meaning that each module doesn't need to know "how" to create an instance of that object.

