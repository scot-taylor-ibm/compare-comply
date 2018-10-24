
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

# Release notes
{: #release_notes}

The release notes provide information about changes to the IBM Watson Compare and Comply service release.

## Beta features
{: #beta_features}

**Important:** Compare and Comply is a beta service.

IBM will release services, features, and language support that are classified as beta or experimental. These capacities can be unstable, can change frequently, and can be discontinued with short notice. They are provided so you can evaluate their functionality. A beta or experimental capacity might not provide the same level of performance or compatibility that generally released capacities provide. These capacities are not designed for use in a production environment, and any such use is at your own risk.

IBM is under no obligation to offer migration capabilities or services. You are responsible for removing content you want to retain prior to expiration or termination of the beta service.

## Service API Versioning
{: #api_versioning}

API requests require a version parameter that takes a date in the format `version=YYYY-MM-DD`. Whenever we change the API in a backwards-incompatible way, we release a new minor version of the API.

Send the version parameter with every API request. The service uses the API version for the date you specify, or the most recent version before that date. Do not default to the current date. Instead, specify a date that matches a version that is compatible with your application, and do not change it until your application is ready for a later version.

The current version is `2018-08-24`.

## Changes
{: #changes}

The following new features and changes to the service are available.

### 17 August 2018

Beta release with the following features that have been introduced since the experimental release:

  - New `batches` APIs, as described at [Using batch processing](/docs/services/compare-comply/batching.html#batching).
  - The ability to parse scanned PDF files that have been processed by an optical character reader (OCR).
  - New `feedback` APIs, as described at [Using the feedback APIs](/docs/services/compare-comply/feedback.html#feedback).
  - Improved table classification, as described at [Classifying tables](/docs/services/compare-comply/tables.html#understanding_tables).

## General notes
{: #general_notes}

 - PDFs must be in text format to be parsed. Documents that have been scanned cannot be parsed, even if the scans have been processed by an optical character reader (OCR).

## Known issues
{: #known_issues}

- The maximum size of a PDF file that can be uploaded is 50 Mb.
- PDFs with security enabled cannot be parsed.
- Documents with non-standard page layouts (such as 2 or 3 columns per page) do not parse correctly.
