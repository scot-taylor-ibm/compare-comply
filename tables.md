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

# Classifying tables
{: #understanding_tables}

You can classify the contents of tables in your input document by using the `POST /v1/tables` method. 

In a `bash` shell or equivalent environment such as Cygwin, use the `POST /v1/tables` method to classify the contents of tables in your document. The method takes the following input parameters:
  - `version` (**required** `string`): A date in the format `YYYY-MM-DD` that identifies the specific version of the API to use when processing the request.
  - `file` (**required** `file`): The input file that is to be classified.
  - `model` (optional `string`): If this parameter is specified, the service runs the specified type of element classification. Currently, the only supported value is `contracts`.
  
Replace `{apikey_value}` with the API key you copied earlier and `{PDF_file}` with the path to the PDF to parse.

```bash
curl -X POST -u "apikey":"{apikey_value}" -F 'file=@{PDF_file};type=application/pdf' https://gateway.watsonplatform.net/compare-comply/api/v1/tables?version=2018-08-24
```
{: pre}

See [Understanding the output schema](/docs/services/compare-comply/schema.html#output_schema) for information about the table parsing format.

The following is an example table from an input document.
 ![Example table](images/example-table.png)

The table is composed as follows:
 ![Table composition](images/table-comp.png)
 
where:

<ul>
  <li><strong><em>Bold italic text</em></strong> indicates a table header</li>
  <li><strong>Bold text</strong> indicates a column header</li>
  <li><em>Italic text</em> indicates a row header</li>
  <li>Unstyled text indicates a body cell</li>
</ul>
  
The output from service represents the example's first body cell (that is, the first cell in row 3 with a value of `35.0%`) as follows.

```
"tables": [ {
    "table": {
      "begin": 872,
      "end": 5879
    },
    "table_text": "...",
    "section_title": { },
    "section_title_text": "",
    "table_headers" : [ {
      "id" : "tableHeader-872-873",
      "cell" : {
        "begin" : 872,
        "end" : 873
      },
      "cell_text" : " ",
      "row_index_min" : 0,
      "row_index_max" : 0,
      "column_index_min" : 0,
      "column_index_max" : 0
    }, {
      "id" : "tableHeader-1381-1382",
      "cell" : {
        "begin" : 1381,
        "end" : 1382
      },
      "cell_text" : " ",
      "row_index_min" : 1,
      "row_index_max" : 1,
      "column_index_min" : 0,
      "column_index_max" : 0
    } ],  
    "row_headers" : [ {
      "id" : "rowHeader-2244-2262",
      "cell" : {
        "begin" : 2244,
        "end" : 2263
      },
      "cell_text" : "Statutory tax rate",
      "cell_text_normalized" : "Statutory tax rate",
      "row_index_min" : 2,
      "row_index_max" : 2,
      "column_index_min" : 0,
      "column_index_max" : 0
    }, {
      "id" : "rowHeader-3197-3217",
      "cell" : {
        "begin" : 3197,
        "end" : 3218
      },
      "cell_text" : "IRS audit settlement",
      "cell_text_normalized" : "IRS audit settlement",
      "row_index_min" : 3,
      "row_index_max" : 3,
      "column_index_min" : 0,
      "column_index_max" : 0
    }, {
      "id" : "rowHeader-4148-4176",
      "cell" : {
        "begin" : 4148,
        "end" : 4177
      },
      "cell_text" : "Dividends received deduction",
      "cell_text_normalized" : "Dividends received deduction",
      "row_index_min" : 4,
      "row_index_max" : 4,
      "column_index_min" : 0,
      "column_index_max" : 0
    }, {
      "id" : "rowHeader-5106-5130",
      "cell" : {
        "begin" : 5106,
        "end" : 5131
      },
      "cell_text" : "Total effective tax rate",
      "cell_text_normalized" : "Total effective tax rate",
      "row_index_min" : 5,
      "row_index_max" : 5,
      "column_index_min" : 0,
      "column_index_max" : 0
    } ],
    "column_headers" : [ {
      "id" : "colHeader-1050-1082",
      "cell" : {
        "begin" : 1050,
        "end" : 1083
      },
      "cell_text" : "Three months ended September 30,",
      "cell_text_normalized" : "Three months ended September 30,",
      "row_index_min" : 0,
      "row_index_max" : 0,
      "column_index_min" : 1,
      "column_index_max" : 2
    }, {
      "id" : "colHeader-1270-1301",
      "cell" : {
        "begin" : 1270,
        "end" : 1302
      },
      "cell_text" : "Nine months ended September 30,",
      "cell_text_normalized" : "Nine months ended September 30,",
      "row_index_min" : 0,
      "row_index_max" : 0,
      "column_index_min" : 3,
      "column_index_max" : 4
    }, {
      "id" : "colHeader-1544-1548",
      "cell" : {
        "begin" : 1544,
        "end" : 1549
      },
      "cell_text" : "2005",
      "cell_text_normalized" : "Year 1",
      "row_index_min" : 1,
      "row_index_max" : 1,
      "column_index_min" : 1,
      "column_index_max" : 1
    }, {
      "id" : "colHeader-1712-1716",
      "cell" : {
        "begin" : 1712,
        "end" : 1717
      },
      "cell_text" : "2004",
      "cell_text_normalized" : "Year 2",
      "row_index_min" : 1,
      "row_index_max" : 1,
      "column_index_min" : 2,
      "column_index_max" : 2
    }, {
      "id" : "colHeader-1889-1893",
      "cell" : {
        "begin" : 1889,
        "end" : 1894
      },
      "cell_text" : "2005",
      "cell_text_normalized" : "Year 1",
      "row_index_min" : 1,
      "row_index_max" : 1,
      "column_index_min" : 3,
      "column_index_max" : 3
    }, {
      "id" : "colHeader-2057-2061",
      "cell" : {
        "begin" : 2057,
        "end" : 2062
      },
      "cell_text" : "2004",
      "cell_text_normalized" : "Year 2",
      "row_index_min" : 1,
      "row_index_max" : 1,
      "column_index_min" : 4,
      "column_index_max" : 4
    } ],
    "body_cells" : [ {
      "id" : "bodyCell-2450-2455",
      "cell" : {
        "begin" : 2450,
        "end" : 2456
      },
      "cell_text" : "35.0%",
      "row_index_min" : 2,
      "row_index_max" : 2,
      "column_index_min" : 1,
      "column_index_max" : 1,
      "row_header_ids" : [ "rowHeader-2244-2262" ],
      "row_header_texts" : [ "Statutory tax rate" ],
      "row_header_texts_normalized" : [ "Statutory tax rate" ],
      "column_header_ids" : [ "colHeader-1050-1082", "colHeader-1544-1548" ],
      "column_header_texts" : [ "Three months ended September 30,", "2005" ],
      "column_header_texts_normalized" : [ "Three months ended September 30,", "Year 1" ]
    }, {
      "id" : "bodyCell-2633-2638",
      "cell" : {
        "begin" : 2633,
        "end" : 2639
      },
      "cell_text" : "35.0%",
      "row_index_min" : 2,
      "row_index_max" : 2,
      "column_index_min" : 2,
      "column_index_max" : 2,
      "row_header_ids" : [ "rowHeader-2244-2262" ],
      "row_header_texts" : [ "Statutory tax rate" ],
      "row_header_texts_normalized" : [ "Statutory tax rate" ],
      "column_header_ids" : [ "colHeader-1050-1082", "colHeader-1712-1716" ],
      "column_header_texts" : [ "Three months ended September 30,", "2004" ],
      "column_header_texts" : [ "Three months ended September 30,", "Year 2" ]
    }, {
      "id" : "bodyCell-2825-2830",
      "cell" : {
        "begin" : 2825,
        "end" : 2831
      },
      "cell_text" : "35.0%",
      "row_index_min" : 2,
      "row_index_max" : 2,
      "column_index_min" : 3,
      "column_index_max" : 3,
      "row_header_ids" : [ "rowHeader-2244-2262" ],
      "row_header_texts" : [ "Statutory tax rate" ],
      "row_header_texts_normalized" : [ "Statutory tax rate" ],
      "column_header_ids" : [ "colHeader-1270-1301", "colHeader-1889-1893" ],
      "column_header_texts" : [ "Nine months ended September 30,", "2005" ],
      "column_header_texts_normalized" : [ "Nine months ended September 30,", "Year 1" ]
    }, {
      "id" : "bodyCell-3008-3013",
      "cell" : {
        "begin" : 3008,
        "end" : 3014
      },
      "cell_text" : "35.0%",
      "row_index_min" : 2,
      "row_index_max" : 2,
      "column_index_min" : 4,
      "column_index_max" : 4,
      "row_header_ids" : [ "rowHeader-2244-2262" ],
      "row_header_texts" : [ "Statutory tax rate" ],
      "row_header_texts_normalized" : [ "Statutory tax rate" ],
      "column_header_ids" : [ "colHeader-1270-1301", "colHeader-2057-2061" ],
      "column_header_texts" : [ "Nine months ended September 30,", "2004" ],
      "column_header_texts_normalized" : [ "Nine months ended September 30,", "Year 2" ]
    }, 
    ...
  ]
}
```
    