.. _dependency-injection:

Dependency Injection
********************

Dependency Injection simplified
===============================

If you're not aware of what dependency injection is you can `read about it on Wikipedia <http://en.wikipedia.org/wiki/Dependency_injection>`_. But if you do you might get a bit scared off because they make it sound quite complex when it's really actually quite simple. The idea is that rather than hardcoding the other classes your class will use it takes them in via the constructor parameters or a setter method.

For example rather than in your class creating a MySQL connection to talk to the database you can specify that your class needs an instance of something that implements the ``Database`` interface and then it will be up to the code that calls your class to pass in the instance.


How Cohesion performs Dependency Injection
==========================================

In Cohesion we've gone one step further and abstracted away creating the instances anywhere within your code and is instead up to the configuration to supply the information needed to decide how to create instances that implement the interface you need.

So basically you can just say, I need a ``Cache`` library, then in the config you can decide whether to use APC, Memcache, Redis, or whatever. And at any point you can change which type of cache you're using just by updating your configuration. 

The dependency injection relies heavily on `PHP Reflection <http://www.php.net/manual/en/book.reflection.php>`_ to inspect the object you want to create, look at the parameters the constructor is asking for, get the required library based on the config and pass in an instance. This is the job of the Factories.


Creating a Service
------------------

created the Service constructor uses the Data Access Factory to create a DAO for that Service. For example, the UserService should only ever use the UserDAO and shouldn't have access to any other DAOs therefore when you create a UserService it will set the object parameter ``dao`` to an instance of the UserDAO.


Creating DAOs
-------------

DAOs are created using the Data Access Factory. It will inspect the constructor and pass in the accesses it needs. DAOs can reference other DAOs and can specify them in the constructor.

.. code-block:: php

    class UserDAO extends DAO {
        protected $cache;
        protected $locationDao;

        public function __construct(Database $db, Cache $cache, LocationDAO $locationDao) {
            parent::__construct($db);
            $this->cache = $cache;
            $this->locationDao = $locationDao;
        }
        ...
    }


Creating Utilities
------------------

If a utility implement the ``Util`` interface you can create instances of that utility using the Util Factory which will construct that util using the configuration set in the config.

