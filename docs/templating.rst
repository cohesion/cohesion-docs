Templating
**********

Cohesion uses the `Mustache <http://mustache.github.io/>`_ templating language (thanks to `@bobthecow <https://github.com/bobthecow>`_'s `implementation <https://github.com/bobthecow/mustache.php>`_) by default and is initialised with a CohesionMo class that set's up some additional useful functionalities for you. The reason for choosing Mustache was to eradicate the ability to perform any logic within the View. It's recommended that you read through the `Mustache documentation <http://mustache.github.io/mustache.5.html>`_ on the Syntax. The implementation of Mustache being used is extremely efficient as it will pre-compile the templates to PHP code and store it in fast access cache.


Additional functionalities
==========================

Variable partials
-----------------

Partials will check to see if there's a variable with that name first, if there is then the content of that variable will be used, otherwise it will revert to the standard behaviour of using the name as the partial. This means you have to take this into consideration when naming your variables and partials.

If you want to load a template named header.html then a template who's name is set by the variable $content then the footer.html template, you can simply do this:

.. code-block:: html

    {{> header}}
    {{> content}}
    {{> footer}}


Site URLs
---------

A default lambda is added to generate site URLs for you. Never hard code your URLs or use relative paths. Always use this lambda when generating any links to pages within your site.

.. code-block:: html

    <a href="{{#site_url}}about{{/site_url}}">About Us</a>


Asset URLs
----------

A default lambda has also been added for generating URLs to your assets. Rather than using the site URL to access your assets you should always use the asset lambda which will allow for using `CDNs <http://en.wikipedia.org/wiki/Content_delivery_network>`_ for your static assets. Even if you don't wish to use a CDN for now it's a good practice to still use this lambda for your assets and if no CDN hosts are defined in your config it will just revert to the site URL. Then when you decide you need to relieve the load on your sever you can set up a CDN and just modify your config to point to that.

.. code-block:: html

    <script src="{{#asset_url}}{{asset_path.javascript}}main.js{{/asset_url}}"></script>


Asset versioning
^^^^^^^^^^^^^^^^

The asset URLs will also contain a "version" as a parameter in the query string which is basically just an md5 checksum of the file. This is used to invalidate the CDN cache whenever you release an update to any of your static assets. The above example will render as:

.. code-block:: html

    <script src="http://cdn.example.com/assets/js/main.js?v=365424d18c0e435388a859592b8f5e3e"></script>

It's very important that when setting up your CDN you tell it to include the query string in the cache key, otherwise you will have to manually invalidate your assets every time you update them.

Don't worry though, we're not recomputing the md5 checksum of every static asset on every page load, these are stored in the local cache (`APC <http://www.php.net/manual/en/book.apc.php>`_) and only updated when the ttl on the cache expires.

