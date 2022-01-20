.. _DockerGettingStarted: https://docs.docker.com/get-started/
.. _MonkeyDo/Lobes: https://github.com/MonkeyDo/lobes
.. _MusicBrainz: https://musicbrainz.org/account/applications
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

To clone the repository and point the local HEAD to the latest commit in the stable branch, something like the following command should work:

::
    
    git clone --recurse-submodules https://github.com/metabrainz/bookbrainz-site.git

Since this project makes use of git submodules, you need to use ``git clone --recurse-submodules`` to clone it.

Alternatively, to manually initialize submodules, run these two commands:
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

Advance Debugging
=================

Testing
=======

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
