######################
The Entity Editor
######################

Introduction
==============

In Bookbrainz, a set of information about a particular author, book, publication, magazine, etc. is referred to as an *Entity*, and the form which we use to add/edit this information is called the **Entity-Editor**.
We currently have the following types of entities in Bookbrainz:

* Author
* Work
* Edition
* Edition-Group
* Series 
* Publisher


Different sections of the Entity Editor
========================================

The Entity-Editor can be broken down into distinct mini-sections which are as follows:

Name Section
-------------
Alias Section
--------------
Identifier Section
----------------------
Entity Section
------------------
Relationship Section
---------------------
Annotation Section
---------------------
Submission Section
----------------------



Adding an Entity
=================

When we click on an Add Entity button, it takes us to a page with an url like this:
``https://bookbrainz.org/{entityType}/create``.
We make use of this ``entityType`` to generate the entity-specific markup for our entity-editor. 
While creating the markup, it also creates a store for our entity-editor, with a *rootReducer* which consists of a combination of reducers, each concerned with a particular section of the entity-editor namely:

* aliasEditorReducer
* annotationSectionReducer
* buttonBarReducer
* entityReducer
* identifierEditorReducer
* nameSectionReducer
* relationshipSectionReducer
* submissionSectionReducer


Editing an Entity
==================

Merging Entities
===================
