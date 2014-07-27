Code Structure
**************

Cohesion lays out the ground work for an extended MVC set up. I say extended because it goes one step further at separation of responsibilities by splitting the 'Model' into the **Business Logic**, **Object Data** and **Data Access Logic**.

For each "object" that you will use in your application you should create an associated service and data access object. For example our ``User`` object will just be a dumb object and all associated business logic should be done within the ``UserService``. The ``UserService`` will then use it's ``DAO`` to access and store related user data.

If you are developing an application with many module you can create a service hierarchy where the high level services will be the high level business logic for the module and it will make use of the individual object services for the object specific business logic.

Nothing within any Service, DTO, or DAO should have any idea about the environment in which it's being used. This means that we should be able to reuse the same services for web application, command line scripts, and anything else that may need to reuse the same business logic such as mobile application back ends etc.


Services - Business Logic
=========================

Services contain all the business logic and only the business logic for a specific object. I've called the business logic section 'Services' because I want people to think of them as an independent Service for one portion of the application. The service will contain all the authorisations and validations before carrying out an operation. Services are the entry point for the DAOs, where only the UserService accesses the UserDAO. The UserService can be thought of as the internal API for everything to do with Users.


Data Transfer Objects (DTOs) - Object Data
==========================================

DTOs are simple instance objects that contain the data about a specific object and don't contain any business logic or data access logic. DTOs have accessors and mutators (getter and setters) and *can* contain minimal validation in the mutators.


Data Access Objects (DAOs) - Data Access Logic
==============================================

DAOs contain all the logic to store and retrieve objects and data from external data sources such as RDBMSs, non relational data stores, cache, etc. The DAO does not contain any business logic and will simply perform the operations given to it, therefore it's extremely important that all accesses to DAOs come from the correlating Service so that can perform the necessary checks before performing the storage or retrieval operations. The Service doesn't care how the DAO stores or retrieves the data only that it does. If later down the line a system is needed to be converted from using MySQL to MongoDB the only code that should ever need to be touched would be to within the DAOs.


Controllers
===========

The Controllers are the external facing code that access the input variables and returns the output of the relevant view. The Controller handles the authentication, accesses the relevant Service(s) then constructs the relevant view. It doesn't contain any business logic including authorisation.


Views
=====

Views take in the required parameters and return a rendering of what the user should see. Views don't perform any logic and don't do any calls to any function to get additional data. All data that the view will need should be provided to the view from the Controller.


