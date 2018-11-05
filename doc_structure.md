---

copyright:
years: 2018
lastupdated: "2018-08-29"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Understanding document structure
{: #doc_struct}

The output of the `POST /v1/element_classification` method includes a `document_structure` object that details the structural composition of the input document. See [Understanding the output schema](/docs/services/compare-comply/schema.html#output_schema) for the placement of the `document_structure` information in the output of the method.

## Document structure output
{: #struct_output}

Document structure information is represented in the output as follows. The object is located immediately after the top-level `elements` array.

```
    "document_structure": {
        "sections": [  
           {
               // One entry per Section title. The contained list of `sentences` 
               // contains all sentences (the span of the sentences, not the 
               // text) *directly* inside that Section.
               // A sentence can appear at most once inside a section.
               "title_text": "...",
               "title": { "begin": int, "end": int }
               "level": int
               "sentences": [
                   { "begin": int, "end": int },
                   ...
               ] 
           }
        ],
        "leading_sentences"": [
           {
               // Same as `sections`, but for each section's leading sentence. 
               // Only the span of the contained sentences is kept.
               "sentence_text": "...",
               "sentence": { "begin": int, "end": int },
               "sentences": [
                   { "begin": int, "end": int },
                   ...            
               ]
           }
        ]
    }
```
{: screen}

## Document structure elements
{: #struct_elements}

The elements of the `document_structure` object contain the following information:

  - `document_structure`: A JSON object describing the structure of the input document.
    - `sections`: A JSON array containing one object per section or subsection detected in the input document. Sections and subsections are not nested; instead, they are flattened out and can be placed back in order by using the `begin` and `end` values of the element and the `level` value of the section. Each object in the `sections` array includes a list of zero or more `sentences` that are directly under the title of the detected section.
      - `title_text`: A string listing the section title, if detected.
      - `title`: A JSON object listing the `begin` and `end` values of the section title.
      - `level`: An integer indicating the level at which the section is located in the input document. For example, `1` represents a top-level section, `2` represents a subsection within the level `1` section, and so forth.
      - `sentences`: A JSON array containing objects that specify the `begin` and `end` values of the sentences in the section.
    - `leading_sentences`: A JSON array containing one object per section or subsection, in parallel with the `sections` array, that details the leading sentences in the matching section or subsection. As in the `sections` array, the objects are not nested; instead, they are flattened out and can be placed back in order by using the `begin` and `end` values of the element or any level markers in the input document.
      - `sentence_text`: The text of the section's leading sentence.
      - `sentence`: A JSON object indicating the `begin` and `end` values of the leading sentence's location in the input document.
      - `sentences`: A JSON array containing objects that specify the `begin` and `end` values of the sentences that follow the leading sentence.
