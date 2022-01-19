#################
BookBrainz ORM
#################

Overview
========

BookBrainz uses an `ORM tool (Object-Relational Mapping) <https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping>`_
to interface with the database. What that means in practice is the we we import a separate Javascript package
(hosted in `this repository <https://github.com/metabrainz/bookbrainz-data-js>`_) in the webserver and use its methods
to work with objects that represent the data rather than directly with raw database rows.

That allows us to load an entity with associated data (for example an Author entity and its aliases), and the ORM will compose the sequence
of SQL queries to fetch the relevant data.
The ORM will return an Author Javascript object with an array under ``myAuthorObject.aliasSet.aliases``

The ORM we use is called `Bookshelf.js <https://bookshelfjs.org/>`_ (no relation to BookBrainz, the name similarity is just a coincidence!).
If you ever get lost in the syntax of the ORM, you will find the `documentation here <https://bookshelfjs.org/api.html>`_.
With it we define 6 main entity models (one per entity, see :ref:`entities-description`) as well as a model for each of the schema concepts
we need to work with (revisions, sets, relationships, author credits, etc.).
In addition we export some utilities to deal with common operations to work with the models.


The ORM is set up, connected to the database, and then stored in the webserver application session `at startup <https://github.com/metabrainz/bookbrainz-site/blob/0fa4a0198f915bed836833980fc85824a36e99db/src/server/app.js#L51>`_
That way it is accessible at any time in the ExpressJS server requests (i.e. in each server route).
In a standard ExpressJS route ``router.get('/:id', (req, res, next) => {…`` you will find it in ``req.app.locals.orm``.

Whenever you see in our codebase an ``orm`` passed as an argument to a function, you can expect to find the whole BookshelfJS ORM as exported by the package.
The list of models and components exported by the package can be found `here <https://github.com/metabrainz/bookbrainz-data-js/blob/a73fffc8c566f51f6a30466f1e60efa28d4f5f63/src/index.ts?_pjax=%23js-repo-pjax-container%2C%20div%5Bitemtype%3D%22http%3A%2F%2Fschema.org%2FSoftwareSourceCode%22%5D%20main%2C%20%5Bdata-pjax-container%5D#L116>`_.


Entities and their revisions
============================

Following our :ref:`schema`, there are important concepts to understand when working with the ORM models.
We have a generic `Entity model <https://github.com/metabrainz/bookbrainz-data-js/blob/master/src/models/entity.js>`_ which represents an entity of unknown type.
It is described by a BBID (unique identifier) and the ID of the latest revision that describes its current info.

From that entity header we can get an entity type, and using the `getEntityModelByType <https://metabrainz.github.io/bookbrainz-site/global.html#getEntityModelByType>`_
utility we can retrieve the corresponding ORM model.


There is also a generic Revision model as well as a more specific revision model for each entity type (i.e. AuthorRevision, WorkRevision, etc.)
Neither of these directly contain the data associated with that revision, but they do provide a way to get from one to the other.
The Revision holds information about who created the revision, notes associated with it, as well as the parent and children revisions that might come before and after it.

The AuthorRevision can be fetched along with the associated ``data`` and allows us to compare different AuthorRevisions

Entities and their data
=======================

When we merge entities, their BBIDs get redirected to the entity we merged into.
To ensure we are targetting the correct entity, we can use the `recursivelyGetRedirectBBID utility <https://metabrainz.github.io/bookbrainz-data-js/global.html#recursivelyGetRedirectBBID>`_:
``const redirectBbid = await orm.func.entity.recursivelyGetRedirectBBID(orm, relEntity.bbid, null);``

To get an entity's current data (i.e. at the last revision) using its BBID, we can forge a new instance of the entity model
(let's take the Author model for example):
``const myAuthor = await model.forge({bbid: redirectBbid}).fetch({withRelated: ['defaultAlias', 'disambiguation']});``
The myAuthor object will have the content of the AuthorData model as well as the defaultAlias and disambiguation that we required.

The ``orm.func.getEntity`` method does just that for you!


Transactions
============

Sometimes, we want to apply a sequence of operations, and only apply them if all of the operations succeed.
For example, when deleting entity A which has links to entity B, we want to modify the relationships
of entity B to remove the A<->B relationship from its RelationshipSet.
If deleting entity A fails for some reason, we want to abort all the operations.
This concept of `Atomicity in SQL <https://www.tutorialspoint.com/sql/sql-transactions.htm>`_ uses transactions to commit or rollback operations.
In the ORM, we can create a ``transaction`` object at pass it around when calling methods on the model to ensure atomicity.
This is done using the ``orm.bookshelf.transaction`` `method <https://bookshelfjs.org/api.html#Bookshelf-instance-transaction>`_