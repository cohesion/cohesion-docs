Error Handling
**************

All errors should throw exceptions and only be caught at a point in the code that is equipped to handle the exception.

Exceptions
==========

You should create your own exceptions for your application so that they can be handled appropriately. Cohesion specifies a ``UserSafeException``. You should use an implemenation of a ``UserSafeException`` for any errors that are safe for the user to see. This includes things such as invalid input etc. The message of ``UserSafeException``\s are displayed back to the user.

Error Output
============

When an exception isn't caught within the code it will generate an error page. Different exceptions may generate different error Views. A ``NotFoundException`` will show a 404 page, an ``UnauthorizedException`` will show a 403 page, and other exceptions will show a 500 ``ServerError`` page. If the environment is set to 'production' non ``UserSafeExceptions`` will just generate a vague error message. But if the environment isn't 'production' then it will show the exception error message as well as the stack trace for easier debugging.

If there's an uncaught exception in an AJAX call it will determine the expected format and return a valid response in that format. For JSON it will return something similar to:

.. code-block:: json

    {
        "success": false,
        "errors": [
            "Server Error"
        ]
    }

