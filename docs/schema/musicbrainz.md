This page describes the key differences between the BookBrainz and MusicBrainz
schemas, for developers already familiar with the MusicBrainz schema.

Entity Base Object
==================
In BookBrainz, there is an "Entity" base object. What this means is that all
entities share some common data, and store this data in a single table in the
database. This greatly simplifies the rest of the database compared to
MusicBrainz - instead of having a set of tables defined for each type of entity,
we only need a single set of tables referencing the base table for entities.

The following fields are stored in the base table:

* gid
* master_revision_id
* last_updated
