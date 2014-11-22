An entity can be described as some concrete object or abstract notion which
BookBrainz stores. More specifically, an entity is any object in the BookBrainz
schema which is associated with a globally-unique identifier (GID). There are
several different types of entity, and more may be defined in future to fit the
needs of the community.

Tables
======

All entity GIDs are stored in a single table in the database, **entity**, along
with a field describing the type of entity referred to.

A second table, **entity_redirect**, allows redirection of GIDs. For example, if
one entity was merged into a second entity, then a row would be created in the
entity_redirect table to indicate that a mapping to the merge target.
