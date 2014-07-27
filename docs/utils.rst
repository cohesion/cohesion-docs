Utility Classes
***************

Cohesion comes with several extremely lightweight utility classes such as input handling, database interaction, configuration, and many more. More will be added over time and I'm always happy to receive pull requests additional utilities.


Database Interaction
====================

A MySQL database library is provided to provide safe interaction with MySQL databases. The database class includes support for master/slave queries, transactions, named variables, as well as many other features.

The Database library isn't an ORM, it requires you to write SQL statements. This allows for better performance optimization and forces you to think about what SQL will actually be executed.

The database library uses Mustache style named parameters.

For more information on the database library read the documentation at the start of the ``MySQL.php`` file.


Configuration Library
=====================

A config object class is provided for easy and extendible configuration. The config object will read one or more JSON formatted files and sections of the config can be given to classes to provide either business rule constants or library configurations. It is designed so that you can have a default config file with all the default configurations for your application then you can have a production config file that will overwrite only the variables within that config file such as database access etc.

You can access sub settings using dot notation.


Input Handler
=============

An extremely simple input handler is provided. The input handler doesn't do anything to prevent SQL injection or XSS as these are handled by the Database library and the Views respectively.

