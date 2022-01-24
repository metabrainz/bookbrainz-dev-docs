######################
The Entity Editor
######################

Introduction
==============

In Bookbrainz, a set of information about a particular author, book, publication, magazine, etc. is referred to as an *Entity*, and the form which we use to add/edit this information is called the **Entity-Editor**.
There are 6 entity types in the BookBrainz world: Author, Work, Edition Group, Edition, Publisher and Series.
For a better description of each entity see :ref:`entities-description`.

The code for the entity editor can be found in ``src/client/entity-editor``.
The entity-editor has been divided into different sections, and each of these sections have a folder within this directory.

We use Redux for state management. Each section has its own set of actions and reducers to handle state corressponding to the component. For more information on how Redux works, you can refer to this: `Getting started with React-Redux <https://react-redux.js.org/introduction/getting-started>`_.

The directory for each section contains the separate components(which share common code) for editing and merging entities, along with the aforementioned actions and reducers. The validation code is also present in the folder.


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

Let us take an example how we make use of actions and reducers in our entity-editor by describing the workflow for the **Name-Section** of the entity editor.

You can take a look at the initial state of a particular section by looking at the reducer in its directory. 

The initial state for Name-section can be seen by looking at the reducer file in ``src/client/entity-editor/name-section/reducer.js``. The initial state looks like this: 
::
    state = Immutable.Map({
                disambiguation: '',
                exactMatches: null,
                language: null,
                name: '',
                searchResults: null,
                sortName: ''
            })

Here, the fields ``disambiguation``, ``name``, ``sortName`` and ``language`` are self-explanatory. 
Whenever, we start entering any name in the Name field, we use the onChange event handler to fire off 4 different actions:

* ``onNameChange``: this updates the *nameValue*.
* ``onNameChangeSearchName``: as we enter the name, we try to seach the nameValue in our database in order to let the user see whether the entity already exists in our database. This updates the search term and adds the results in *searchResults*.
* ``onNameChangeCheckIfExists``: if we find an entity whose name matches exactly with the entity name we entered, we try to display a warning so as to avoid the user from making a duplicate entry. This updates the content of *exactMatches*.
* ``searchForMatchindEditionGroups``: Search for Edition Groups that match the name, if the entity is an Edition.

Similarly, appropriate eventHandlers and actions are present for updating the value of *Sort Name* field, *Language*, and *Disambiguation*.

Hence, a similar pattern is followed on all the other sections of the entity-editor, where we make use of onChange event handlers for a particular field, to fire off the action which subsequently uses dispatch to update its corresponding field in the state.

    
The Relationship Editor
-------------------------


Editing an Entity
==================

Merging Entities
===================
