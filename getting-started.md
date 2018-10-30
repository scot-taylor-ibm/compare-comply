---

copyright:
  years: 2017, 2018
lastupdated: "2018-10-29"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Getting started
{: #getting_started}

In this short tutorial, we introduce IBM Watson&reg; Compare and Comply and go through the process of classifying a contract to identify component pieces, their nature, the parties affected, and any identified categories.

## Before you begin
{: #before-you-begin}

- Create an instance of the service:
    1.  Go to the [Compare and Comply page ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/catalog/services/compare-comply){: new_window} in the {{site.data.keyword.Bluemix_notm}} Catalog.
    1.  Sign up for a free {{site.data.keyword.Bluemix_notm}} account or log in.
    1.  Click **Create**.
- Copy the credentials to authenticate to your service instance:
    1. From the [{{site.data.keyword.Bluemix_notm}} dashboard](https://console.bluemix.net/dashboard/apps), click on your Compare and Comply service instance to go to the Compare and Comply service dashboard page.
    1.  On the **Manage** page, click **Show** to view your credentials.
    1.  Copy the `apikey` and `url` values.

    In some instances, you authenticate by providing basic authentication. If you see `username` and `password` in the credentials, use those values instead of `"apikey":"{apikey_value}"` in the examples in this tutorial.
    {: tip}

## Step 1: Identify content
{: #identify_content}

Identify appropriate documents to analyze. Compare and Comply has been designed to analyze contractual <!-- and regulatory -->documents that meet the following criteria:

- Files to be analyzed are in PDF format.
- Both programmatic and scanned PDF files are supported. Files that have been scanned and processed by an optical character reader (OCR) are also supported.
  
  You can now submit PDF files that have been scanned and processed by an optical character reader (OCR).
  {: tip}

- Files can be up to 1.5 MB in size when submitted to the service with individual methods. If you submit files through the [`/v1/batches` interface](/docs/services/compare-comply/batching.html#batching), files can be up to 50 MB in size.
- Secure PDFs (with a password to open) and restricted PDFs (with a password to edit) cannot be processed.

## Step 2: Classify a contract's elements
{: #parse_contract}

In a `bash` shell or equivalent environment such as Cygwin, use the `POST /v1/element_classification` method to classify your contract. The method takes the following input parameters:
  - `version` (**required** `string`): A date in the format `YYYY-MM-DD` that identifies the specific version of the API to use when processing the request.
  - `file` (**required** `file`): The input file that is to be classified
  - `model` (optional `string`): If this parameter is specified, the service runs the specified type of element classification. Currently, the only supported value is `contracts`.

Replace `{apikey_value}` with the API key you copied earlier and `{PDF_file}` with the path to the PDF to parse.

```bash
curl -X POST -u "apikey":"{apikey_value}" -F "file=@{PDF_file};type=application/pdf" https://gateway.watsonplatform.net/compare-comply/api/v1/element_classification?version=2018-10-15
```
{: pre}

The method returns a JSON object that contains:

  - [A `documents` object](#documents) that includes the input document's title,  an HTML version of the input document, and the MD5 hash of the input document.
  - Information about the model used to classify the input document.  
  - [An `elements` array](#elements) that details semantic elements identified in the input document.
  - [A `tables` array](#tables) that breaks down the tables identified in the input document.
  - [A `document_structure` object](#doc_struct) that lists section titles and leading sentences identified in the input document.
  - [A `parties` array](#parties) that lists the parties, roles, addresses, and contacts of parties identified in the input document.
  - [Arrays defining `effective_dates` and `contract_amounts`.](#other_arrays)

## Step 3: Review the analysis
{: #review_analysis}

This section provides a high-level overview of the output of the `POST /v1/element_classification` method, focusing on the major sections. See [Understanding the output schema](/docs/services/compare-comply/schema.html#output_schema) for a detailed discussion of the method's output.

### Documents
{: #documents}

The `documents` object provides basic information about the input document.

### Elements
{: #elements}

Each object in the `elements` array describes an element of the contract that Compare and Comply has identified. The following code represents a typical element:

```
{
    "location" : {
      "begin" : 134323,
      "end" : 135109
    },
    "text" : "9. In the event that the Participant's total vested account balance is determined to be less than or equal to $2,000.00 as of the date that the Order is received, the parties will be informed in writing that the QDRO determination fee may potentially liquidate the account.",
    "types" : [ {
      "label" : {
        "nature" : "Obligation",
        "party" : "All Parties"
      },
      "provenance_ids" : ["Nlu0ogWAEGms4vjhhzpMv3iXhm8b8fBqMBNtT/bXH8JI=", "PlyERkjg5is36RpFjVUFXp69eDmGmCxLCXRs1sDMDUCo="
      ]
    } ],
    "categories" : [ {
      "label" : "Communication",
      "provenance_ids" : [ "Cs38YyU6VBFtJK1/bgtEJBlqqWmX5F6OYUciRxQXf7HrN5TOCPuI7QXbkbj4LRXoxVuB3/i9H15q5TU+vFxorhUBeWFfF998OYQiPYViD2yI="
      ]
    } ],
    "attributes" : [ {
      "type" : "Currency",
      "text" : "$2,000.00",
      "attribute" : {
        "begin" : 134780,
        "end" : 134789
      }
    } ]
}
```
{: screen}

Each element has five important sections:
  - `location`: The `begin` and `end` indexes indicating the location of the element in the input document.
  - `text`: The text of the classified element.
  - `types`: An array that includes zero or more `label` objects. Each `label` object includes a `nature` field that lists the effect of the element on the identified party (for example, `Right` or `Exclusion`) and a `party` field that identifies the party or parties affected by the element. See [Types](/docs/services/compare-comply/parsing.html#contract_types) in [Understanding contract parsing](/docs/services/compare-comply/parsing.html#contract_parsing) for additional information. 
  - `categories`: An array that contains zero or more `label` objects. The value of each `label` object lists a functional category into which the identified element falls. See [Categories](/docs/services/compare-comply/parsing.html#contract_categories) in [Understanding contract parsing](/docs/services/compare-comply/parsing.html#contract_parsing) for additional information. 
  - `attributes`: An array that lists zero or more objects that define attributes of the element. Currently supported attribute types include `Location` (geographic location or region referenced by the element), `DateTime` (date, time, date range, or time range specified by the element), and `Currency` (monetary values and units). Each object in the `attributes` array also includes the identified element's text and location; location is defined by the `begin` and `end` indexes of the text in the input document. See [Attributes](/docs/services/compare-comply/parsing.html#attributes) in [Understanding contract parsing](/docs/services/compare-comply/parsing.html#contract_parsing) for additional information.
  
Additionally, each object in the `types` and `categories` arrays includes a `provenance_ids` array. The values listed in the `provenance_ids` array are hashed values that you can send to IBM to provide feedback or receive support about the part of the analysis associated with the element.

**Note**: Some sentences do not fall under any type or category, in which case the service returns the `types` and `categories` arrays as empty objects.

**Note:** Some sentences cover multiple topics, in which case the service returns multiple sets of `types` and `categories` objects.

**Note**: Some sentences do not contain any identifiable attributes, in which case the service returns the `attributes` array as empty objects.

### Tables
{: tables}

The `tables` array details the structure and content of any tables found in the input document. See [Classifying tables](/docs/services/compare-comply/tables.html#understanding_tables) and [Understanding the output schema](/docs/services/compare-comply/schema.html#output_schema) for details.

### Document structure
{: #doc_struct}

The `document_structure` object identifies the section titles and leading sentences of the input document. See [Understanding document structure](/docs/services/compare-comply/doc_structure.html_#doc_struct) for details.

### Parties
{: #parties}

The `parties` array lists available information about parties affected by the input document, including the party's name, role, address or addresses, and contacts. See [Parties](/docs/services/compare-comply/parsing.html##contract_parties) in [Understanding contract parsing](/docs/services/compare-comply/parsing.html#contract_parsing) for additional information.

```
  "parties": [
      {
      "party": "Wolfbone Investments, LLC",
      "role": "Supplier",
      "addresses": [],
      "contacts": [
        {
         "name": "Will Smith",
         "role": "business contact"
        },
        ...
      ]
    },
    {
      "party": "Torchlight Energy, Inc.",
      "role": "Buyer",
      "addresses": [
       {
       "text": "5700 W. Plano Pkwy., Ste. 3600, Plano, Texas 75093",
       "location": {
          "begin": 150,
          "end": 200
        }
       },
       ...
      ],
      "contacts": [ ]
    }
  ]
```
{: screen}
  
### Other arrays
{: #other_arrays}

The following arrays provide useful information about the input document. Each of the arrays contains zero or more objects that list the `text` in which the information was identified and the `location` of that text as defined by the text's `begin` and `end` indexes.

  - The `effective_dates` array lists any effective dates identified in the input document.
  - The `contract_amounts` array lists monetary amounts specified by the input document.

## Next steps
{: #next_steps}

You have successfully classified a contract to identify its elements, tables, structure, parties, and other information. You can use the analysis to quickly understand and enforce the classified contract. The next steps are:

 - [Understanding contract parsing](/docs/services/compare-comply/parsing.html#contract_parsing)
 - [Understanding the output schema](/docs/services/compare-comply/schema.html#output_schema) and [classifying tables](/docs/services/compare-comply/tables.html#understanding_tables).


