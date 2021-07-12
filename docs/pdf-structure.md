# The PDF structure

At a very simplified level PDFs are composed of 3 parts:
1. The header
2. The body
3. The footer

## The header

The header it self is very simple: it's composed of 2 lines, the first one contains the PDF version number (although most programs will ignore this and attempt to open any pdf file), the second one is some random bits of data with value over 127 which are used to make sure legacy applications understand that this is not a text file and so it prevents corruption of the file when, for example, it get transfered over FTP in text mode.
Here is an example:
```pdf
%PDF-1.7
%����
```
This is telling us that the pdf we are reading should be complainant with the 1.7 specification, of which you can find [the official documentation provided by adobe (and now archived on archive.org) here](https://archive.org/details/pdf1.7). This is great reference after you understood the basics of the file format.
As you can see both lines are preceded by a `%` symbol, this is a comment, much like the `//` in JavaScript(any many other languages) or `#` in python. Generally this lines are ignored by the PDF viewers, with the exeption of the first line (with the file version as we said above) and the last line (the end of file `%%EOF`, read more below).

## The body

The body is composed of a series of objects defined by an `obj` and an `endobj` keyword. This is what actually contains the data to display on the PDF. Objects are linked together by an *object number* and a *generation number*(most of the time 0), which precede the `obj` keyword. 
Here is an example of a page object, with some explanation:
```pdf
4 0 obj % Defining Object 4 of generation 0
<<  /Type /Page % Defining Object as a Page Object
    /MediaBox [0 0 612 792] % Defining the page size
    /Resources 3 0 R % Defining the resources for this page(images, fonts, ...)
    /Parent 1 0 R % Defining the parent object
    /Contents [4 0 R] % Defining the contents of the page(text, art, ...)
>>
```
Many of the things in this example will get clearer as you continue reading this blog. After finishing reading this article you might be interested in reading about [data types in PDFs](datatypes).

Ultimately the body of a pdf is composed of a tree of objects linked together. At the base of everything there is a **Root Object**, referenced by the trailer in footer (read more about this below) which then references all the other objects of the document.

## The footer
The footer is ironically one of the first things to be read in the PDF. It contains the *Cross-reference table*, which contains a list of offsets, which are used by PDF viewer as a lookup table to quickly know where to look for certain content when needed. To put it simply it's like a phone book of all the objects of the document, it starts with a starting offset and a lenght, then it has a special entry `0000000000 65535 f` which never changes, and then a list of all the offets that count the bytes from the beginning of the document till the object the PDF viewer is looking for. It's harder to explain in words than just showing you so here is an example of a Cross-reference table:
```pdf
xref % marks the beginning of the Cross-reference table
0 8 % 8 entries in the table, starting from the object 0
0000000000 65535 f % The special entry
0000000015 00000 n % Offset of object 0 is 15
0000000226 00000 n % Offset of object 1 is 226
0000000074 00000 n % Offset of object 2 is 74
0000000125 00000 n 
0000000454 00000 n 
0000000553 00000 n 
0000000711 00000 n 
trailer % Marks the beginning of the trailer, thus the ending of the Cross-reference table
```
As you can see offsets don't have to be linear, they can jump back and forth. The offset is a 10 long zero-filled number, while the generation number is a 5 long sero-filled number.

The next section is the trailer, preceeded by the keyword `trailer` ending with the `startxref` keyword. This contains reference to the root object, info/metadata object and a couple other information.
Last there is the offset referencing the beginning of the Cross-reference table and lastly the `%%EOF` marking the end of the pdf.
Here is an example of a full pdf footer:
```pdf
xref
0 8
0000000000 65535 f 
0000000015 00000 n 
0000000226 00000 n 
0000000074 00000 n 
0000000125 00000 n 
0000000454 00000 n 
0000000553 00000 n 
0000000711 00000 n 
trailer
<<
/Root 3 0 R
/Size 8 % This is the number of entries in the Cross-reference table
>>
startxref
1181 % Bythe offset of the Cross-reference table
%%EOF
```

## But why go trough all this trouble to display a document?
You might be kind of confused on why there is a need for all this object tree and byte offset mess, and tbh it's fair, so why are we doing this? Welp, PDFs are made for speed and memory efficiency, a PDF viewer want to avaoid loading the full document in memory because of limited resources (especially in older machines this can't be taken for grnated), so by having a Cross-reference table and all this separate objects a PDF viewer can start reading the document from the bottom, see what it needs to load from the drive and load only that to display on the screen. I hope that explains it!








