This repository contains the language specification for LayoutScript.

#

LayoutScript is a domain-specific language (DSL) designed to streamline the process of interacting with electronic resources by abstracting away technical complexities. With LayoutScript, users can define steps to read content from various sources such as spreadsheets, web pages, and emails in a human-readable format, resembling a step-by-step guide rather than traditional computer code. By providing a more intuitive and accessible approach, LayoutScript simplifies tasks involving electronic resources, reduces the need for technical expertise, and enhances productivity for users across various domains.


Terminology used in this document and other places that refer to LayoutScript.

| Term | Meaning
| ---- | -------
| `address` | The locator or identifier used to locate specific content within a given context. This could be an XPath expression, a CSS selector (commonly used for web content), or another type of selector depending on the type of resource being accessed.
| `content` | The actual information or data that is being targeted or retrieved using the provided address. This could include text, images, audio/video, links, or any other type of data that is relevant.
| `context` | The environment or setting in which both the content and the address exist.


The anatomy of an LayoutScript file consists of a list of directives. Each directive is typically placed on a separate line within the file. The first line must always contain the `layout` directive, followed by any additional directives as needed. The delimiter (or separator) between arguments is `<tab>`.

```
first line  —  | layout <tab> "engine name"
               |  ^
               |   \ 
               |    must begin with this
               |
               |
           /¯  | "directive" <tab> "first argument" <tab> "second argument" [<tab> ...]
next lines   [...]
           \_  | "directive" <tab> "first argument"
               |      ^
               |       \
               |        directives are part of this
               |        language specification
               |
commented   —  | # this is a comment
               | ^
               |  \ 
               |   lines that start with this character should be ignored
```

The quotes in the sketch above are used solely to denote the placeholders. In a LayoutScript file, quotes will be included as part of the arguments, forming part of the text itself.

#

Version 1.0 of the languages provides the following directives to cover working with HTML documents and resources that are adjacent to it. The abbreviation of FA denotes the first argument; SA, second argument.

Document:
- `layout` is a reserved keyword to denote the begining of a LayoutScript
- `prompt` attempts to delegate the content as an argument to FA as a function
- `label` classifies the last evaluated content as FA

Text:
- `extract` outputs the first captured group from a given RegExp as FA
- `replace` outputs a new text by replacing the text of FA with the text of SA
- `remove` outputs a new text by removing the text of FA
- `insert` outputs a new text by inserting the given placeholder FA in SA
- `glue` outputs a new text by inserting the given FA as a prefix
- `keep` filters (keeps) by a given RegExp as FA
- `drop` filters (drops) by a given RegExp as FA

HTML:
- `bundle` changes the context on which `follow` and `select` perform
- `follow` outputs a list of elements that match the given address of XPath as FA
- `select` outputs a list of elements that match the given address of CSS selector as FA

HTML/CSS:
- `style` extracts the value of the CSS property as FA

#

Example:

```
layout  vivid 1.0

# this is a comment... it can be useful
# or not, depending on what it's writen
select  h1
label   title
extract ([0-9]+)
label   title year
replace [0-9]+  20XX
# label can be used a few more times to
# also store the last evaluated/outputs
insert  ~   This "~" is the page title
label   observation
glue    New:<space>

# get css properties for the headings
select  h1
style   font-family
label   link font
style   color
label   link color

# to focus on elements that are groupped
# together...
bundle  //div[@class="article"]
follow  h1[contains(@class, "title")]/a/@href
keep    \.png$
label   link
follow  a/@target
drop    (_blank|_top)
label   link target

# call another procedure
prompt  rpc-call
prompt  3rd-party-api
prompt  subprocess.sh
```
