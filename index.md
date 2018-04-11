---

copyright:
  years: 2018
lastupdated: "2018-04-11"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}

# Getting started with Client Insight for Wealth Management
{: #getting_started_client_insight_wealth_short}

The {{site.data.keyword.client_insight_wealth}} service provides a set of analytical models that help you generate client insights for use in marketing, distribution, and services provision for wealth management companies. You can segment clients based on behavior, determine attrition propensity, and enhance client engagement increasing retention, cross-sell, and up-sell opportunities.
{:shortdesc}

The full production version of the {{site.data.keyword.client_insight_wealth_short}} service is available for purchase through your IBM representative. For more information, see (https://www.ibm.com/us-en/marketplace/client-insight-for-wealth-management)

## Before you begin
{: #prereqs}
The following steps show you how to obtain access to the {{site.data.keyword.client_insight_wealth_short}} service and invoke API methods. To use the {{site.data.keyword.client_insight_wealth_short}} REST API, it is important that you understand the basics of RESTful web services and JSON representations.
Ensure that you have cURL installed because you will use cURL to call the API in the examples.

## Step 1: Getting your API key

The full production version of the {{site.data.keyword.client_insight_wealth_short}} service is available for purchase through your IBM sales representative. For more information, see (https://www.ibm.com/us-en/marketplace/client-insight-for-wealth-management)

Once you have received your API key from your IBM representative, use the API key in the following command to test your access to the service:
 
```
curl -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/customers/
```
{:codeblock}

The service returns an empty json with square brackets.

## Step 2: Loading sample data into the object store

To load sample data into your tenant's Cloud Object Store (COS), enter the following command:

```
curl -X POST -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/sample_data
```
{:codeblock}

The service returns an **OK** response.

Note: This operation loads the data provided by IBM for evaluation purposes. It is not intended for use in a production environment.

## Step 3: Loading data into the database

Before using the example in this step, save the following JSON code in a file named ingest.json:

```
{  
   "inputPaths":{  
      "account":{  
         "schema":"sample-inputs/schemas/account.csv",
         "table":"sample-inputs/tables/account.csv"
      },
      "account_summary":{  
         "schema":"sample-inputs/schemas/account_summary.csv",
         "table":"sample-inputs/tables/account_summary.csv"
      },
      "channel_organization":{  
         "schema":"sample-inputs/schemas/channel_organization.csv",
         "table":"sample-inputs/tables/channel_organization.csv"
      },
      "code":{  
         "schema":"sample-inputs/schemas/code.csv",
         "table":"sample-inputs/tables/code.csv"
      },
      "customer":{  
         "schema":"sample-inputs/schemas/customer.csv",
         "table":"sample-inputs/tables/customer.csv"
      },
      "customer_product_summary":{  
         "schema":"sample-inputs/schemas/customer_product_summary.csv",
         "table":"sample-inputs/tables/customer_product_summary.csv"
      },
      "customer_summary":{  
         "schema":"sample-inputs/schemas/customer_summary.csv",
         "table":"sample-inputs/tables/customer_summary.csv"
      },
      "event":{  
         "schema":"sample-inputs/schemas/event.csv",
         "table":"sample-inputs/tables/event.csv"
      },
      "event_type":{  
         "schema":"sample-inputs/schemas/event_type.csv",
         "table":"sample-inputs/tables/event_type.csv"
      },
      "financial_asset":{  
         "schema":"sample-inputs/schemas/financial_asset.csv",
         "table":"sample-inputs/tables/financial_asset.csv"
      },
      "intermediary_person":{  
         "schema":"sample-inputs/schemas/intermediary_person.csv",
         "table":"sample-inputs/tables/intermediary_person.csv"
      },
      "position":{  
         "schema":"sample-inputs/schemas/position.csv",
         "table":"sample-inputs/tables/position.csv"
      },
      "product":{  
         "schema":"sample-inputs/schemas/product.csv",
         "table":"sample-inputs/tables/product.csv"
      },
      "transaction_trading":{  
         "schema":"sample-inputs/schemas/transaction_trading.csv",
         "table":"sample-inputs/tables/transaction_trading.csv"
      }
   }
}
```
{:codeblock}

To load sample data into the database, enter the following command:

```
curl -X POST -H "Authorization: <api-key>" -H "Content-Type: application/json" -d @ingest.json https://ci-api.mybluemix.net/api/v2/wealth/ingest
```
{:codeblock}

The service returns a response that contains the job-id, status, and details of the job.

The following is an example of a response to a successful request:

```
{
    "job_id": "ingest-13878a6d-2bef-4b90-b875-6eba024b11b9",
    "status": "Running",
    "message":"To check the current status of the ingestion job use GET https://ci-api.mybluemix.net/api/v2/wealth/ingest/ingest-13878a6d-2bef-4b90-b875-6eba024b11b9"
}
```

Use the job-id to check the job status as in the following command:

```
curl -X GET -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/ingest/<job-id>
```
{:codeblock}

The service returns a response that contains the job-id, either a status of **Running** while it is still processing or a status of **Success** when it is complete.

The following is an example of a response when the request has completed successfully:

```
{
       "job_status": {
           "job_id": "ingest-13878a6d-2bef-4b90-b875-6eba024b11b9",
           "status": "Success",
           "message":"The ingestion job completed successfully"
       }
   }
```

## Step 4: Verifying that the sample data is loaded

To ensure the sample data loaded successfully, enter the following command:

```
curl -X GET -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/customers/
```
{:codeblock}

The following is an example showing the first customer entry from a response when sample data is in the database and the request has completed successfully. It has been reformatted for readability.

```
[
  {
        "relationship_start_date": "2005-05-18",
        "address_home_state": null,
        "household_id": "tristique",
        "primary_advisor_id": "30",
        "influence_score": 95531,
        "geographic_area_mailing": "Canada SK Saskatchewan",
        "customer_id": "1000",
        "geographic_area_work": "Canada SK Saskatchewan",
        "customer_behavior": "lorem ipsum",
        "status_reason": null,
        "referrals_value_code": "High",
        "market_group": "ultricies",
        "aggregation_opt_in_indicator": true,
        "effective_date": "2007-02-11",
        "primary_advisor_organization_id": null,
        "status_date": "2007-02-11",
        "geographic_area_home": "Canada SK Saskatchewan",
        "head_of_household_indicator": false,
        "recorded_voice_sample_id": "semper viverra",
        "urban_code": "City",
        "importance_level_code": "Low priority",
        "life_cycle_status_code": "nulla curabitur",
        "preferred_communication_form": "ultricies",
        "status": "Inactive",
        "secondary_advisor_organization_id": "tristique",
        "address_home_country": null,
        "address_home_city": null,
        "satisfaction_rating_from_survey": "ultricies",
        "pursuit": "semper viverra",
        "secondary_advisor_id": "nulla curabitur",
        "address_last_changed_date": "2005-02-04",
        "primary_branch_proximity": 115.08760355744768,
        "advertising_indicator": true
    },
    ...
]
```

## Step 5: Transforming data


Before using the example in this step, save the following JSON code in a file named transform_body.json:


```
{
  "customer": {
    "status": {
      "active": ""
    }
  } 
}  
```
{:codeblock}

To transform data, enter the following command:

```
curl -X POST -H "Authorization: <api-key>" -H "Content-Type: application/json" -d @transform_body.json https://ci-api.mybluemix.net/api/v2/wealth/ingest/transform/<model-id>
```
{:codeblock}


The model name can be "Attrition", "LifeEventPrediction", "AttritionWithAggregation", "CrossSellProduct", "GeneralSegmentation", "GeneralSegmentationWithAggregation"

The following is an example of a successful request using the GeneralSegmentation model returning the job-id and the status of the job:

```
{
    "job_id": "transform-GeneralSegmentation-1f52dc7b-8432-4597-bbad-4b4466ce95c7",
    "status": "Running",
    "message":"To check the current status of the transformation job use GET https://ci-dev.mybluemix.net/api/v2/wealth/ingest/transform-GeneralSegmentation-1f52dc7b-8432-4597-bbad-4b4466ce95c7"
}
```

Use the job-id to check the job status as in the following command:

```
curl -X GET -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/jobs/<job-id>
```
{:codeblock}

The service returns a response that contains the job-id, either a status of **Running** while it is still processing, or a status of **Success** when it is complete.

The following is an example of a response when the request has completed successfully using the GeneralSegmentation model:

```
{
    "job_status": {
        "job_id": "transform-GeneralSegmentation-1f52dc7b-8432-4597-bbad-4b4466ce95c7",	
        "status": "Success",
        "message": "The transformation job completed successfully"
    }
}
```

## Step 6: Training a model


Before using the example in this step, save the following JSON code in a file named genseg_params.json:

```
{
  "settings": { 
    "effective_date": "2017-08-01" 
  } 
}
```
{:codeblock}


To train a model, enter the following command:

```
curl -X POST -H "Authorization: <api-key>" -H "Content-Type: application/json" -d @genseg_params.json https://ci-api.mybluemix.net/api/v2/wealth/analytics/train/<model-id>
```
{:codeblock}

The model name can be "Attrition", "LifeEventPrediction", "AttritionWithAggregation", "CrossSellProduct", "GeneralSegmentation", or "GeneralSegmentationWithAggregation"

The following is an example of a successful request using GeneralSegmentation returning the job-ids and the status of the job:

```
{
    "job_id": "GeneralSegmentation-0a8c1e15-3431-46c3-8ad1-6539266a7b75",
    "status": "Running",
    "message": "To check the current status of the training job use GET https://ci-dev.mybluemix.net/api/v2/wealth/analytics/status/GeneralSegmentation-0a8c1e15-3431-46c3-8ad1-6539266a7b75"
}
```

Note: The request returns one entry per product for the CrossSellProduct model because each product has a model that is trained and scored separately.

Use the job-id to check the job status as in the following command:

```
curl -X GET -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/analytics/status/<job-id>
```
{:codeblock}

The service returns a response that contains the job-id, either a status of **Running** while it is still processing, or a status of **Success** when it is complete.

The following is an example of a response when the request has completed successfully using the AutoChurn model:

```
{
    "job_id": "GeneralSegmentation-0a8c1e15-3431-46c3-8ad1-6539266a7b75",
    "status": "Success",
    "message": "The model job completed successfully"
}
```

## Step 7: Scoring a model

To score a model, enter the following command:

```
curl -X POST -H "Authorization: <api-key>" -H "Content-Type: application/json" -d @genseg_params.json https://ci-api.mybluemix.net/api/v2/wealth/analytics/score/<model-id>
```
{:codeblock}

This request reuses the genseg_params.json file you created in Step 6. 

The following is an example of a successful request using the GeneralSegmentation model:

```
{
    "job_id": "GeneralSegmentation-3a26e978-2ac3-4f55-8571-17e9617d8c5c",
    "status": "Running",
    "message": "To check the current status of the scoring job use GET https://ci-dev.mybluemix.net/api/v2/wealth/analytics/status/GeneralSegmentation-3a26e978-2ac3-4f55-8571-17e9617d8c5c"
}
```
	
Note: The request returns one entry per product for the CrossSellProduct model because each product has a model that is trained and scored separately.

Use the job-id to check the job status as in the following command:

```
curl -X GET -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/analytics/status/<job-id>
```
{:codeblock}

The service returns a response that contains the job-id, either a status of **Running** while it is still processing or a status of **Success** when it is complete.

The following is an example of a response when the request has completed successfully for a GeneralSegmentation model:

```
{
    "job_id": "GeneralSegmentation-3a26e978-2ac3-4f55-8571-17e9617d8c5c",
    "status": "Success",
    "message": "The model job completed successfully"
}
```
	
There is a post-scoring job that runs after scoring takes place. It is not represented by the job status poll at this time. It may take a minute for results of scoring to show up in data retrieval API calls.

## Step 8: Retrieving data

To retrieve the results of the analytics after scoring is done, enter the following command:

```
curl -X GET -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/segments
```
{:codeblock}

The following is the first six segments from a successful request returning segments for the GeneralSegmentation model. It has been reformatted for readability.

```
[
    {
        "column_name": "CUSTOMER_AGE_RANGE_index",
        "rank": 1,
        "segment_id": "0",
        "effective_date": "2010-08-01",
        "max_value": 3,
        "score_code": "SEGMENTATION",
        "min_value": 0
    },
    {
        "column_name": "CUSTOMER_AGE_RANGE_index",
        "rank": 1,
        "segment_id": "5",
        "effective_date": "2010-08-01",
        "max_value": 3,
        "score_code": "SEGMENTATION",
        "min_value": 0
    },
    {
        "column_name": "CUSTOMER_AGE_RANGE_index",
        "rank": 1,
        "segment_id": "6",
        "effective_date": "2010-08-01",
        "max_value": 3,
        "score_code": "SEGMENTATION",
        "min_value": 0
    },
    {
        "column_name": "CUSTOMER_AGE_RANGE_index",
        "rank": 1,
        "segment_id": "8",
        "effective_date": "2010-08-01",
        "max_value": 3,
        "score_code": "SEGMENTATION",
        "min_value": 0
    },
    {
        "column_name": "CUSTOMER_EDUCATION_LEVEL_index",
        "rank": 1,
        "segment_id": "3",
        "effective_date": "2010-08-01",
        "max_value": 4,
        "score_code": "SEGMENTATION",
        "min_value": 0
    },
    {
        "column_name": "CUSTOMER_EDUCATION_LEVEL_index",
        "rank": 1,
        "segment_id": "4",
        "effective_date": "2010-08-01",
        "max_value": 4,
        "score_code": "SEGMENTATION",
        "min_value": 0
    },
    ...
]
```

The CrossSellProduct model returns multiple scores per customer when you enter the following command:

```
curl -X GET -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/customers/scores
```
{:codeblock}

## Step 9: Setting the number of records returned

You can set the number of records returned. For example, to return the first 10 records on the second page, you would set the page size to 10 and page to 1, using the following command:

```
curl -X GET -H "Authorization: <api-key>" -H "Content-Type: application/json" https://ci-api.mybluemix.net/api/v2/wealth/segments?page_size=10^&page=1
```
{:codeblock}

The following is an example of a successful request returning segments for the GeneralSegmentation model. It has been reformatted for readability.

```
[
    {
        "column_name": "CUSTOMER_AGE_RANGE_index",
        "rank": 1,
        "segment_id": "8",
        "effective_date": "2010-08-01",
        "max_value": 3,
        "score_code": "SEGMENTATION",
        "min_value": 0
    },
    {
        "column_name": "CUSTOMER_EDUCATION_LEVEL_index",
        "rank": 1,
        "segment_id": "3",
        "effective_date": "2010-08-01",
        "max_value": 4,
        "score_code": "SEGMENTATION",
        "min_value": 0
    },
    {
        "column_name": "CUSTOMER_EDUCATION_LEVEL_index",
        "rank": 1,
        "segment_id": "4",
        "effective_date": "2010-08-01",
        "max_value": 4,
        "score_code": "SEGMENTATION",
        "min_value": 0
    }
]
```

In this example, you can see the segments match the last 3 records of the example in Step 8, showing how the offset was customized to return the second set of 3 records.
