Troubleshooting
===============

How to access the Application Logs?
-----------------------------------

Display the application log file via:

.. code-block:: terminal

    $ symfony log app --tail

Any log messages generated by the application is sent to this ``app`` file. This includes language errors such as PHP Errors, Warnings, and Notices, as well as uncaught exceptions.

It also contains your application logs if you log on ``stderr``.

Because Platform.sh manages this file for you (preventing disks to get filled and using very fast local drives instead of slower network disk), we recommend that applications always output their log to ``stderr``. For Monolog, check ``config/packages/prod/monolog.yaml``:

.. code-block:: diff

    --- a/config/packages/prod/monolog.yaml
    +++ b/config/packages/prod/monolog.yaml
    @@ -11,7 +11,7 @@ monolog:
                members: [nested, buffer]
            nested:
                type: stream
    -            path: "%kernel.logs_dir%/%kernel.environment%.log"
    +            path: php://stderr
                level: debug
            buffer:
                type: buffer

**If you log deprecations, don’t forget to log them on** ``stderr`` **as well.**

Oops! An Error Occurred
-----------------------

This error message comes from your application and is generated by the default Symfony's error template.

The server returned a "500 Internal Server Error"
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. image:: _includes/production-error-500.png
   :alt: A 500 error page in the production mode
   :align: center
   :class: with-browser

If your application works locally but you see this message on Platform.sh it usually means you have a configuraton error or missing a dependency.

To fix this issue you have to inspect application logs, the cause of the error is usually specified in the error message:

.. code-block:: terminal

   $ symfony logs all
   [app] [14-Aug-2020 10:52:27 UTC] [critical] Uncaught PHP Exception Exception: [...]
   [app]
   [php.access] 2020-08-14T10:52:27Z GET 500 2.386 ms 2048 kB 419.11% /
   [access] 78.247.136.119 - - [14/Aug/2020:10:52:27 +0000] "GET / HTTP/1.1" 500 843 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.125 Safari/537.36"

If the error happens on a non production environment or on the main environment of a non production project you can also enable Symfony's dev/debug mode to inspect the cause of the error:

.. code-block:: terminal

   # Enable debug mode
   $ symfony env:debug
   # Disable debug mode
   $ symfony env:debug --off

The server returned a "404 Not Found"
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

New Symfony applications comes without controllers by default. This means there's no page to show as a homepage. When running your project locally you should have been welcomed with this page:

.. image:: https://symfony.com/uploads/assets/blog/new-symfony-welcome-page.png
   :alt: The default Symfony welcome page in the development mode
   :align: center
   :class: with-browser

But with this page when running on Platform.sh:

.. image:: _includes/production-error-404.png
   :alt: A 404 error page in the production mode
   :align: center
   :class: with-browser

This is because Platform.sh runs in production mode and as such Symfony shown a generic 404 error. To fix this, you will have to `create your first Symfony page <https://symfony.com/doc/current/page_creation.html>`_.

If you already created a custom page, check that all your files are commited, that you ran ``symfony deploy`` and it succeeded.
