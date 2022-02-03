######################
The Entity Editor
######################

Introduction
==============

In Bookbrainz, a set of information about a particular author, book, publication, magazine, etc. is referred to as an *Entity*, and the form which we use to add/edit this information is called the **Entity-Editor**.
There are 6 entity types in the BookBrainz world: Author, Work, Edition Group, Edition, Publisher and Series.
For a better description of each entity, see :ref:`entities-description`.

The code for the entity editor can be found in `src/client/entity-editor <https://github.com/metabrainz/bookbrainz-site/tree/master/src/client/entity-editor>`_.
The entity-editor has been divided into different sections, and each of these sections have a folder within this directory.

We use Redux for state management. Each section has its own set of actions and reducers to handle state corresponding to the component. For more information on how Redux works, you can refer to this: `Getting started with React-Redux <https://react-redux.js.org/introduction/getting-started>`_.

Each section has its own directory containing the React components for editing and merging entities, along with the aforementioned Redux actions and reducers. You will also find a ``common`` folder with shared code and a ``validators`` folder containing the code used to validate the data entered in the form before submission.

Different sections of the Entity Editor
========================================

The Entity-Editor can be broken down into distinct mini-sections which are as follows:

Name Section
    This section is used to give a default alias to the entity. It also has fields which define the sort name, the language used to define the name of the entity, and an optional disambiguation field.

Alias Section
    This section helps us to define alternate aliases for the entity, such as an alternate spelling, or a different stylistic representation, etc.
Identifier Section
    This section allows us to add any identifier of the entity in some other databases and services such as an ISBN, MusicBrainz ID, etc.
Entity Section
    This deals with adding some entity specific information, such as a work might have a ``workType``, an edition might have an Edition-Group, etc.
Relationship Section
    This section makes use of a modal called the relationship-editor, which allows us to make a logical connection between two entities.
Annotation Section
    This section contains a field which allows us to add additional freeform data that does not fit in any other section above.
Submission Section
    This section contains a field where the user can enter any edit notes. This also contains the Submit button which finalises our submission.




Adding an Entity
=================

When we click on an Add Entity button, it takes us to a page with an url like this:
``https://bookbrainz.org/{entityType}/create``.
We make use of this ``entityType`` to generate the entity-specific markup for our entity-editor.
The `/create` routes are created separately for each entity type, each having a file in the `src/server/routes/entity folder <https://github.com/metabrainz/bookbrainz-site/tree/master/src/server/routes/entity>`_.

While creating the entity specific markup for our entity-editor, we also inject certain props into our entity-editor.
We make use of `router-level middleware <https://expressjs.com/en/guide/using-middleware.html#middleware.router>`_ to load ``languageTypes``, ``identifierTypes``, and ``relationshipTypes`` specific to the entity we are going to add. We might also need to load some entity-specific props, for example, in case of Work entity, we load ``workTypes`` too.

While creating the markup, it also creates a Redux store for our entity-editor, with a *rootReducer* which consists of a combination of reducers, each concerned with a particular section of the entity-editor namely:

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

The initial state for Name-section can be seen by looking at the reducer file in `src/client/entity-editor/name-section/reducer.js <https://github.com/metabrainz/bookbrainz-site/tree/master/src/client/entity-editor/name-section/reducer.js>`_. The initial state looks like this:
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
Whenever we start entering any name in the Name field, we use the onChange event handler to fire off 4 different actions:

* ``onNameChange``: this updates the *nameValue*.
* ``onNameChangeSearchName``: as we enter the name, we try to seach the nameValue in our database in order to let the user see whether the entity already exists in our database. This updates the search term and adds the results in *searchResults*.
* ``onNameChangeCheckIfExists``: if we find an entity whose name matches exactly with the entity name we entered, we try to display a warning so as to avoid the user from making a duplicate entry. This updates the content of *exactMatches*.
* ``searchForMatchindEditionGroups``: Search for Edition Groups that match the name, if the entity is an Edition.

Similarly, appropriate eventHandlers and actions are present for updating the value of *Sort Name* field, *Language*, and *Disambiguation*.

Hence, a similar pattern is followed on all the other sections of the entity-editor, where we make use of onChange event handlers for a particular field, to fire off an action with the help of the dispatch function to update its corresponding field in the state.

    
The Relationship Editor
-------------------------

Any type of connection between two entities in BookBrainz is called a relationship. We use the `relationship editor component <https://github.com/metabrainz/bookbrainz-site/tree/master/src/client/entity-editor/relationship-editor>`_ to add relationships to the entity we are creating.

The relationship section is concerned with two main tasks:

* Providing an **Add Relationship** button to open a Modal which acts as our relationship-editor. The relevant code for this is present in `src/client/entity-editor/relationship-editor/relationship-editor.tsx <https://github.com/metabrainz/bookbrainz-site/tree/master/src/client/entity-editor/relationship-editor/relationship-editor.tsx>`_.
* Rendering a list of already added relationships. This is done with the help of *RelationshipList* component present in `src/client/entity-editor/relationship-editor/relationship-section.tsx <https://github.com/metabrainz/bookbrainz-site/tree/master/src/client/entity-editor/relationship-editor/relationship-section.tsx>`_.


We make use of ``relationshipEditorVisible`` flag to toggle the Relationship editor modal. Within the relationship editor modal, there are two fields:

**Entity Select field** : The *renderEntitySelect* function deals with this field. Here ``baseEntity`` is the entity which is being edited. The ``EntitySearchFieldOption`` allows us to search for any existing entity which we would like to link to our current *baseEntity*.

We can apply some additional filters to our search, so as to the optimize search results. For example, in case of a Series entity of type X, we dont need to display search results with entities which are not of the same type.
When we select an entity from the Search results, it gets stored as ``targetEntity``.

**RelationshipType Select field** : After selecting a targetEntity, we make use of a function called ``generateRelationshipSelection`` which takes our relationshipTypes object which was passed as a prop to our entity-editor, the baseEntity, and the targetEntity.
This function returns all combinations of relationship types which are valid between the two entities. We can then select the Relationship Type for our entity using the RelationshipSelect field in the editor. This sets the value of ``relationship`` and ``relationsipType`` of our state.

When we click on Add, we pass the ``relationship`` object to the following action:
::
    let nextRowID = 0;
    export function addRelationship(data: Relationship): Action {
        return {
            payload: {data, rowID: `n${nextRowID++}`},
            type: ADD_RELATIONSHIP
        };
    }

Here the data is the ``relationship`` object we passed from our Relationship Editor props. In the payload for this action, we also pass a ``rowID``, which is the index of the relationship in the array of relationships for that entity.
This is then added to the ``relationships`` object, with the ``rowId`` acting as a key for the mapping.


As we keep on adding relationships, they are rendered as a list on the entity-editor with the help of the aforementioned RelationshipList component. We can still edit and remove these relationships from the list.
If we click the edit button next to a particular relationship, it opens up the relationship modal with the ``relationship`` object passed as prop to the relationshipEditor.


Submission Section
---------------------
When we click on submit, the entire state of the form(rootState) is sent to an appropriate *createHandler* (``{entityType}/create/handler``). This route makes use of a utility function  `makeEntityCreateOrEditHandler <https://github.com/metabrainz/bookbrainz-site/tree/master/src/server/helpers/entityRouteUtils.tsx>`_ which returns a handler function ``handleCreateOrEditEntity`` adapted to the entity type.

The *rootState* is then validated using the validators for that entity type.
After this, the *rootState* is manipulated to be in a certain format by the ``transformNewForm`` function in each entity's route definitions file (``src/server/routes/entity/{entityType}``).

This formatted form is then passed to the aforementioned ``handleCreateOrEditEntity`` function which takes care of creating and modifying the required ORM models [:doc:`ORM`] (including other entities i.e. for relationships) and persisting the changes to the database.

If all the above steps are successful, the user is redirected to the Display Page of the newly created entity.

