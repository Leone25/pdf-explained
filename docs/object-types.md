# Object Types in PDFs

As we've discussed in [the PDF structure](pdf-structure), a pdf is at its core composed on many objects which contain the data to be displyed. This object are then linked together through [indirect references](data-types#indirect-references).

At a minimum an object starts with a `obj` keyword, preceeded by its object number and generation number, then contains a [dictionary](data-types#dictionaries) and lastly it ends with the `endobj` keyword. Below there is a example of an object.
```pdf
4 0 obj % Defining Object 4 of generation 0
<<  /Type /Page % Defining Object as a Page Object
    /MediaBox [0 0 612 792] % Defining the page size
    /Resources 3 0 R % Defining the resources for this page(images, fonts, ...)
    /Parent 1 0 R % Defining the parent object
    /Contents [4 0 R] % Defining the contents of the page(text, art, ...)
>>
```
This is a page object as it's defined by the key `/Type`. There are many functions an object could perform, we are going to learn which types exist, what they do and what data they contain.

### Index
 - [Trailer](#trailer)
 - [Metadata Object](#metadata-object)
 - [Document Object](#document-object)
 - [Pages and Page Object](#pages-and-page-object)
 - [Content Object](#content-object)

## Trailer
Ironically we are going to start with something that is as far as possible from a standard object as we can go, we are speaking of course of the trailer. As we discussed in [the PDF structure](pdf-structure), this are located at the bottom of the pdf, it's not defined by any `obj` keyword and it doesn't have a `/type` key. It's composed of a single dictionary whoose keys and values are summerized in the following table.
Key | Value type | Value
------------ | ------------- | -------------
`/Size` | Integer | (Required) The number of total entries in the [cross-reference table](pdf-structure#the-footer)
`/Root` | Indirect reference | (Required) Indirect reference to the document object (more about this later)
`/Info` | Indirect reference | Indirect reference to the metadata object (more about this later)
`/ID` | Array of 2 strings | Used by programs to uniquely identify files within a workflow. The first one is given when the file is generated at first, the second is given by the program that is modifying the PDF
```pdf
<<
  /Size 420
  /Root 69 0 R
  /Info 426 0 R
  /ID [(Hello) (World)]
>>
```

## Metadata Object
One of the object referenced in the trailer is the info object, which basically contains metadata about the PDF, who made it, when, ecc. This data is to be used for cataloging by external programs and it's generally not displayed to the user. The content of this object can be summarized in the following table.
Key | Value type | Value
------------ | ------------- | -------------
`/Title` | String | This is the title of the document
`/Subject` | String | This is a small summary of the PDF
`/Keywords` | String | Keywords for this document, no specific data structure is provided
`/Author` | String | The name of the author of the document
`/CreationDate` | Date | The date when the document was first created
`/ModDate` | Date | The date when the document has been last modified
`/Creator` | String | The name of the program that has generated this document
`/Producer` | String | The name of the program that converted the document to PDF
```pdf
420 0 obj
<<
  /Title (Pdf explained)
  /Author (Enrico)
  /CreatingDate (D:20210805104817+02'00)
>>
endobj
```

## Document Object
The document object is one of the fondamental objects referenced in the trailer. It's the root of the page tree. It's content can be summarized in the following table.

Key | Value type | Value
------------ | ------------- | -------------
`/Type` | Name | (Required) `/Catalog`
`/Pages` | Indirect reference | (Required) This an indirect reference to the first pages node (more about this later)
`/PageLables` | Number tree | A number tree giving the page lables for the document
`/Names` | Dictionary | This maps objects to names, which can be useful to avoid to having to reference by object number every time
`/Dest` | Array | This maps locations of hyperlinks within a document
`/ViewerPreferences` | Dictionary | This contains a series of flags that the PDF viewer will use as default settings to display the file
`/PageLayout` | Name | Either `/SinglePage`, `/OneColumn`, `TwoColumnLeft`, `TwoColumnRight`, `/TwoPageLeft` or `/TwoPageRight`. If missing will be `/SinglePage`
`/Outline` | Dictionary | The outline dictionary
`/Metadata` | Indirect reference to stream | Stream will contain the XMP metadata

Remember that what I'm showing here is just a couple of the available keys. If you want to learn more look into table 28 of the PDF spec.








