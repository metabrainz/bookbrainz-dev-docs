.. _userscripts: https://github.com/bookbrainz/bookbrainz-userscripts

#####################
Entity Editor Seeding
#####################

This page documents a list of parameters you can *POST* to the entity editor to “seed” it. All values are optional unless otherwise stated.

This feature is most useful for bookbrainz's import `userscripts`_.

All date's parameters mentioned below should be in format of YYYY-MM-DD eg. 2002-02-27.

Genric Sections
===============

This will include parameters for those sections which are common to all entities.

Name Section
************
* ``nameSection.name`` : string
        defaultAlias's name for Entity
* ``nameSection.sortName`` : string
        defaultAlias's sortName for Entity

* ``nameSection.language`` : string
        defaultAlias's language for Entity
* ``nameSection.disambiguation`` : string
        disambiguation for Entity

Identifier Editor
*****************
* ``identifierEditor.t{id}`` : string *(int id for that identifier)*
        adding one identifier row to identifierEditor
        
        eg. for isbn13 parameter would be ``identifierEditor.t9``
        

Annotation Section
******************

* ``annotationSection`` : string
        content for annotationSection
   
Submission Section
******************

* ``submissionSection`` : string
        note for submissionSection
 

Entity Specific
===============
This includes entity specific parameters.


Author Section
**************

* ``authorSection.type`` : string (Person | Group)
        author entity type

* ``authorSection.gender`` : string (Male | Female | Other)
        author entity gender
    
* ``authorSection.beginDate`` : string
        author entity date of birth

* ``authorSection.beginArea`` : string
        author entity place of birth

* ``authorSection.endDate`` : string
        author entity date of death

* ``authorSection.endArea`` : string
        author entity place of death


Work Section
************

* ``workSection.type`` : string  
        work entity type eg. Novel

* ``workSection.languages{i}`` : string (int i index of language)
        work entity multi-languages

Edition Section
***************
* ``editionSection.publisher`` : string
        publisher of the edition entity

* ``editionSection.releaseDate`` : string
        edition release date

* ``editionSection.languages{i}`` : string (int i index of language)
        edition entity multi-languages

* ``editionSection.format`` : string
        edition format eg. Paperback

* ``editionSection.x`` : number, x : (height | width | depth | weight | pages)
        edition format eg. editionSection.pages

EditionGroup Section
********************
* ``editionGroup.type`` : string
        edition-group entity type eg. Book
 

Series Section
**************
* ``seriesSection.orderType`` : string (Automatic | Manual)
        ordering type for series entity

* ``seriesSection.seriesType`` : string 
        series type for series entity eg. Work



Publisher Section
*****************
* ``publisherSection.type`` : string 
        publisher type for publisher entity eg. Distributer

* ``publisherSection.beginDate`` : string 
        date founded for publisher entity

* ``publisherSection.endDate`` : string 
        date dissolved for publisher entity

* ``publisherSection.area`` : string 
        area for publisher entity


Example Submission
==================

Basic form request body for seeding edition entity should look something like this

::

    {
	"nameSection.name": "Thinking,+Fast+and+Slow",
	"nameSection.sortName": "Thinking,+Fast+and+Slow",
	"nameSection.language": "English",
	"identifierEditor.t9": "978-0374533557",
	"identifierEditor.t10": "0374533555",
	"identifierEditor.t5": "0374533555",
	"editionSection.publisher": "Farrar,+Straus+and+Giroux",
	"editionSection.releaseDate": "2013-04-02",
	"editionSection.format": "Paperback",
	"editionSection.pages": "499",
	"editionSection.width": "37",
	"editionSection.height": "139",
	"editionSection.depth": "209",
	"editionSection.weight": "453",
	"submissionSection": "Imported+from+Amazon\r\nsource:+www.amazon.com/dp/0374533555\r\nscript:+amazon-import\r\nversion:+0.0.1+\r\n++++" 
    }
    
