# Data Types in PDFs

## Integers and Real Numbers
An integer is composed of one or more decimal digits (0 through 9), optionally preceded by a plus or minus sign.
```pdf
0 +1 -1 69
```
A real number is composed of one or more decimal digits (0 trought 9), optionally preceded by a plus or minus sign, and containing a single decimal point `.`.
```pdf
0.1 .25 -0.007 4.20
```

**Be aware that exponential notation is not supported**

## Strings
A string is a series of bytes, written between parentesis.
```
(Hello World!)
```
The backslash `\` is used to escape special characters like the backslash it self and a parenthesis, or it can be used to mark special character sequences(see table below).
```pdf
(Hello \\ world \( but with escaped characters\))
```
Character sequence | Meaning
------------ | -------------
\n | Line feed
\r | Carriage return
\t | Horizonatal tab
\b | Backspace
\f | Form feed
\*ddd* | Display character from provided 3 octal digits

### Hexadecimal Strings
Strings can be also encoded as a sequence of hexadecimal digits between two `<` and `>` characters, with each pair rappresenting a byte. If the number of digits is odd, the last digit is assumed to be 0.
```pdf
<5044462061726520636f6f6c21> % Converts to 'PDFs are cool!'
```

## Names
Names are words preceded by a forward slash `/` and find many uses all troughout a document, but their most common use is as keys for Dictionaries (see below). They can contain `#xx` hexadecimal ASCII (like URLs) which will be converted by the reader on need.
```pdf
/Name
/Name#20with#20spaces % -> 'Name with spaces'
```

## Booleans
Denoted by the keywords `true` and `false`, they are exactly what you would expect them to be.

## Arrays
Arrays are a way to group other data in an ordered manner. The data doesn't have to be all of the same type and and array can contain another arrays.
```pdf
[0 0 600 800]
[/Red 42 [true (Hi!)]]
```

## Dictionaries
Dictionaries group a series of keywords, as names, relating them to other data in an unordered manner. They are denoted by `<<` and `>>` at the beginning and end. Like arrays dictionaries can contain other dictionaries and any other data type.
```pdf
<<  /Hello /World
    /Flag true
    /Numbers <<
        /One 1
        /Two 2
        /Three 3
    >>
    /Words [(Hi) (Hello) (Sup)]
>>
```

## Indirect References
As we said in [the PDF structure](pdf-structure), PDFs are made of a series of objects. To link this together we use indirect references. They are composed of the object number, followed by the generation number, ending with a capital `R`.
```pdf
6 0 R
```

## Streams
Streams are used to store binary data, like images and fonts, but also text and grapycs. Streams are made of a dictionary, followed by the `stream` keyword and then the `endstream` keyword. In between the `stream` and `endstream` keyword is where is the data at. The dictionary has to contain the `/Lenght` key with the lenght of the number of bytes of the stream. Additionally it can contain a `/Filter` key which mark which compression method has been used in the stream (which is almost always compressed); see the table below for more info. This can be combined, and have to be marked trough an array.
```pdf
4 0 obj
<<
/Length 64
>>
stream
1. 0. 0. 1. 50. 700. cm
BT
  /F0 36. Tf
  (Hello World!) Tj
ET
endstream
endobj
```
Method name | Description
------------ | -------------
`/ASCIIHexDecode` | Similar to hexadecimal strings, every hexadecimal pair encodes one byte of uncompressed data
`/ASCII85Decode` | This uses only 7 of the 8 bits of every byte of data to encode the data
`/LZWDecode` | Marks the stream to be using the Lempel-Ziv-Welch compression
`/FlateDecode` | Marks the stream to be using the open source zlib library
`/RunLengthDecode` | A simple byte-based run-lenght compressor
`/CCITTFFaxDecode` | Implements Group 3 and 4 encoding as used in fax machines
`/JBIG2Decode` | A modern alternative to `/CCITTFaxDEcode`; implements JBIG2 compression
`/DCTDecode` | JPEG lossy compression; a whole JPEG file may be put in this stream including headers
`/JPXDecode` | JPEG2000 loddy and lossless compression

