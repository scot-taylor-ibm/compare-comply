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
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Using the feedback APIs
{: #feedback}

Subject-matter experts (SMEs) can use the IBM Watson Compare and Comply service's feedback APIs to provide feedback on a parsed document. An SME can provide feedback on any element that has been labeled by the service or by other SMEs. The feedback is retained in the document and can be reviewed and revised at a later time. The feedback APIs enable users to submit, get, and delete feedback.

**Important:** Feedback is not immediately incorporated into the training model, nor is it guaranteed to be incorporated at a later date. Instead, submitted feedback is used to suggest future updates to the training model.

## API endpoints
{: #feedback_endpoints}

The feedback API endpoints are as follows.

 - `POST /v1/feedback`: Creates or adds feedback to the document.
 - `GET /v1/feedback`: Gets all feedback from the document.
 - `GET /v1/feedback/{feedback_id}`: Gets specified feedback from the document.
<!-- `DELETE /v1/feedback`: Deletes all feedback from the document. -->
 - `DELETE /v1/feedback/{feedback_id}`: Deletes specified feedback from the document.
 
## Warnings
{: #feedback_warnings}

Observe the following warnings and precautions when working with the feedback APIs.

  - Users of the feedback API must be able to provide accurate, consistent, and professionally informed feedback on documents. 
  - Incorrect feedback can provide inaccurate results and can also remain in the document to cause future problems. 
  - Exercise extreme caution if you are using the `POST` or `DELETE` methods.
  - The `GET` methods cannot change feedback that has been added to a document, but the unauthorized use of a `GET` method on a confidential document can potentially result in information being exposed to unauthorized users.

## Steps
{: #feedback_steps}

In the following scenario, an SME named Stuart reviews a parsed governing document and provides feedback on it by using the feedback API. After Stuart finishes his review, an SME named Ann takes the document, reviews it and Stuart's feedback, and uses the feedback API to provide additional feedback. At a high level, the steps are:

1. Stuart reviews the legal document and labels it by using the the `POST /v1/feedback` API method, as described in [Adding feedback](#add_feedback).
1. Ann starts reviewing the legal document that was labeled by Stuart.
1. Ann retrieves all feedback provided on the document by using the `GET /v1/feedback` method, as described in [Getting all feedback](#get_all_feedback).
1. Ann provides her own feedback by using the `POST /v1/feedback` API method.
1. Ann retrieves the feedback she provided by using the `GET /v1/feedback/{feedback_id}` method, as described in [Getting specific feedback](#get_spec_feedback).
1. Ann, Stuart, or another user can optionally delete <!-- all feedback by using the `DELETE /v1/feedback` method, as described in [Deleting feedback](#delete_feedback), or -->specific feedback by using the `DELETE /v1/feedback/{feedback_id}` method, as described in [Deleting specific feedback](#delete_spec_feedback).

These steps are described in more detail in the following sections.

  You can also provide feedback by using the Compare and Comply Tooling as described at [Adding feedback in Using the Compare and Comply Tooling](/docs/services/compare-comply/tooling.html#tool-add-feedback).
  {: tip}

### Adding feedback
{: #add_feedback}

You can add feedback to a document programmatically by using the `POST /v1/feedback` method. 

In a `bash` shell or equivalent environment such as Cygwin, issue the following command to add feedback to a document, with values as follows:
  - Replace `{apikey_value}` with the API key you copied in [Before you begin in Getting Started](/docs/services/compare-comply/getting-started.html#before-you-begin).
  - The method requires a JSON body object that specifies:
    - `feedback_type`: The feedback type. Currently the only permitted value is `element_classification`.
    - `collection_id`: The ID of the collection in which the document is stored.
    - `feedback_data`: A specifically formatted JSON object specifying the feedback you want to add to the document. The value of the `feedback_data` key must be in the following format.
    ```
    {
      "feedback_type": string # required, identifies the type of feedback, only current permitted value is `element_classification`
      "document_id": string # required, uniquely identifies an element document
      "model_id": string # optional, identifies the element model
      "model_version": string # optional, identifies the model version
      "element": { # required, identifies the element within a document
        "begin": integer, # required, the span begin of the sentence in the document
        "end": integer, # required, the span end of the sentence in the document
        "text": string, # optional, the text of the sentence
      },
      "comment": string # optional, free form comment or further description of this feedback
      "original_labels": { # required, empty node is allowed
        "types": [ # required, empty array is allowed
          {
            "label": { # required
              "nature": string, # required, empty string is allowed
              "party": string, # required, empty string is allowed
            },
            "assurance": string, # optional, empty string is allowed
            "provenance": [ # required, empty array is allowed
              {
                "id": string, # required
              }
            ]
          }
        ],
        "categories": [ # required, empty array is allowed
          {
            "label": string, # required, empty string is not allowed,
            "assurance": string, # optional, empty string is allowed
            "provenance": [ # required, empty array is allowed
              {
                "id": string, # required
              }
            ]
          }
        ]
      },
      "updated_labels": { # required, empty node is allowed
        "types": [
        ],
        "categories": [ # required, empty array is allowed
          {
            "label": string, # required, empty string is not allowed
          }
        ]
      }
    }
    ```
    {: codeblock}

<!--
You can assemble the body of the `feedback_data` object as follows:

  1. Run the `POST /v1/element_classification` method as described at [Step 2: Classify a contract's elements in Getting Started](/docs/services/compare-comply/getting-started.html#parse_contract), and save its output to a file.
  1. For the `feedback_type` field, specify a value of `"element_classification"`.
  1. For the `collection_id` field, optionally specify a value of your choosing. 
  1. In the saved output file, look in the `elements` array for the element for which you want to provide feedback. The most common element for posting feedback is `sentence`.
  1. Locate the `begin` and `end` indexes for the element you identified; for example:
    ```
    "sentence": {
        "begin": 31443,
        "end": 31488    
    }
    ```
    {: screen}
  1. 
-->

```bash
curl -X POST -u "apikey":"{apikey_value}"  
https://gateway.watsonplatform.net/compare-comply/api/v1/feedback?version=2018-08-24 \
-d '{
  "feedback_type": "element_classification",
  "collection_id": "440480a1-5ef1-4c3e-9861-3743b5610795",
  "feedback_data": {
    "document_id": "3db9c1f4-57dd-42b5-9586-a2ddf3ed8b64",
    "model_id": "9001500f-d5d9-4c56-a7b4-eef2a218c350",
    "element": {
      "begin": "214",
      "end": "237",
      "text": "1. IBM will provide a Senior Managing Consultant / expert resource, for up to 80 hours, to assist Florida Power & Light (FPL) with the creation of an IT infrastructure unit cost model for existing infrastructure."
    },
    "comment": "",
    "original_labels": {
      "types": [
        {
          "label": {
            "nature": "Obligation",
            "party": "IBM"
          },
          "assurance": "High",
          "provenance": [
            {
              "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
            },
            {
              "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
            }
          ]
        },
        {
          "label": {
            "nature": "End User",
            "party": "Exclusion"
          },
          "assurance": "High",
          "provenance": [
            {
              "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
            },
            {
              "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
            }
          ]
        }
      ],
      "categories": [
        {
          "label": "Responsibilities"
        },
        {
          "label": "Amendments"
        }
      ]
    },
    "updated_labels": {
      "types": [
        {
          "label": {
            "nature": "Obligation",
            "party": "IBM"
          }
        },
        {
          "label": {
            "nature": "Disclaimer",
            "party": "Buyer"
          }
        }
      ],
      "categories": [
        {
          "label": "Responsibilities"
        },
        {
          "label": "Audits"
        }
      ]
    }
  }
}' 
```
{: codeblock}

The output of the command resembles the following.

```
{
  "feedback_id": "9730b437-cb86-4d40-9a84-ff6948bb3dd1",
  "customer_id": "125",
  "collection_id": "440480a1-5ef1-4c3e-9861-3743b5610795",
  "feedback_type": "element_classification",
  "created": "2018-07-03T10:16:05-0500",
  "feedback_data": {
    "document_id": "3db9c1f4-57dd-42b5-9586-a2ddf3ed8b64",
    "model_id": "9001500f-d5d9-4c56-a7b4-eef2a218c350",
    "comment": "",
    "original_labels": {
      "types": [
        {
          "label": {
            "nature": "Obligation",
            "party": "IBM"
          },
          "assurance": "High",
          "provenance": [
            {
              "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
            },
            {
              "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
            }
          ],
          "flag": "notChanged"
        },
        {
          "label": {
            "nature": "End User",
            "party": "Exclusion"
          },
          "assurance": "High",
          "provenance": [
            {
              "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
            },
            {
              "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
            }
          ],
          "flag": "removed"
        }
      ],
      "categories": [
        {
          "label": "Responsibilities",
          "provenance": [],
          "flag": "notChanged"
        },
        {
          "label": "Amendments",
          "provenance": [],
          "flag": "removed"
        }
      ]
    },
    "element": {
      "begin": "214",
      "end": "237",
      "text": "1. IBM will provide a Senior Managing Consultant / expert resource, for up to 80 hours, to assist Florida Power & Light (FPL) with the creation of an IT infrastructure unit cost model for existing infrastructure."
    },
    "updated_labels": {
      "types": [
        {
          "label": {
            "nature": "Obligation",
            "party": "IBM"
          },
          "flag": "notChanged"
        },
        {
          "label": {
            "nature": "Disclaimer",
            "party": "Buyer"
          },
          "flag": "added"
        }
      ],
      "categories": [
        {
          "label": "Responsibilities",
          "flag": "notChanged"
        },
        {
          "label": "Audits",
          "flag": "added"
        }
      ]
    }
  }
}
```
{: screen}


### Getting all feedback
{: #get_all_feedback}

You can retrieval all feedback that has been added to a document by using the `GET /v1/feedback/` method. The method takes the following input parameters:

  - `version` (**required** `string`): A date in the format `YYYY-MM-DD` that identifies the specific version of the API to use when processing the request.
  - `feedback_type` (optional `string`): If this parameter is specified, the service returns only the records having the specified feedback type. Currently the only supported value is `element_classification`.
  - `customer_id` (optional `string`): If this parameter is specified, the service returns only the records with the specified customer ID.
  - `collection_id` (optional `string`): If this parameter is specified, the service returns only the records with the specified collection ID.
  - `before` (optional `string`): If this parameter is specified in the format `YYYY-MM-DD`, the service returns only the records with dates prior to the specified date.
  - `after` (optional `string`): If this parameter is specified in the format `YYYY-MM-DD`, the service returns only the records with dates subsequent to the specified date.
  - `ec_document_id` (optional `string`): If this parameter is specified, the service returns only the records with the specified element document ID.
  - `ec_model_id` (optional `string`): If this parameter is specified, the service returns only the records with the specified element model ID.
  - `ec_category_removed` (optional `string`): A comma-separated list of `categories`. If this parameter is specified, the service returns only the records that have one or more of the specified `categories` removed. See [Categories](/docs/services/compare-comply/parsing.html#contract_categories) for a table of valid `categories`.
  - `ec_category_added` (optional `string`): A comma-separated list of `categories`. If this parameter is specified, the service returns only the records that have one or more of the specified `categories` added. See [Categories](/docs/services/compare-comply/parsing.html#contract_categories) for a table of valid `categories`.
  - `ec_category_not_changed` (optional `string`): A comma-separated list of `categories`. If this parameter is specified, the service returns only the records that have one or more of the specified `categories` unchanged. See [Categories](/docs/services/compare-comply/parsing.html#contract_categories) for a table of valid `categories`.
  - `ec_type_removed` (optional `string`): A comma-separated list of `types` in which each `type` value is of the form `nature:party`. If this parameter is specified, the service returns only the records that have one or more of the specified types removed. See [Types](/docs/services/compare-comply/parsing.html#contract_types) for a table of valid `types` (that is, `nature` and `party` pairs).
  - `ec_type_added` (optional `string`): A comma-separated list of `types` in which each `type` value is of the form `nature:party`. If this parameter is specified, the service returns only the records that have one or more of the specified types added. See [Types](/docs/services/compare-comply/parsing.html#contract_types) for a table of valid `types` (that is, `nature` and `party` pairs).
  - `ec_type_not_changed` (optional `string`): A comma-separated list of `types` in which each `type` value is of the form `nature:party`. If this parameter is specified, the service returns only the records that have one or more of the specified types unchanged. See [Types](/docs/services/compare-comply/parsing.html#contract_types) for a table of valid `types` (that is, `nature` and `party` pairs). 
  - `count` (optional `string`): The number of documents that you want returned in the response. The default is `200`. The maximum for the count and offset values together in any one query is `10000`.
  - `offset` (optional `string`): The number of query results to skip at the beginning of the returned results. The default is `0`. The maximum for the count and offset values together in any one query is `10000`.
  - `sort` (optional `string`): A comma-separated list of fields in the document on which to sort returned results. You can optionally specify a sort direction by prefixing the field with `-` for descending order or `+` for ascending order. Ascending order is the default sort direction.
  
A example command that combines the `ec_type_added` and `ec_category_removed` parameters is:

```bash
curl -X GET -u "apikey":"{apikey_value}" https://gateway.watsonplatform.net/compare-comply/api/v1/feedback?version=2018-08-24&ec_type_added=Definition:None,Disclaimer:Supplier&ec_category_removed=Assignments,Audits
```
{: codeblock}

The output of the command is a JSON array resembling the following.

```
[
  {
    "feedback_id": "9730b437-cb86-4d40-9a84-ff6948bb3dd1",
    "customer_id": "125",
    "collection_id": "440480a1-5ef1-4c3e-9861-3743b5610795",
    "feedback_type": "element_classification",
    "created": "2018-07-03T10:16:05-0500",
    "feedback_data": {
      "document_id": "3db9c1f4-57dd-42b5-9586-a2ddf3ed8b64",
      "model_id": "9001500f-d5d9-4c56-a7b4-eef2a218c350",
      "comment": "",
      "original_labels": {
        "types": [
          {
            "label": {
              "nature": "Obligation",
              "party": "IBM"
            },
            "assurance": "High",
            "provenance": [
              {
                "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
              },
              {
                "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
              }
            ],
            "flag": "notChanged"
          },
          {
            "label": {
              "nature": "End User",
              "party": "Exclusion"
            },
            "assurance": "High",
            "provenance": [
              {
                "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
              },
              {
                "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
              }
            ],
            "flag": "removed"
          }
        ],
        "categories": [
          {
            "label": "Responsibilities",
            "provenance": [],
            "flag": "notChanged"
          },
          {
            "label": "Amendments",
            "provenance": [],
            "flag": "removed"
          }
        ]
      },
      "element": {
        "begin": "214",
        "end": "237",
        "text": "1. IBM will provide a Senior Managing Consultant / expert resource, for up to 80 hours, to assist Florida Power & Light (FPL) with the creation of an IT infrastructure unit cost model for existing infrastructure."
      },
      "updated_labels": {
        "types": [
          {
            "label": {
              "nature": "Obligation",
              "party": "IBM"
            },
            "flag": "notChanged"
          },
          {
            "label": {
              "nature": "Disclaimer",
              "party": "Buyer"
            },
            "flag": "added"
          }
        ],
        "categories": [
          {
            "label": "Responsibilities",
            "flag": "notChanged"
          },
          {
            "label": "Audits",
            "flag": "added"
          }
        ]
      }
    }
  },
  {
    "feedback_id": "9730b437-cb86-4d40-9a84-ff6948bb3dd1",
    "customer_id": "125",
    "collection_id": "440480a1-5ef1-4c3e-9861-3743b5610795",
    "feedback_type": "element_classification",
    "created": "2018-07-03T10:16:05-0500",
    "feedback_data": {
      "document_id": "3db9c1f4-57dd-42b5-9586-a2ddf3ed8b64",
      "model_id": "9001500f-d5d9-4c56-a7b4-eef2a218c350",
      "comment": "",
      "original_labels": {
        "types": [
          {
            "label": {
              "nature": "Obligation",
              "party": "FPL"
            },
            "assurance": "High",
            "provenance": [
              {
                "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
              },
              {
                "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
              }
            ],
            "flag": "notChanged"
          },
          {
            "label": {
              "nature": "End User",
              "party": "Exclusion"
            },
            "assurance": "High",
            "provenance": [
              {
                "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
              },
              {
                "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
              }
            ],
            "flag": "removed"
          }
        ],
        "categories": [
          {
            "label": "Responsibilities",
            "provenance": [],
            "flag": "notChanged"
          },
          {
            "label": "Amendments",
            "provenance": [],
            "flag": "removed"
          }
        ]
      },
      "element": {
        "begin": "214",
        "end": "237",
        "text": "3. FPL will provide access to IT Infrastructure SMEs and make available any pre-existing cost models for network devices, as well as a description of monitored usage data for its network devices."
      },
      "updated_labels": {
        "types": [
          {
            "label": {
              "nature": "Obligation",
              "party": "FPL"
            },
            "flag": "notChanged"
          },
          {
            "label": {
              "nature": "Disclaimer",
              "party": "Buyer"
            },
            "flag": "added"
          }
        ],
        "categories": [
          {
            "label": "Responsibilities",
            "flag": "notChanged"
          },
          {
            "label": "Audits",
            "flag": "added"
          }
        ]
      }
    }
  }
]
```
{: screen}

### Getting specific feedback
{: #get_spec_feedback}

You can retrieve specific feedback from a document by using the `GET /v1/feedback/{feedback_id}` method. The method takes the following input parameters.

  - `version` (**required** `string`): A date in the format `YYYY-MM-DD` that identifies the specific version of the API to use when processing the request.
  - `feedback_id` (**required** `string`): The feedback ID for which the service is to return details. You can obtain the `feedback_id` from the output of the `GET /v1/feedback` method.
  
An example command is:

```bash
curl -X GET -u "apikey":"{apikey_value}" https://gateway.watsonplatform.net/compare-comply/api/v1/feedback?version=2018-08-24&feedback_id=9730b437-cb86-4d40-9a84-ff6948bb3dd1
```
{: codeblock}

The command returns output similar to the following:

```
{
 "feedback_type": "element_classification",
 "feedback_id": "9730b437-cb86-4d40-9a84-ff6948bb3dd1",
 "customer_id": "550480a1-5ef1-4c3e-9861-3743b5610795",
 "collection_id": "440480a1-5ef1-4c3e-9861-3743b5610795",
 "created": "2018-05-08T22:17:45+0000",
 "feedback_data" :
   {
      "document_id": "3db9c1f4-57dd-42b5-9586-a2ddf3ed8b64",
      "model_id": "9001500f-d5d9-4c56-a7b4-eef2a218c350",
      "element": {
        "begin": "214",
        "end": "237",
        "text": "1. IBM will provide a Senior Managing Consultant / expert resource, for up to 80 hours, to assist Florida Power & Light (FPL) with the creation of an IT infrastructure unit cost model for existing infrastructure."
      },
      "comment": "",
      "original_labels": {
        "types": [
          {
            "label": {
              "nature": "Obligation",
              "party": "IBM"
            },
            "assurance": "High",
            "provenance": [
              {
                "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
              },
              {
                "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
              }
            ]
          }
        ],
        "categories": [
          {
            "label": "obligation",
            "assurance": "High",
            "provenance": [
              {
                "id": "85f5981a-ba91-44f5-9efa-0bd22e64b7bc"
              },
              {
                "id": "ce0480a1-5ef1-4c3e-9861-3743b5610795"
              }
            ]
          }
        ]
      },
      "updated_labels": {
        "types": [
          {
            "label": {
              "nature": "Obligation",
              "party": "IBM"
            }
          }
        ],
        "categories": [
          {
            "label": "Responsibilities"
          }
        ]
      }
    }
}
```
{: screen}

<!--
###Deleting all feedback
{: #delete_feedback}

You can delete all feedback in a document collection by using the `DELETE /v1/feedback` method. The method requires only the `version` date parameter.

```bash
curl -X DELETE -u "apikey":"{apikey_value}" https://gateway.watsonplatform.net/compare-comply/api/v1/feedback?version=2018-08-24
```
{: pre}

Alternatively, you can delete all feedback from documents with a specific customer ID by specifying the ID in the call headers:

```bash
curl -X DELETE -u "apikey":"{apikey_value}" -H 'x-watson-metadata: customer_id=3910' https://gateway.watsonplatform.net/compare-comply/api/v1/feedback?version=2018-08-24
```
{: pre}

The service returns the following output on success:

```
{
  "status": "200",
  "message": "Successfully deleted the feedback"
}
```
{: codeblock}
-->

### Deleting specific feedback
{: #delete_spec_feedback}

You can delete specific feedback from a document by using the `DELETE /v1/feedback/{feedback_id}` method. The method takes the following input parameters.

  - `version` (**required** `string`): A date in the format `YYYY-MM-DD` that identifies the specific version of the API to use when processing the request.
  - `feedback_id` (**required** `string`): The feedback ID the service is to delete. You can obtain the `feedback_id` from the output of the `GET /v1/feedback/{feedback_id}` method.

An example command is:
  
```bash
curl -X DELETE -u "apikey":"{apikey_value}" https://gateway.watsonplatform.net/compare-comply/api/v1/feedback?version=2018-08-24&feedback_id=5206038a-5ea0-4f48-bee1-0780c56c53c9
```
{: codeblock}

The service returns the following output on success:

```
{
  "status": "200",
  "message": "Successfully deleted the feedback with id  - 5206038a-5ea0-4f48-bee1-0780c56c53c9"
}
```
{: screen}
