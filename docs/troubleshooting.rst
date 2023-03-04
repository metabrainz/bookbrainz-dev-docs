===============
Troubleshooting
===============

General
=======
* ``[Error: EACCES: permission denied, scandir '/root/.npm/_logs']``

  There is some incompatibility with Docker for Windows The solution
  is to **enable WSL2** (Windows Subsystem for Linux v2) and the
  problem goes away.


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

* ``[/usr/bin/env: 'bash\r': No such file or directory] or [exec scripts/wait-for-postgres.sh: no such file or directory]``

  These errors may occur when running develop.sh or database-init-docker.sh during installation if git or another application
  has replaced Linux line endings (LF) with Windows line endings (CR LF) in certain .sh files.

  To determine if this is the issue, you may open the .sh files using an application such as Notepad++ or VS Code and check
  the bottom right corner. If any file's line endings are indicated incorrectly as Windows (CR LF) you can resolve the issue
  by following these steps:

  1. Navigate to the bookbrainz-site directory and configure git to clone repositories using the original line endings
  with the following command:
  ::

     git config --global core.autocrlf input

  2. Delete the bookbrainz-site directory from its parent directory.
  ::

  3. Clone the bookbrainz repository again using the appropriate command (the URL will be different if cloning from a fork)
  ::

     git clone --recurse-submodules https://github.com/metabrainz/bookbrainz-site.git

  4. The line endings should now be corrected and you may proceed with following the normal installation procedure.
  ::

Elasticsearch
=============
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
=====
* ``port 6379 already in use``
  
  This is because redis server is already on and you need to stop it first so that it can restart. 
  So to get rid of this issue simply run the below command
  ::

    /etc/init.d/redis-server stop

Node/npm/yarn
=============
* When filling out the requirements of BookBrainz, you'll encounter an error that says you'll need to install postgresql-server-dev-X.Y for building a server-side extension or ``libpq-dev`` for building a client-side application To solve this problem, please install ``libpq-dev`` and ``node-gyp``

  for ubuntu 
  ::

    sudo apt install -y node-gyp libpq-dev