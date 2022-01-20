.. _DockerGettingStarted: https://docs.docker.com/get-started/
.. _MonkeyDo/Lobes: https://github.com/MonkeyDo/lobes
.. _MusicBrainz: https://musicbrainz.org/account/applications
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

Cloning
*******

To clone the repository and point the local HEAD to the latest commit in the stable branch, something like the following command should work

::
    
    git clone --recurse-submodules https://github.com/metabrainz/bookbrainz-site.git

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

You can then copy the tokens for that developer application and paste as strings in your ``config/config.json``. The tokens and callback URL in your ``config/config.json`` needs to match exactly the one for the developer application.

Docker Installation
===================
The easiest way to get a local developement server running is using Docker. This will save you a fair amount of set up.

You'll need to install Docker and Docker-compose on your development machine:

* `Docker`_
*  `Docker-compose`_

When it is installed, follow the below instructions step by step.

.. note:: 
  If you are using docker-toolbox you need to replace `elasticsearch:9200 <https://github.com/metabrainz/bookbrainz-site/blob/master/config/config.json.example#L30>`_ with ip address of your docker-machine in config.json.
  To get ip address of your docker machine use command ``docker-machine ip default``

Database set-up
***************
When you first start working with BookBrainz, you will need to perform some initialization for PostgreSQL and import the latest BookBrainz database dump.

Luckily, we have a script that does just that: from the command line, in the ``bookbrainz-site`` folder, type and run ``./scripts/database-init-docker.sh``. The process may take a while as Docker downloads and builds the images. Let that run until the command returns.

The latest database dump can be found `here <http://ftp.musicbrainz.org/pub/musicbrainz/bookbrainz/latest.sql.bz2>`_

Running Web server
******************
If all went well, you will only need to run ``./develop.sh`` in the command line from the ``bookbrainz-site`` folder. Press ``Ctrl+c`` to stop the server. 
The dependencies will continue to run in the background.

Wait until the console output gets quiet and this line appears: 
::

    > cross-env node ./lib/server/app.js. After a few seconds, you can then point your browser to localhost:9099.

Make changes to the code in the ``src`` folder and run ``./develop.sh`` again to rebuild and run the server.

Once you are done developing, you can stop the dependencies running in docker in the background by running ``./stop.sh``.

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

Troubleshooting
===============

General
*******
* ``[Error: EACCES: permission denied, scandir '/root/.npm/_logs']``

  There is some incompatibility with Docker for Windows The solution
  is to **enable WSL2** (Windows Subsystem for Linux v2) and the
  problem goes away.

.. note::
   refresh your package catalog using ``sudo apt update``

* ``Can't open input file latest.sql.bz2: No such file or directory``
  
  After downloading the data dumps, you may realize that an attempt
  to uncompress it using the command
  ``bzip2 -d latest.sql.bz2`` doesn't work and gives the above
  error.

  It can be solved by giving the *absolute* path of the latest.sql.bz2
  file in place of the file name.
  ::

        
        bzip2 -d path/to/file
* ``fatal: unable to access 'https://github.com/path/to/repo.git/': gnutls_handshake() failed: Error in the pull function``
   
  Check your internet connection and make sure  you are not working behind any proxy.

* No Css Styles?
   
  Try running ``yarn run build-less`` or ``npm run build-less`` from the project directory.

Elasticsearch
*************
* Elasticsearch taking too much time?
  
  let ElasticSearch to run on its own terminal, and proceed the building process by making another window of terminal.

* ``Waiting for elasticsearch:9200 .elasticsearch: forward host lookup failed: Unknown host``
  
  The cause could be the docker-machine's memory limits. you can inspect this with the command:
  ::

    docker-machine inspect machine-name

  To diagnose this problem, try taking a look at the logs with the command:
  ::

    docker-compose logs elasticsearch

  And if you see an error within the logs along the lines of:
  ::

    # There is insufficient memory for the Java Runtime Environment to continue.
    # Native memory allocation (mmap) failed to map 2060255232 bytes for committing reserved memory.

  Please try recreating the default docker machine by:

  1. Remove default docker-machine with the command:
  :: 
  
    docker-machine rm default
 	
  2. Create a new default machine with the command:
  ::

 	docker-machine create -d virtualbox --virtualbox-cpu-count=2 --virtualbox-memory=4096 --virtualbox-disk-size=50000 default

  3. Restart your docker environment with the commands:
  ::	
    
    docker-machine stop
    exit

Redis
*****
* ``port 6379 already in use``
  
  This is because redis server is already on and you need to stop it first so that it can restart. 
  So to get rid of this issue simply run the below command
  ::

    /etc/init.d/redis-server stop

Node/npm/yarn
*************
* When filling out the requirements of BookBrainz, you'll encounter an error that says you'll need to install postgresql-server-dev-X.Y for building a server-side extension or ``libpq-dev`` for building a client-side application To solve this problem, please install ``libpq-dev`` and ``node-gyp``

  for ubuntu 
  ::

    sudo apt install -y node-gyp libpq-dev