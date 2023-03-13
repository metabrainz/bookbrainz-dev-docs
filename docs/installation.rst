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

But first some basic configuration common to both Docker and manual installation.

Forking
*******

Fork this repository

``https://github.com/metabrainz/bookbrainz-site.git``


Cloning
*******

To clone the repository and point the local HEAD to the latest commit in the stable branch, something like the following command should work:

::

    git clone https://github.com/<Your_Github_Username>/bookbrainz-site.git

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

The easiest way to get a local development server running is using Docker. This will save you a fair amount of set up.

You'll need to install Docker and Docker-Compose on your development machine:

* `Docker`_
* `Docker-Compose`_

.. important:: 
  We recommended Windows users to :doc:`docker-setup` (preferably WSL2) to avoid any compatibility issues.
  
When it is installed, follow the below instructions step by step.

.. note::
  After a fresh Docker installation, you need ~4GB of disk space for the container images, better more.

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


Manual Installation
===================

If you do not want to use Docker (``./develop.sh``) to run the server on your machine, you can run the server code manually (regardless of whether you are running dependencies (database, search,…) with Docker or you are running them manually)
So for setting up and running the NodeJS server outside of Docker -

Installing NodeJS and Packages
******************************

To install NodeJS, follow the instruction for your operating system on the `official website <https://nodejs.org/en/download/>`_.

The site depends on a number of node packages which can be installed using yarn (or npm):

::

    cd bookbrainz-site/
    yarn install

This command will also compile the site LESS and JavaScript source files.

Configuration
*************

Our ``config.example.json`` is set up to work out of the box running everything in Docker. Addresses for the dependencies refer to docker container names, so that containers can communicate with each other.

For local development (run outside of Docker), make a copy of `config/config.local.json.example` and [fill up the musicbrainz tokens](README.md#configuration). You can then pass this configuration file when running the server locally using `--config` flag.
For example, ``yarn start -- --config ./config/config.local.json`` will use ``./config/config.local.json`` config instead of the Default config (``config.json`` for Docker).


Building and running
********************

A number of subcommands exist to manage the installation and run the server.
These are described here; any commands not listed should not be called directly:

* start - start the server in production mode, with code built once
* debug - start the server in debug mode, with code watched for changes
* lint - check the code for syntax and style issues
* test - perform linting and attempt to compile the code
* jsdoc - build the documentation for JSDoc annotated functions within the
  code 


Installing dependencies manually 
********************************

If you don't want to use Docker for the dependencies, here are the steps you will need to take to get your local environment up and running.

PostgreSQL
----------

BookBrainz uses version 12.3. To get PostgreSQL, use one of the following commands:

Debian-based OS
::

    sudo apt-get install postgresql

Red Hat-based OS
::

    sudo yum install postgresql-server

Redis
-----

To install Redis, run similar commands to get the dependency from your package
manager:

Debian-based OS
::

    sudo apt-get install redis-server

Red Hat-based OS
::

    sudo yum install redis


Elasticsearch
-------------

To install Elasticsearch, follow `this helpful guide <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-ubuntu-16-04) for Linux-based systems or the [official instructions](
https://www.elastic.co/guide/en/elasticsearch/reference/6.3/install-elasticsearch.html>`_.

The BookBrainz server has been tested with ElasticSearch version 6.3.2.

Setting up Dependencies
-----------------------

No setup is required for Redis or Elasticsearch. However, it is necessary to
perform some initialization for PostgreSQL and import the latest BookBrainz
database dump.

Firstly, begin downloading the `latest BookBrainz dump <http://ftp.musicbrainz.org/pub/musicbrainz/bookbrainz/latest.sql.bz2>`_.

Then, uncompress the ``latest.sql.bz2`` file, using the bzip2 command:
::

    bzip2 -d latest.sql.bz2

This will give you a file that you can restore into PostgreSQL, which will
set up data identical to the data we have on the bookbrainz.org website. First, you must create the necessary role and database with these two commands:
::

    psql -h localhost -U postgres --command="CREATE ROLE bookbrainz"	
    psql -h localhost -U postgres --command="CREATE DATABASE bookbrainz"

Then you can restore the database from the lates dump you dowloaded. To do this, run:
::

    psql -h localhost -U postgres -d bookbrainz -f latest.sql

At this point, the database is set up, and the following command should give you a list of usernames of BookBrainz editors (after entering the password from earlier):
::

    psql -h localhost -U postgres bookbrainz --command="SELECT name FROM bookbrainz.editor"

You are also required to set the password of your local PostgreSQL instance.
You can do this by
::

    psql -h localhost -U postgres

    postgres=# \password

This will set the password to your PostgreSQL, which you will need to set in the `config/config.json` database section.

Search server setup
===================

In order for searching to work on your local server, you will need to index the contents of the database.

1. First, ensure that Elasticsearch is running.
2. Add your user name (if you haven't created a user yet, `now is the time! <https://musicbrainz.org/doc/How_to_Create_an_Account>`_) to the array of ``trustedUsers`` in the ``src/server/routes/search.js`` file.
3. With that done and the server (re)started, navigate to ``localhost:9099/search/reindex``.
   Reindexing will take a few minutes depending on your resources, and you can expect that the browser window will time out before the reindexing is done.
   However the process will continue in the background and after a little while the search indices will be created.
4. You can now try searching for an entity on the page ``localhost:9099/search``.

Advanced Users
==============

To improve your developer experience, here are some things we suggest you should do.

.. _live-reload:

Live Reload
***********

You may want to use Webpack to build, watch files and inject rebuilt pages without having to refresh the page, keeping the application state intact, for the price of increased compilation time and resource usage (see note below).

If you are running the server manually, you can simply run ``yarn run debug`` in the command line.

If you're using Docker and our ``./develop.sh`` script, you will need to create a custom Compose file and define a few overrides for the ``bookbrainz-site`` service there:

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
      # 2. Mount the src directory
          - "./src:/home/bookbrainz/bookbrainz-site/src"

Ideally you save this new Compose file inside the ``local/`` directory, which will be ignored by git, for example as ``local/docker-compose.live-reload.yml``.

Now you have to explicitly tell Docker-Compose which Compose files it should read when the ``./develop.sh`` script is run.
In addition to the default ``docker-compose.yml`` we also want Compose to read our custom file with the overrides.

We will achieve that by creating a ``.env`` file (in the repository's root directory) which sets the ``COMPOSE_FILE`` environment variable, e.g.

::

    COMPOSE_FILE=docker-compose.yml:local/docker-compose.live-reload.yml

.. warning::
  Using Webpack watch mode (``yarn run debug``) results in more resource consumption (about ~1GB increased RAM usage) compared to running the standard web server.

Debugging with VSCode
*********************

You can use VSCode to run the server or API and take advantage of its debugger, an invaluable tool I highly recommend you learn to use.
This will allow you to put breakpoints to stop and inspect the code and variables during its execution, advance code execution line by line and step into function calls, instead of putting console.log calls everywhere.

`Here <https://www.youtube.com/watch?v=yFtU6_UaOtA>`_ is a good introduction to debugging javascript in VSCode.

Running the code with Docker
----------------------------

If you're using Docker with our ``./develop.sh`` script, you will need to adapt your custom Compose file once again, or create a new one (see :ref:`live-reload`):

1. Change the bookbrainz-site service's ``command`` to

* ``yarn run debug --inspect=0.0.0.0:9229`` if you only want to change client files (in ``src/client``)
* ``yarn run debug-watch-server --inspect=0.0.0.0:9229`` if you also want to modify server files (in ``src/server``)

2. Add ``9229:9229`` to ``ports``, for the Docker container to expose port 9229.

For example:

::

    services:
      bookbrainz-site:
      # 1. Change the command to run
        command: yarn run debug --inspect=0.0.0.0:9229
        ports:
      # 2. Expose the port
          - "9229:9229"

Now make sure that you have the `Docker <https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker>`_ extension installed.

That's it, now you can just open the debugger tray in VSCode, select 'Docker: Attach to Node' and click the button!

Running the code with VSCode
----------------------------

There are VSCode configuration files (in the ``.vscode`` folder) for running both the server and the tests, useful in both cases to debug into the code and see what is happening as the code executes. Make sure the dependencies (postgres, redis, elasticsearch) are running, and you can just open the debugger tray in VSCode, select 'Launch Program' and click the button!

Testing
=======

The test suite is built using `Mocha <https://mochajs.org/>`_ and `Chai <https://www.chaijs.com/>`_. Before running the tests, you will need to set up a ``bookbrainz_test`` database in postgres. Here are the instructions to do so:

Run the following command to create and set up the ``bookbrainz_test`` database using Docker:
::

    docker-compose run --rm bookbrainz-site scripts/wait-for-postgres.sh scripts/create-test-db.sh

If you are running postgres manually outside of Docker, you can set some environment variables before running the script ``scripts/create-test-db.sh``.
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
  If you face any issues, please refer to our :doc:`troubleshooting` section.
