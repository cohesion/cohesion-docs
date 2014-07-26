Installation
************

This documentation currently focuses on installing Cohesion on a Debian/Ubuntu system.


Requirements
============

Once PHP is installed and set up adding additional libraries will be handled by Composer but there're still a few things you need to install yourself before then.

* PHP version 5.4 or above
* PHP-FPM
* MySQLnd extension
* APCu extension
* CURL extension


Installing PHP and required extensions
--------------------------------------
::

   sudo apt-get install php5 mysql-client php5-mysqlnd php5-fpm php5-curl php-pear php5-dev

Install APCu for fast access caching::

   sudo pecl install apcu-beta


You'll most likely have to add the extension to your php.ini::

   sudo echo "extension=apcu.so" > /etc/php5/mods-available/apcu.ini
   sudo ln -s /etc/php5/mods-available/apcu.ini /etc/php5/conf.d/20-apcu.ini
   sudo ln -s /etc/php5/mods-available/apcu.ini /etc/php5/fpm/conf.d/20-apcu.ini

The paths may be different depending on your distribution


Make PHP-FPM use a Unix socket
------------------------------

By default PHP-FPM is listening on port 9000 on 127.0.0.1 in some distributions. You should make PHP-FPM use a Unix socket which avoids the TCP overhead. To do this, open ``/etc/php5/fpm/pool.d/www.conf``::

   sudo vi /etc/php5/fpm/pool.d/www.conf

Find the existing ``listen`` line and comment it out and add the new one as shown here::

   [...]
   ;listen = 127.0.0.1:9000
   listen = /var/run/php5-fpm.sock
   [...]


Installing Composer
===================

`Composer <https://getcomposer.org/>`_ is the package manager used by modern PHP applications and is the only recommended way to install Cohesion. To install composer run these commands::

   curl -sS https://getcomposer.org/installer | php
   sudo mv composer.phar /usr/local/bin/composer


Installing Nginx
================

It's recommended to use `Nginx <http://en.wikipedia.org/wiki/Nginx>`_ with Cohesion. Nginx is a very lightweight web server and is much more efficient than Apache and very easy to configure::

   sudo apt-get install nginx


Installing Cohesion with Composer
=================================

Once composer is installed run the following command to create a new project in a ``myproject`` directory using Cohesion::

   composer create-project cohesion/cohesion-framework -sdev myproject

Composer will then download all required dependencies and create the project directory structure for you.

After composer finishes the installation process the installer will ask you ``Do you want to remove the existing VCS (.git, .svn..) history? [Y,n]?`` just hit ``<Enter>`` to safely remove the Cohesion git history. This will prevent you from polluting your projects version history with Cohesion commits. It will also make it easier to set up your own version control for your project.


Setting up Nginx server
=======================

Default Nginx configurations are provided within the ``/config/nginx/`` directory of your project. So that we can keep our server configurations in version control we'll just link to the configuration file you want to use for the current environment within our project::

   sudo ln -s /full/path/to/myproject/conf/nginx/local.conf /etc/nginx/sites-available/myproject-local
   sudo ln -s /etc/nginx/sites-available/myproject-local /etc/nginx/sites-enabled/myproject

.. note::

    Your nginx directory might be somewhere else depending on your distribution.

Open up the configuration and set the ``root`` path to the ``www`` directory within your project::

   vi config/nginx/local.conf

You can create additional Nginx configuration files for your different environments just remember to change the ``server_name``, ``root`` path and ``APPLICATION_ENV``.

Restart nginx::

   sudo service nginx restart

Now you should be able to see a default Cohesion welcome page when you go to http://localhost.

