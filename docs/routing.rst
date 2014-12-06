Routing
*******

Route matching
==============

The current routing doesn't get specified but instead in inferred by the path, class and function names of the Controllers.

Based on ``/{directory}/../{controller}/{function}/{var}``. For example ``/user/list/all`` would call ``UserController->list('all')``.

hyphens (``-``) in the path are used as word separators so that it can match the correct capitalisation of controller and function names. ``/contact-us`` will match ``ContactUsController``.


Using defaults
--------------

It will also accept variations that use the default values. It will first try to match a controller, if it doesn't match a controller then it will try to match a function on the default controller. Also if the function is omitted it will use the default function of the selected controller. The defaults can be configured in the config.

By default ``/`` will match the default controller's default function, ``HomeController->index()``.

``/about`` will first look for an ``AboutController`` if it exists it will use the default ``index()`` function on that controller. If that controller doesn't exist then it will try to use the ``about()`` function on the default ``HomeController``. If that function doesn't exist it will throw a ``NotFound`` error.


Parameters
----------

The function must be able to accept the number of parameters on the path otherwise it will throw a ``NotFound`` error. If the parameter to the function is optional (has a default value) then that parameter may be left off the path.

For example the path ``foo/bar/abc/def`` could match ``FooController->bar('abc', 'def')`` but if the ``bar()`` function can't accept two arguments then it will fail.

Here's an example of a controller class and the url -> call matches.

.. code-block:: php

    class FooController extends Controller {
        function index($param1, $param2, $param3 = null) {
            // ...
        }

        function bar($param1, $param2) {
            // ...
        }
    }

* ``/foo/abc/def/ghi`` -> ``FooController->index('abc', 'def', 'ghi');``
* ``/foo/abc/def/`` -> ``FooController->index('abc', 'def', null);``
* ``/foo/abc`` -> 404 NotFound
* ``/foo/bar/abc/def`` -> ``FooController->bar('abc', 'def');``
* ``/foo/bar/abc/def/ghi`` -> 404 NotFound
* ``/foo/bar/abc`` -> 404 NotFound

.. note::

    That last one could actually match ``FooController->index('bar', 'abc')`` but because ``bar`` matched a function it doesn't fall back to the index. This is important to note so that you take this into consideration when naming your functions and what data you could be expecting.


Sub directories
---------------

Cohesion will itterate through the URI components and will first try to find a controller with the name of the component, then it will look to see if there's a directory with that name and move on to the next URI component.

* ``/some/path/foo/bar/abc/def`` will match the file ``controllers/some/path/FooController.php`` and run ``FooController->bar('abc', 'def');``


Redirects
=========

Redirects can be specified in the config. Each of the keys within the redirects configuration are used as regular expressions to match on the path. If the regex matches the page will be redirected to the value here.

For example this will match the default favicon most browsers will look for and redirect to the icon in the ``assets/images`` directory

.. code-block:: json

    {
        "routing": {
            "redirects": {
                "^/favicon.ico$": "/assets/images/favicon.ico"
            }
        }
    }
