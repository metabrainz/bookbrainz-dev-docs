.. BookBrainz Developer Docs documentation master file, created by
   sphinx-quickstart on Tue Dec 16 16:17:08 2014.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the BookBrainz Developer Docs!
=========================================

This documentation is intended to act as a guide for developers working on or
with the BookBrainz project, describing the system, its modules and functions.

For a description of BookBrainz and end-user oriented documentation, please
see the `BookBrainz User Guide <https://bookbrainz-user-guide.readthedocs.io/en/latest/>`_.


Contents of this documentation
==============================

.. toctree::
   :maxdepth: 2

   docs/installation
   docs/musicbrainz
   docs/schema
   docs/ORM
   docs/webservice
   docs/editing
   docs/entity-editor

Our repositories
================

Our codebase is divided in a few repositories:

- `bookbrainz-site <https://github.com/metabrainz/bookbrainz-site>`_:the main repository for the website and API
- `bookbrainz-data-js <https://github.com/metabrainz/bookbrainz-data-js>`_: the ORM package we use to interface with the database
- `bookbrainz-dev-docs <https://github.com/metabrainz/bookbrainz-dev-docs>`_: these very same docs you're reading !
- `bookbrainz-user-guide <https://github.com/metabrainz/bookbrainz-user-guide>`_: the user-facing guide for using the website
- `bookbrainz-utils <https://github.com/metabrainz/bookbrainz-utils>`_: database tools and scripts