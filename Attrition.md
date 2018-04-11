---

copyright:
  years: 2018
lastupdated: "2018-04-04"

---

<!-- Common attributes used in the template are defined as follows: -->
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}

# Attrition

## Purpose

This model predicts how likely the event of attrition will occur in the life of a client within a certain period of time in the future (Forecast Horizon). Attrition is defined by the client being inactive or the value of the client's Assets Under Management (AUM) drops by a certain percentage over the period of a month. This percentage can be set in the API parameters.

## Model Usage

This model allows the organization to identify the clients who have a high likelihood of living or transferring their assets to another firm within a certain period of time. The model does this by tracking seasonality trends: learning about other clients who experienced attrition in that same period of time in previous years.

## Model Overview

| Purpose | Predict when a client will suffer attrition | Description |
| :--- | :--- | :--- |
| Input | Demographic and Assets Information about client |
| Model | Accelerated Failure Time (AFT) model in PySpark | AFT is a parametric survival analysis model that measures the time to a failure of an event - attrition  |
| Target | Attrition | This happens if a client's asset drops by a set percentage or the client becomes inactive |
| Output | Probability of attrition, sensitive attributes, segments per client | The sensitive attributes are those that played a relatively larger role in determining the probability scores |

## Inputs and Outputs

The input for the model consists of demographic and summary information about the client. The summary and demographic information is analyzed at a client level.
The model input data structure is a wide form data structure (multiple rows per client), organized by Customer ID as the key field.

The wide-form data is constructed from the following tables:

* Customer
* Customer Summary
* Account

The fields available are from these tables but a recommended list of attributes is provided.

## Recommended Attributes

| Field | Details | Format |
| :--- | :--- | :--- |
| Age Range |  Age of the client | integer |
| Gender | Gender of the client | string |
| Education Level| The highest level of degree obtained by the person | string |
| Employment Status | Employment status of the client | string |
| Number of Dependent Children | The count of dependent children at the effective date | integer |
| Marital Status | The legal status of the client with regards to marriage | string |
| Urban Code | The type of area where the client lives | string |
| Total Amount of Deposits | Total amount of deposits the client has made| double|
| Number of Dependent Adults | The count of dependent adults at the effective date | integer |
| Average Sentiment Score | The average score based on opinion analysis of the client's communication | integer |
| Total Assets | Total value of the assets of the client | double |
| Annual Income | Annual income of the client | double |
| Total Funds Under Management | Total funds under management of the client | double |
| Internet Banking Indicator | Does the client have web banking?| boolean |
| Family Size| The count of the number of members of the client's family | integer |
| Relationship Start Date | Date when the client started their current relationship with the institution  | date |
| Status | Is the client Active or Inactive | boolean |
| Total Sell Transaction Quantity | The total number of sell transactions made by the client | integer |
| Total Fee Amount | The total fee-based management expenses the client is charged by the institution  | double |

## Missing Data and Conversions

1. The Status and Funds Under management are used to calculate attrition which is the target event.
2. Fields are removed from the model input that don't have any values.
3. For numeric fields, missing values are filled with the mean of the column.
4. For the string and boolean fields, missing values are filled with the mode of the column.
5. A field called period is calculated using the date when the client joined the company and the last date of the month in which client's attrit for client's that attrited. For non-attrited clients, it uses the most recent effective date.

## Outputs

1. A propensity score that shows the probability of the client attriting within the set forecast horizon.
2. A sensitivity summary identifying which attributes contributed the most to predicting the propensity score.
3. A segment Id assigned to each customer ID for the customer score table.
4. A segment summary, identifying the features that distinguish each segment from the overall client base.

## Affected Data Tables

The wide form data is created by joining the following tables:

* Customer
* Customer Summary
* Account

## Model Assumptions

* The model assumes a forecast horizon (usually an integer to represent months) is passed in and it predicts which clients will leave in the period between when the model is run and the forecast horizon
* A period in a model is the duration of a month
* The model assumes everyone is going to attrit some day
* The most recent effective date for a client is their current record

## Definition of API Parameters

| Parameter Name| Description | Example |
| :--- | :---------- | :--- |
|forecast_horizon| This refers to the number of months in the future for which attrition is being predicted| 6|
|AUM_reduction_threshold| This is the ratio by which the assets under management (AUM) will drop by for attrition to take place| 0.75|
|quantile_probabilities| This is a list of probabilities between 0 & 1|[0.1,0.5,0.9]|
|testperc| The ratio to set apart for testing the model| 0.2|
|set_attributes| A list of attributes to be used by the model. If no list of attributes is passed, the model uses the recommended attributes above. |
