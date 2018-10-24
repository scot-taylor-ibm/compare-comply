---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-24"

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
- The PDF contents are in text form. Documents that have been scanned cannot be parsed, even if the scans have been processed by an optical character reader (OCR).

  **Note:** You can identify a PDF that is in text by opening the document in a PDF viewer and using the **Text select** tool to select a single word. If you cannot select a single word in the document, the file cannot be parsed.

- Files are no larger than 50Mb in size.
- Secure PDFs (with a password to open) and editing restricted PDFs (with a password to edit) cannot be parsed.

## Step 2: Classify a contract's elements
{: #parse_contract}

In a `bash` shell or equivalent environment such as Cygwin, use the `POST /v1/element_classification` method to classify your contract. The method takes the following input parameters:
  - `version` (**required** `string`): A date in the format `YYYY-MM-DD` that identifies the specific version of the API to use when processing the request.
  - `file` (**required** `file`): The input file that is to be classified
  - `model` (optional `string`): If this parameter is specified, the service runs the specified type of element classification. Currently, the only supported value is `contracts`.

Replace `{apikey_value}` with the API key you copied earlier and `{PDF_file}` with the path to the PDF to parse.

```bash
curl -X POST -u "apikey":"{apikey_value}" -F 'file=@{PDF_file};type=application/pdf' https://gateway.watsonplatform.net/compare-comply/api/v1/element_classification?version=2018-08-24
```
{: pre}

The method returns a JSON object that contains:

 - An HTML conversion of the source PDF file.
 - The extracted document title.
 - An array of `elements` that define the component parts of the contract.
 - An array of `parties` that define entities that have been identified as parties.

## Step 3: Review the analysis
{: #review_analysis}

Each object in the `elements` array describes an element of the contract that Compare and Comply has identified. The following code represents a typical element:

```
{
  "sentence_text": "If the parties are unable to agree on the License Price within thirty (30) days after Richmond Enterprises provides the written notice of its exercise of the Optional Patent License to Savage Narwhal Studios in Section 2.3(a), such FMV Dispute shall, at either party's request, be resolved solely and exclusively by final, binding and confidential arbitration to be filed and the decision rendered in New York, New York (with hearings at the request of either party to be held in San Francisco, California or other mutually agreeable place convenient for the parties) in accordance with the Commercial Arbitration Rules of the American Arbitration Association (\" AAA \"), including as supplemented by the Procedures for Large, Complex Commercial Disputes.",
  "attributes": [
    {
      "type": "Location",
      "text": "New York",
      "attribute": {
        "begin": 58372,
        "end": 58380
      }
    },
    {
      "type": "Location",
      "text": "New York",
      "attribute": {
        "begin": 58382,
        "end": 58390
      }
    },
    {
      "type": "Location",
      "text": "San Francisco",
      "attribute": {
        "begin": 58451,
        "end": 58464
      }
    },
    {
      "type": "Location",
      "text": "California",
      "attribute": {
        "begin": 58466,
        "end": 58476
      }
    }
  ],
  "categories": [
    {
      "label": "Communication",
      "assurance": "High",
      "provenance": [
        {
          "id": "C7xhbsepUodh09zmJdUXSvYZCdixx00wFyCZuAnTujok="
        }
      ]
    },
    {
      "label": "Dispute Resolution",
      "assurance": "High",
      "provenance": [
        {
          "id": "Ck8vgUWOj41OutOOLJ38b2Q7jOj3F30ABGaGLKKxppFA="
        },
        {
          "id": "CdPeg8mAxM5YIsdpzzaluDg7jOj3F30ABGaGLKKxppFA="
        }
      ]
    },
    {
      "label": "Intellectual Property",
      "assurance": "High",
      "provenance": [
        {
          "id": "Cor/mgcf1UE/zmsKm68M6+a9LSRCpcKe8EWCUdwsjrgs="
        }
      ]
    }
  ],
  "types": [
    {
      "label": {
        "nature": "Obligation",
        "party": "All Parties"
      },
      "assurance": "High",
      "provenance": [
        {
          "id": "NNpSqaNkY2zHtxI6Zh78NaZccVNtutrQxISkzdnaVjx0="
        },
        {
          "id": "PlyERkjg5is36RpFjVUFXp69eDmGmCxLCXRs1sDMDUCo="
        }
      ]
    }
  ],
  "sentence": {
    "begin": 57998,
    "end": 58952
  }
}
```
{: screen}

The element has five important sections:
  - `sentence_text`: The text that was analyzed.
  - `attributes`: An array that lists one or more attributes of the element. Currently supported objects in the `attributes` array include `Location` (geographic location or region referenced by the element), `DateTime` (date, time, date range, or time range specified by the element), and `Currency` (monetary values and units). 
  - `categories`: An array that lists the functional categories into which the identified sentence falls; in other words, the subject matter of the sentence.
  - `types`: An array that describes what the element is and whom it affects. It consists of one or more sets of `nature` keys (the effect of the sentence on the identified `party`) and `party` keys (whom the sentence affects).
  - `sentence`: An object that describes where the element was found in the converted HTML. It contains a `start` character value and an `end` character value.

**Note**: Some sentences do not fall under any type or category, in which case the service returns the `types` and `categories` arrays as empty objects.

**Note:** Some sentences cover multiple topics, in which case the service returns multiple sets of `types` and `categories` objects.

**Note**: Some sentences do not contain any identifiable attributes, in which case the service returns the `attributes` array as empty objects.

Additionally, any identified parties are defined in the `parties` array. The `parties` array is located after the `elements` array in the JSON output.

```
  "parties" : [ {
    "party" : "Customer",
    "role" : "Buyer"
  } ]
```
{: screen}

The `parties` array includes two important sections:

  - `party`: The text that was identified as a party within the document.
  - `role`: The role of the identified party. Roles changed based on model; see [the documentation on the specified model for a list of possible roles](/docs/services/compare-comply/parsing.html#contract_parties). Parties that cannot be identified as having a specific role are listed with the `unknown` value.

## Next steps
{: #next_steps}

You have successfully parsed a contract to identify the nature, parties, and categories of the component parts of the document. You can use the analysis to quickly understand and enforce the parsed contract. The next steps are:

 - [Understanding contract parsing](/docs/services/compare-comply/parsing.html#contract_parsing)
 - [Understanding the output schema](/docs/services/compare-comply/schema.html#output_schema) and [classifying tables](/docs/services/compare-comply/tables.html#understanding_tables).


