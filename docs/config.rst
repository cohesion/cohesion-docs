Configuration
*************

The configuration is a big part of the backbone of Cohesion. It contains all the information for the :ref:`Dependency Injection <dependency-injection>` and how each utility should be used.

Configuration files are JSON formatted and can include comments. The configuration files are set up in a cascading fashion where loading subsequent configurations will overwrite just the values that are specified in that config and the unspecified configurations are left unchanged.

The default Cohesion configuration is located at ``myproject/config/cohesion-default-conf.json``. It is not recommended to make any changes to this file directly as it may make it harder to resolve any conflicts etc if we update it. Instead you should put all your default configurations in the ``myproject/config/default-conf.json`` file. Remember you don't need to implement all the settings, only add the ones that you want to do differently from the cohesion defaults.


File Structure
==============

The configuration is very well structured and documented so make sure you take the time to read the comments in the cohesion default configuration file about what each setting does.

When constructing various objects and libraries they will be given a sub section of the configuration so it's important to get the structure right.

All objects and libraries will have access to the ``global`` section.

Each Service that you create will get a copy of the ``application`` section. The view and templating library will get the ``view`` section. The database class will use a copy of the ``data_access.database``. And so on and so forth.


Environment specific configurations
===================================

For environment specific configurations such as different database settings, etc for your dev, staging and production environments. These are just examples you can have separate configuration for what ever different environments you might have. Simply create additional configuration files in the form ``{environment}-conf.json``. For example, ``local-conf.json`` or ``production-conf.json``.

Again these are cascaded so you only need to include the settings that are different for that environment and it will get the rest of the settings from your ``default-conf.json`` and the ``cohesion-default-conf.json``.


Configuration Options
=====================


global
------

Global configurations are settings that may effect multiple areas of the code. Global should only be used as a last resort if the configuration doesn't fit in better in any of the other setting sections.
Options provided within the global section will be available to all Configurable objects.


Default global settings
^^^^^^^^^^^^^^^^^^^^^^^

+-----------------------------+---------+--------------------+---------------------------------------------------------------------------------------+
| Key                         | Type    | Default            | Description                                                                           |
+=============================+=========+====================+=======================================================================================+
| global.domain_name          | string  | 'www.example.com'  | Domain name of your application. Used for setting link URLs as well as other places.  |
|                             |         |                    | This will be overwritten by ``$_SERVER['HOST']`` if it's set but it's important to    |
|                             |         |                    | still set this as it will be used in things like sending emails from the command line |
|                             |         |                    | etc that don't have access to the host parameter                                      |
+-----------------------------+---------+--------------------+---------------------------------------------------------------------------------------+
| global.production           | boolean | ``false``          | Whether or not the current system is being used in production. This is used for       |
|                             |         |                    | things such as setting the error output level as well as other business logic         |
|                             |         |                    | determinant on whether it's in production or not.                                     |
+-----------------------------+---------+--------------------+---------------------------------------------------------------------------------------+
| global.autoloader.cache_key | string  | 'autoloader_cache' | Cache key used to cache the class paths for your files.                               |
+-----------------------------+---------+--------------------+---------------------------------------------------------------------------------------+


application
-----------

The application configuration section is what will be passed to your business logic classes. This is where you should be placing your own configurations for your application settings and should contain all your business rule settings.


Default application settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+---------------+--------+-----------+-----------------------------------------------------------------------------------------+
| Key           | Type   | Default   | Description                                                                             |
+===============+========+===========+=========================================================================================+
| class.default | string | `null`    | Default service to use if the ``ServiceFactory::getService()`` function isn't provided  |
|               |        |           | a class name. The default of ``null`` means that you must specify the service as        |
|               |        |           | there's no default.                                                                     |
+---------------+--------+-----------+-----------------------------------------------------------------------------------------+
| class.prefix  | string | ''        | Prefix used in the service class name before the service name. For example if the       |
|               |        |           | prefix was "Application" and you called ``ServiceFactory::getService('User')`` it will  |
|               |        |           | look for a ``ApplicationUser`` class.                                                   |
+---------------+--------+-----------+-----------------------------------------------------------------------------------------+
| class.suffix  | string | 'Service' | Suffix used in the service class name after the service name. For example with the      |
|               |        |           | default suffix of "Service" when you call ``ServiceFactory::getService('User')`` it     |
|               |        |           | will look for a ``UserService`` class.                                                  |
+---------------+--------+-----------+-----------------------------------------------------------------------------------------+


routing
-------

The routing configuration section is used to decided how to deal with incoming requests and decide which Controller should be used to handle it.

We currently don't provide a way to specify specific routes and how they will be handled. Rather Cohesion works on a simple yet effective default route system.

It will look for the first path segment that is a valid Controller then check if the following path segment is a function of that Controller, if it is then it will call that function with all the following path segments as parameters to that function, if it's not a valid function then all segments after the Controller will be considered parameters to the Controller's index function.

Given the path ``/nothing/something/action/param1/param2?foo=bar`` "nothing" is not a Controller and will be ignored. ``something`` matches the controller ``SomethingController`` (based on the class prefix and suffix) and therefore that Controller will be used. ``action`` is a function of ``SomethingController`` therefore the resulting call based on a request to that URI will be: ``SomethingController->action('param1', 'param2')``. To access the GET and POST parameters you can to use ``$this->input->get('foo')``;

For the home page (route '``/``') the default controller will be used with the default function.


Default routing settings
^^^^^^^^^^^^^^^^^^^^^^^^

+------------------+----------+---------------------------------------+---------------------------------------------------------------------------------------+
| Key              | Type     | Default                               | Description                                                                           |
+==================+==========+=======================================+=======================================================================================+
| class.default    | string   | 'Home'                                | Default controller to use if no other Controller is found in the path.                |
+------------------+----------+---------------------------------------+---------------------------------------------------------------------------------------+
| class.prefix     | string   | ''                                    | Prefix to use when matching the Controller class name with the path.                  |
+------------------+----------+---------------------------------------+---------------------------------------------------------------------------------------+
| class.suffix     | string   | 'Controller'                          | Suffix to use when matching the Controller class name with the path. The default of   |
|                  |          |                                       | "Controller" means that ``something`` will try to match the ``SomethingController``   |
+------------------+----------+---------------------------------------+---------------------------------------------------------------------------------------+
| function.default | string   | 'index'                               | Default function to use within the matched Controller when no other function is found |
|                  |          |                                       | on the path.                                                                          |
+------------------+----------+---------------------------------------+---------------------------------------------------------------------------------------+
| function.prefix  | string   | ''                                    | Prefix to use when matching the Controller function name with the path.               |
+------------------+----------+---------------------------------------+---------------------------------------------------------------------------------------+
| function.suffix  | string   | ''                                    | Suffix to use when matching the Controller function name with the path. For example   |
|                  |          |                                       | if this is set to "Action" and the path had ``view`` then it would look for the       |
|                  |          |                                       | ``viewAction`` function within the Controller.                                        |
+------------------+----------+---------------------------------------+---------------------------------------------------------------------------------------+
| redirects        | mappings |  ``{``                                | Redirects can be used to send a redirect. Each of the keys within the redirects       |
|                  |          |    ``"^/favicon.ico$":``              | configuration are used as regular expressions to be used to match the path. If the    |
|                  |          |      ``"/assets/images/favicon.ico"`` | regex matches the page will be redirected to the value here.                          |
|                  |          |  ``}``                                |                                                                                       |
+------------------+----------+---------------------------------------+---------------------------------------------------------------------------------------+


view
----

The view configuration section defines how the views will be generated.


Default view settings
^^^^^^^^^^^^^^^^^^^^^

+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| Key                      | Type    | Default                           | Description                                                                           |
+==========================+=========+===================================+=======================================================================================+
| class                                                                  | Used to define the class names so that the ViewFactory can create the views based on  |
|                                                                        | just the base name. Eg, 'home' will create a HomeView instance.                       |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| class.default            | string  | ''                                | Default view to use when no view name is given to the ViewFactory. The default of     |
|                          |         |                                   | ``''`` means that it will default using the prefix and suffix. If you don't want it   |
|                          |         |                                   | to use any default set this to `null`.                                                |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| class.prefix             | string  | ''                                | Prefix used in the view class name before the view name. For example if the prefix    |
|                          |         |                                   | was "My" and you called ``ViewFactory::createView('page')`` it will look for a        |
|                          |         |                                   | ``MyPage`` class.                                                                     |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| class.suffix             | string  | 'View'                            | Suffix used in the view class name after the view name. With the default value a call |
|                          |         |                                   | to ``ViewFactory::createView('page')`` will look for the ``PageView`` class.          |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| template.engine          | string  | '\Cohesion\Templating\CohesionMo' | The default templating engine is basically an extended version of Mustache with a     |
|                          |         |                                   | couple of added functionalities. You can change this to use a different templating    |
|                          |         |                                   | engine but the engine must implement the TemplateEngine interface. So if you want to  |
|                          |         |                                   | use a different currently unsupported template engine you can wrap it in a new class  |
|                          |         |                                   | that implements the interface.                                                        |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| template.directory       | string  | 'www/templates'                   | Root template directory containing all your template files.                           |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| template.extension       | string  | 'html'                            | Extension used on your template files.                                                |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| template.default_layout  | string  | 'index'                           | Default template file to use which contains the root layout of all your pages.        |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| template.vars            | mapping | *see file*                        | Set of default variables to be available in every template.                           |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| cache                    | mixed   | ``{ "ttl": 3600 }``               | To cache prerendered templates set either a directory or a ttl. If you set the        |
|                          |         |                                   | directory it will store the prerendered templates on disk within that directory. The  |
|                          |         |                                   | directory can either be a relative path to the base directory or an absolute path     |
|                          |         |                                   | starting from '/'. Make sure the application server has write access to the           |
|                          |         |                                   | directory. If you don't set a directory it will use the default system cache and you  |
|                          |         |                                   | can set the ``ttl`` for the cache keys. Setting ``cache`` to ``false`` will disable   |
|                          |         |                                   | caching although this is not recommended.                                             |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| cdn                                                                    | Using Content Delivery Network will usually increase the speed of viewing your site   |
|                                                                        | as well as drastically reducing server load as all your static assets will be cached  |
|                                                                        | on a third party server that usually have end points all around the globe.            |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| cdn.hosts                | array   | ``[]``                            | Hosts is an array of fully qualified domain names to use. It's recommended that you   |
|                          |         |                                   | use multiple CDN domains as nearly all browsers have a limit of concurrent            |
|                          |         |                                   | connections to the same domain. Adding multiple CDN hosts will allow clients to       |
|                          |         |                                   | download more assets concurrently. If no hosts are set then CDN will be disabled and  |
|                          |         |                                   | all requests will go to the application server.                                       |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| cdn.version                                                            | Versioning is used to invalidate assets when they change. Because the CDN will cache  |
|                                                                        | your static assets they will need a new resource name every time you update your      |
|                                                                        | files, such as updates to JavaScript files etc. It's important that if your CDN asks  |
|                                                                        | you whether or not to include the query string you include it. File versions are      |
|                                                                        | basically just an MD5 check sum of the file. To improve performance the version is    |
|                                                                        | stored in the local cache and is only re-evaluated after the ttl has expired.         |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| cdn.version.cache_prefix | string  | 'asset_version\_'                 | Prefix to use when caching the versions into the local cache.                         |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| cdn.version.ttl          | int     | 300                               | Expiry of the cached version in seconds. `300` is 5 minutes                           |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| formats                  | object  | *see file*                        | These are the accepted formats and the view class that matches them If you want to    |
|                          |         |                                   | use your own default view for HTML you should overwrite it in your configuration. Eg, |
|                          |         |                                   | the format 'plain' will use the PlainView by default.                                 |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+
| mime_types               | object  | *see file*                        | Mappings of various mime types to the format. You probably shouldn't overwrite these  |
|                          |         |                                   | but you can extend this if want to implement other formats.                           |
+--------------------------+---------+-----------------------------------+---------------------------------------------------------------------------------------+


data_access
-----------

The data_access configuration section contains each of the data access types that your application will use. The key should match a data access interface or class. If a driver is specified it will use that class otherwise it will try to use the setting value.

``public SomethingDAO(Database $db)`` will use the database setting and instantiate the driver (MySQL).


default data access settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| Key                     | Type   | Default             | Description                                                                           |
+=========================+========+=====================+=======================================================================================+
| class.default           | string | ``null``            | Default Data Access Object to use when no name is given to the                        |
|                         |        |                     | ``DAOFactory::getDAO()`` function. ``null`` means that you must supply a DAO name     |
|                         |        |                     | whenever calling that function.                                                       |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| class.prefix            | string | ''                  | Prefix for DAO classes.                                                               |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| class.suffix            | string | 'DAO'               | Suffix for DAO classes.                                                               |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database                                               | Database settings.                                                                    |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.driver         | string | 'MySQL'             | Driver class name used to connect to the database.                                    |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.host           | string | 'localhost'         | Database host for the main database connection used for writes.                       |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.port           | int    | 3306                | Database port to use when connecting to the host.                                     |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.user           | string | 'root'              | User used when connecting to the host.                                                |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.password       | string | ''                  | Database password.                                                                    |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.database       | string | 'cohesion'          | Default database used when connected to the server.                                   |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.charset        | string | 'UTF8'              | Default character set to use.                                                         |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.slave                                         | The slave is used in a master-slave database set up. You can override the user,       |
|                                                        | password and database settings here if you want to use different ones for the slaves, |
|                                                        | otherwise it will use the same ones.                                                  |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.slave.hosts    | array  | ``[ 'localhost' ]`` | Array of slave hosts to use for read only statements. If none are set then it will    |
|                         |        |                     | just use the same as the main.                                                        |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.slave.user     | string | *unset*             | Set this if you want to use a different user to connect to the slaves. The same user  |
|                         |        |                     | will be used for all slaves.                                                          |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.slave.password | string | *unset*             | Set this if you want to use a different password to connect to the slaves.            |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| database.slave.database | string | *unset*             | Set this if you want to use a different database on the slaves.                       |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| cache                                                  | Default cache to use for system caching.                                              |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+
| cache.driver            | string | 'APC'               | Class used for system caching.                                                        |
+-------------------------+--------+---------------------+---------------------------------------------------------------------------------------+


object
------

The object configuration section sets up the basic objects. There shouldn't really be any reason to add anything extra in here as objects are just dumb classes that store data.


default object settings
^^^^^^^^^^^^^^^^^^^^^^^

+--------------+--------+---------+---------------------------------------------------------------------------------------+
| Key          | Type   | Default | Description                                                                           |
+==============+========+=========+=======================================================================================+
| class.prefix | string | ''      | Prefix used for DTO classes.                                                          |
+--------------+--------+---------+---------------------------------------------------------------------------------------+
| class.suffix | string | ''      | Suffix used for DTO classes. The default of no prefix or suffix means that the 'user' |
|              |        |         | DTO will be the 'User' class.                                                         |
+--------------+--------+---------+---------------------------------------------------------------------------------------+


utility
-------

The utility configuration section provides the config used by the various utility classes. Each of these sections are optional and only required if you want to use the utility.

