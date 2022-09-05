######################
The Unified Form
######################

Introduction
==============
The main problem with the entity editor is that it becomes hard for new users
to add new entities without prior knowledge of bookbrainz. This is because the entity editor mostly mirrors the entities we have in bookbrainz schema on the backend with bare-bones abstraction.


To overcome the issue we introduced a new editor for entity creation called unified form. as its name suggests it unifies all workflows of an entity creation into a simpler book interface.
This editor is built upon existing entity-editor components and supports most of the features of entity-editor.

.. important:: To understand the working of this editor, you first need to understand how :doc:`/docs/entity-editor`  editor works.

Overview
=========
This Editor is divided in such a way that each tab corresponds to a section of a book (except the last one).

These tabs are -

* `Cover Tab`: This includes attributes that can be found on the book's cover like the author(s), publisher(s), etc.
* `Content Tab`: This includes works (or series) that are contained inside a book like introduction, forward, etc.
* `Details Tab`: Other details relevant to a book like width, weight, etc.
* Submit Tab: Use for reviewing newly created entities and adding submission notes.

Most of the components are reused directly from the entity editor, and all client code for this editor is located inside the ``/src/client/unified-form`` folder. 
The folder is further divided into sub-folders corresponding to each tab described above. 

Redux Store
===========
This editor also makes use of redux to manage its state similar to the entity editor.
The core entity state is the same except with little modifications to support the editor's special features.

Let us see what the editor state looks like-

.. code-block:: javascript

    {
            Authors: authorsReducer,
            EditionGroups: editionGroupsReducer,
            Editions: newEditionReducer,
            ISBN: ISBNReducer,
            Publishers: publishersReducer,
            Series: seriesReducer,
            Works: worksReducer,
            aliasEditor: aliasEditorReducer,
            annotationSection: annotationSectionReducer,
            authorCreditEditor: authorCreditEditorReducer,
            authorSection: authorSectionReducer,
            autoISBN: autoISBNReducer,
            buttonBar: buttonBarReducer,
            editionGroupSection: editionGroupSectionReducer,
            editionSection: editionSectionReducer,
            entityModalIsOpen: entityModalIsOpenReducer,
            identifierEditor: identifierEditorReducer,
            nameSection: nameSectionReducer,
            publisherSection: publisherSectionReducer,
            relationshipSection: relationshipSectionReducer,
            seriesSection: seriesSectionReducer,
            submissionSection: submissionSectionReducer,
            workSection: workSectionReducer
    }


It might look complicated at first but if you look closely you will notice how similar it is to the old entity-editor.

The core entity of this editor is edition but we keep other entity(s) sections handy to support their creation using modal.

Let's understand each new state in detail.


===========
Inline ISBN
===========
This is new in this editor since ISBN is the single most useful thing to uniquely identify a book. We decided to keep it separate inside the cover tab.

* ``ISBNReducer`` is responsible for handling two basic actions on the ISBN field that is updating field value and its type.

* ``autoISBNReducer`` handles toggling of autoISBN state, which is responsible for adding other ISBN to the identifiers.

======================
Inline Entity Creation
======================
One major highlight of this editor is that it supports inline entity creation that is a user can create new entities on the fly while editing.
This is achieved by keeping track of all entity states and switching between them as needed.

Most of the code related to swapping of entities is located inside ``src/client/unified-form/helpers.ts``, in which we make use `cross slice reducer <https://redux.js.org/usage/structuring-reducers/beyond-combinereducers>`_ to load/dump entity state. 

Let us understand it by an example,
suppose we want to select some work that doesn't exist so we choose the `create new work` option.
This will trigger the ``DUMP_EDITION`` action, which will save the main edition state to ``Editions``.
Followed by the opening of a modal that has some attributes already filled like names, languages, etc.
On hitting the submit button, It will call the ``postUFSubmission`` subroutine which will also process the state before posting it to the server.
``transformFormData`` is responsible for segregating this raw entity state into valid entities(either new or modified) as supported by ``/create/handler``. \

After completion saved edition state is loaded back from `Editions` to the main state.

The editor will store the response received from the server inside its particular state (in our case its Works).
This stored response will later be used for showing a summary and adding relationships to other entities.


====================
Inline Series Editor
====================
Another new component we have is the inline-series editor, this automates the workflow of adding works to series (of work type).

This component makes use of the existing series section and adds some states on top of it to support this feature.

Mainly these new actions are introduced in series-section

.. code-block:: javascript

    export const REMOVE_ALL_SERIES_ITEMS = 'REMOVE_ALL_SERIES_ITEMS';
    export const ADD_BULK_SERIES_ITEMS = 'ADD_BULK_SERIES_ITEMS';
 
These actions do what you expect them to do, mainly adding or removing all items from the series section state.

After enabling the `Add work to series` checkbox,

* When a user selects an existing series from series-editor, all the corresponding series items are fetched from the server to hydrate the series section.
  This is done by calling addBulkSeriesItems with new series item data.

.. code-block:: javascript
    
    /**
    * Produces an action indicating that the new series items should replace the old ones.
    *
    * @param {Object} seriesItems - The series items object to be added.
    * @returns {Action} The resulting ADD_BULK_SERIES_ITEMS action.
    */

    export function addBulkSeriesItems(seriesItems: Record<string, string>): Action 


* When the user adds new work in the content tab, it will trigger an action of type ``ADD_SERIES_ITEM`` with proper data to add the selected work in the series.

.. note:: All relationships/links are delayed till the final submission of the form, in contrast to inline entity creation which gets created as soon as we submit the modal.

* When the user disables the `Add work to series` checkbox, it will call removeAllSeriesItems to reset the series section state.

.. code-block:: javascript

    /**
    * Produces an action indicating that all series items should be removed.
    *
    * @returns {Action} The resulting REMOVE_ALL_SERIES_ITEMS action.
    */
    export function removeAllSeriesItems(): Action


Summary Tab
===========
If all other tabs have valid data, the user will then be able to see the summary of created entities in the summary tab.

.. note:: Currently there's no preview available for modified entities

Each entity state gets transformed into stringified form before displaying on the card.
The ``renderField`` method is responsible for this transformation though currently only basic fields get transformed like languages, series items, etc.

