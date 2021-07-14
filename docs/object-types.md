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
This is a page object as it's defined by the key `/Type`. There are many of this and that's what we are going to focus on in this article, we are going to learn which types exist, what they do and what other keys they accept because of this.

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




