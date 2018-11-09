
---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-07"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Release notes
{: #release_notes}

The release notes provide information about changes to the IBM Watson Compare and Comply service release.

## Beta features
{: #beta_features}

**Important:** Compare and Comply is a beta service.
{: important}

IBM will release services, features, and language support that are classified as beta or experimental. These capacities can be unstable, can change frequently, and can be discontinued with short notice. They are provided so you can evaluate their functionality. A beta or experimental capacity might not provide the same level of performance or compatibility that generally released capacities provide. These capacities are not designed for use in a production environment, and any such use is at your own risk.

IBM is under no obligation to offer migration capabilities or services. You are responsible for removing content you want to retain prior to expiration or termination of the beta service.

## Service API Versioning
{: #api_versioning}

API requests require a version parameter that takes a date in the format `version=YYYY-MM-DD`. Whenever we change the API in a backwards-incompatible way, we release a new minor version of the API.

Send the version parameter with every API request. The service uses the API version for the date you specify, or the most recent version before that date. Do not default to the current date. Instead, specify a date that matches a version that is compatible with your application, and do not change it until your application is ready for a later version.

The current version is `2018-10-15`.

## Changes
{: #changes}

The following new features and changes to the service are available.

### 9 November 2018
{: #9nov2018}

Compare and Comply now has the ability to process certain image files and text files as listed at [Supported input formats](/docs/services/compare-comply/formats.html#formats).

The service can process "plain" text (ASCII) files that use a monospaced font and page breaks. Richer text formats that include non-monospaced fonts and style attributes such as bold and italics are not yet supported. If you need to process an enriched text file, convert it to PDF before submitting it to the service.

Supported image formats currently include the following. Scanned image files must have a resolution of at least 300 DPI.
  - BMP
  - GIF
  - JPEG
  - JPEG2000
  - PNG
  - RAW
  - TIFF

The service's methods can accept different types of files as specified in the following table.

| Method           |Image support    |Text support                             |
|------------------|-----------------|-----------------------------------------|
|`/v1/html_conversion`| All supported image formats | Supported |
|`/v1/element_classification`|  All supported image formats | **Not** supported|
|`/v1/tables`      | All supported image formats | Supported |
|`/v1/comparison`*  | All supported image formats | **Not** supported|

\* **Note:** The `/v1/comparison` method still accepts JSON files.

The `/v1/feedback` methods do not accept image or text files. 

The `/v1/batches` methods accept images and text files according to the method called in the batch job. For example, if your batch job calls the `/v1/html_conversion` method, it accepts both images and text files. Similarly, if your batch job calls the `/v1/element_classification` method, it accepts images but not text files.

### 30 October 2018
{: #30oct2018}

**Important:** Changes to the service's API and output schema are ongoing throughout the course of the beta. The API and output schema will be stabilized when the service becomes generally available (GA). When writing applications that access the service, be aware that the API calls and output can change in a future release; applications that access the service therefore need not to be used in a production environment. The following list summarizes the major API and schema changes in the current release.
{: important}

  - A new API version date (`2018-10-15`). If you specify an API version date earlier than `2018-10-15`, you call an older API that most likely has different method names and parameters than those documented for the current release.
  - Changes to the output schema for the `/v1/element_classification` method. See [Getting started](/docs/services/compare-comply/getting-started.html#getting_started) and [Understanding the output schema](/docs/services/compare-comply/schema.html#output_schema) for details.
  - Changes to the `/v1/tables` method's output schema. See [Understanding the output schema](/docs/services/compare-comply/schema.html#output_schema) and [Classifying tables](/docs/services/compare-comply/tables.html#understanding_tables) for information about the table parsing format.
  - Changes to the input and output parameters in the `/v1/feedback` and `/v1/feedback/{feedback_id}` methods. See [Using the feedback APIs](/docs/services/compare-comply/feedback.html#feedback).

### 30 August 2018

Beta release with the following features that have been introduced since the experimental release:

  - Enhanced output including a structural analysis of input documents, as described at [Understanding document structure](/docs/services/compare-comply/doc_structure.html#doc_struct).
  - New `batches` APIs to run Compare and Comply methods over a set of documents, as described at [Using batch processing](/docs/services/compare-comply/batching.html#batching).
  - The ability to parse scanned PDF files that have been processed by an optical character reader (OCR).
  - New `feedback` APIs to enable subject-matter experts to suggest feedback for future updates to the training model, as described at [Using the feedback APIs](/docs/services/compare-comply/feedback.html#feedback).
  - Improved table classification, as described at [Classifying tables](/docs/services/compare-comply/tables.html#understanding_tables).
  - Tooling for working visually on documents, as described at [Using the Compare and Comply Tooling](/docs/services/compare-comply/tooling.html#using_tool).


## Known issues
{: #known_issues}

- The maximum size of an input file that can be uploaded to the service in interactive mode (that is, with a method not called by the `/v1/batches` interface, as well as in the Compare and Comply Tooling) is 1.5 MB. Files submitted through the `/v1/batches` interface can be up to 50 MB. See [Using batch processing](/docs/services/compare-comply/batching.html#batching) for information on the `/v1/batches` interface.
- PDFs with security enabled cannot be parsed.
- Documents with non-standard page layouts (such as 2 or 3 columns per page) do not parse correctly.
