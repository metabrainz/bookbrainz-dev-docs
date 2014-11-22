Introduction
============
The BookBrainz schema describes how the data used by BookBrainz is stored. It's
quite important to have a good idea of this before you look at any of our code.
If you're coming from a MusicBrainz background, our schema is similar, but there
are some key differences - see [Coming from MusicBrainz](musicbrainz.md).

Definition
==========
The BookBrainz schema is defined in the bbschema node package, which you can
access at https://github.com/BookBrainz/bbschema. When the schema is more
stable, there'll be a nice schema diagram here to help you understand how things
fit together, but for now, you'll have to make do with some words.

There are several core concepts to understand when considering the schema:

- [Entities](entities.md)
