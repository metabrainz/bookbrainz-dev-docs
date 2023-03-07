.. _DockerGettingStarted: https://docs.docker.com/get-started/
.. _MonkeyDo/Lobes: https://github.com/MonkeyDo/lobes
.. _MusicBrainz: https://musicbrainz.org/account/applications
.. _CritiqueBrainz: https://critiquebrainz.org/profile/applications/
.. _Docker: https://docs.docker.com/install/
.. _Docker-Compose: https://docs.docker.com/compose/install/

############
Installation
############

So you've decided to contribute some code to BookBrainz?
Fantastic! Thank you!

Here are some instruction to get your local coding environment set up.

Setting Up
==========

BookBrainz depends on having PostgreSQL, Redis, Elasticsearch and NodeJS set up and running.

But first some basic configuration common to both docker and manual installation.

Forking
*******

Fork this repository

``https://github.com/metabrainz/bookbrainz-site.git``


Cloning
*******

To clone the repository and point the local HEAD to the latest commit in the stable branch, something like the following command should work

::
    
    git clone https://github.com/<Your_Github_Username>/bookbrainz-site.git

Since this project makes use of git submodules, you need to use ``git clone --recurse-submodules`` to clone it.

Alternatively, to manually initialize submodules, run these two commands
::

    git submodule init
    git submodule update

Currently we are using these submodules

* `MonkeyDo/Lobes`_ in ``src/client/stylesheets/lobes``

Configuration
*************

Create a copy of ``config/config.json.example`` and rename it to ``config.json``. You can do this by running this command from the bookbrainz-site directory.

::

    cp config/config.json.example config/config.json

If you want to be able to sign-up and edit, you will need to set up authentication under ``musicbrainz``.
To get the ``clientID`` and ``clientSecret`` tokens, head to `MusicBrainz`_ and register a new developer application.

Make sure to enter the callback URL as ``http://localhost:<port>/cb`` (port: 9099 by default).

If you want to review the entities, you will need to set up authentication under ``critiquebrainz``.
To get the ``clientID`` and ``clientSecret`` tokens, head to `CritiqueBrainz`_ and register a new developer application.

Make sure to enter the homepage URL as ``http://localhost:<port>/`` and callback URL as ``http://localhost:<port>/external-service/critiquebrainz/callback`` (port: 9099 by default).

You can then copy the tokens for that developer application and paste as strings in your ``config/config.json``. The tokens and callback URL in your ``config/config.json`` needs to match exactly the one for the developer application.

Docker Installation
===================
The easiest way to get a local developement server running is using Docker. This will save you a fair amount of set up.

You'll need to install Docker and Docker-compose on your development machine:

* `Docker`_
*  `Docker-compose`_

.. important:: 
  
  We recommended Windows users to :doc:`docker-setup` (preferbly WSL2) to avoid any compatibility issues.
  
When it is installed, follow the below instructions step by step.

Database set-up
***************
When you first start working with BookBrainz, you will need to perform some initialization for PostgreSQL and import the latest BookBrainz database dump.

Luckily, we have a script that does just that: from the command line, in the ``bookbrainz-site`` folder, type and run ``./scripts/database-init-docker.sh``. The process may take a while as Docker downloads and builds the images. Let that run until the command returns.

.. note::
  The latest database dump can be found `here <http://ftp.musicbrainz.org/pub/musicbrainz/bookbrainz/latest.sql.bz2>`_

Running Web server
******************
If all went well, you will only need to run ``./develop.sh`` in the command line from the ``bookbrainz-site`` folder. Press ``Ctrl+c`` to stop the server. 

.. note::
  The dependencies (postgres, redis,…) will continue to run in the background. To stop them, run the command ``./stop.sh``

Wait until the console output gets quiet and this line appears: 
::

    > cross-env node ./lib/server/app.js.

After a few seconds, you can then point your browser to localhost:9099.

Make changes to the code in the ``src`` folder and run ``./develop.sh`` again to rebuild and run the server.

Once you are done developing, you can stop the dependencies running inside docker in the background by running ``./stop.sh``.

Search server setup
===================

In order for searching to work on your local server, you will need to index the contents of the database.

1. first, ensure that Elasticsearch is running.
2. add your user name (if you haven't created a user yet, `now is the time! <https://musicbrainz.org/doc/How_to_Create_an_Account>`) to the array of ``trustedUsers`` in the ``src/server/routes/search.js`` file
3. with that done and the server (re)started, navigate to ``localhost:9099/search/reindex``
    Reindexing will take a few minutes depending on your resources, and you can expect that the browser window will time out before the reindexing is done.
    However the process will continue in the background and after a little while the search indices will be created.
4. You can now try searching for an entity on the page ``localhost:9099/search``

Advance Users
=============
To improve your developer experience, here are some things we suggest you should do

Live Reload
***********
You may want to use Webpack to build, watch files and inject rebuilt pages without having to refresh the page, keeping the application state intact, for the price of increased compilation time and resource usage (see note below).

If you are running the server manually, you can simply run ``yarn run debug`` in the command line.

If you're using Docker and our ``./develop.sh`` script, you will need to modify the ``docker-compose.yml`` file and change a few things on the ``bookbrainz-site`` service defined there

1. Change the bookbrainz-site command to

* ``yarn run debug`` if you only want to change client files (in ``src/client``)
* ``yarn run debug-watch-server`` if you also want to modify server files (in ``src/server``)

2. Mount the ``src`` folder to the bookbrainz-site service

For example:

::

    services:
      bookbrainz-site:
      # 1. Change the command to run
        command: yarn run debug
        volumes:
          - "./config/config.json:/home/bookbrainz/bookbrainz-site/config/config.json:ro"
      # 2. Mount the src directory
          - "./src:/home/bookbrainz/bookbrainz-site/src"
.. warning::
  Using Webpack watch mode (``yarn run debug``) results in more resource consumption (about ~1GB increased RAM usage) compared to running the standard web server.

Debugging with VSCode
*********************
You can use VSCode to run the server or API and take advantage of its debugger, an invaluable tool I highly recommend you learn to use.

This will allow you to put breakpoints to stop and inspect the code and variables during its execution, advance code execution line by line and step into function calls, instead of putting console.log calls everywhere.

`Here <https://www.youtube.com/watch?v=yFtU6_UaOtA>`_ is a good introduction to debugging javascript in VSCode.

There are VSCode configuration files (in the ``.vscode`` folder) for running both the server and the tests, useful in both cases to debug into the code and 
see what is happening as the code executes. Make sure the dependencies (postgres, redis, elasticsearch) are running, and 
you can just open the debugger tray in VSCode, select 'Launch Program' and click the button!

Testing
=======
The test suite is built using `Mocha <https://mochajs.org/>`_ and `Chai <https://www.chaijs.com/>`_. Before running the tests, you will need to set up a ``bookbrainz_test`` database in postgres. Here are the instructions to do so:

Run the following command to create and set up the ``bookbrainz_test`` database using Docker
::

    docker-compose run --rm bookbrainz-site scripts/wait-for-postgres.sh scripts/create-test-db.sh.

If you are running postgres manually outside of Docker, you can set some environment variables before running the script `scripts/create-test-db.sh`
In particular ``POSTGRES_HOST=localhost`` but you can also set ``POSTGRES_USER``, ``POSTGRES_PASSWORD`` and ``POSTGRES_DB``.

Once your testing database is set up, you can run the test suite using 

* To run in Docker
::

    docker-compose run --rm bookbrainz-site yarn run test 

* To run locally
::
  
    yarn run test 

.. note::
  You may need to adjust your ``config/test.json`` file to match your setup.

.. seealso:: 
  if you face any issues, please refer to our :doc:`troubleshooting` section.
