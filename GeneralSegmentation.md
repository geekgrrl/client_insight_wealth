---

copyright:
  years: 2018
lastupdated: "2018-04-09"

---

<!-- Common attributes used in the template are defined as follows: -->
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}


# General Segmentation

## Purpose

This model provides a fresh perspective about your clients. Rather than the traditional demographic markers of age, income, gender, or other inherent attributes, this algorithm examines commonalities in client behaviors and segregates your client list into ten separate segments. Each one has its own profile, interests, and attributes.

## Model Usage

Behavior-based segmentation allows marketers to design differentiated, unique strategies targeting each segment. The segmentation helps you to understand how segments differ from one another, and to better understand and differentiate cohorts of clients. There are other creative applications of this information. For example, it can also be used to assign the best-fit agent, identify opportunities for leveraging agent skills, or even to create hosting opportunities to improve client engagement.

## Model Overview

| Purpose | Identify and assign clients to a segment | Description |
| :--- | :--- | :--- |
| Input | Demographic, summary product, and communication information |
| Model | Bisecting K-means algorithm from SparkML | Bisecting k-means is a divisive hierarchical model, where all clients start in one cluster and then are recursively split into further clusters |
| Target | Not applicable |  Unsupervised learning |
| Output | Segment ID per customer ID, Segment Description | Segment Description is the range of values that the five most important factos that define the segment take on  |

## Inputs and Outputs

The input for the model consists of demographic, summary product, and communication information. The summary product and communication information is analyzed at a client level, as opposed to looking at individual products or communications.

The model input data structure is a wide form data structure (one row per client), organized by Customer ID as the key field.

The wide-form data is constructed from the following tables:

* Customer
* Customer Summary
* Customer Product Summary
* Account
* Account Summary

The fields available are from these tables but a recommended list of attributes is provided.

## Recommended Attributes

| Field | Details | Format |
| :--- | :--- | :--- |
| Age Range |  Age of the client | integer |
| Gender | Gender of the client | string |
| Education Level| The highest level of degree obtained by the person | string |
| Employment Status | Employment status of the client | string |
| Number of Dependent Children |  The count of dependent children at the effective date | integer |
| Marital Status | The legal status of the client with regards to marriage | string |
| Urban Code | The type of area where the client lives | string |
| Number of Emails | The number of emails the client has sent to the institution | integer |
| Number of Logins | The number of logins the client has made into the institution's site | integer |
| Number of Calls | The number of calls the client has made to the institution | integer |
| Total Assets | Total value of the assets of the client | double |
| Annual Income | Annual income of the client | double |
| Total Funds Under Management | Total funds under management of the client | double |
| Return Last Quarter | The return for the client in the last quarter | double |
| Return Since Inception | The return for the client since inception | double |
| Relationship Start Date | Date when the client started their current relationship with the institution  | date |
| Total Number of Buy Trades | The total number of buy transactions made by the client | integer |
| Total Number of Sell Trades | The total number of sell transactions made by the client | integer |
| Total Fee Amount of All Fees | The total fee-based management expenses the client is charged by the institution  | double |
| Risk Tolerance | Number of accounts of each risk tolerance the client holds | string |
| Investment Objective | Number of accounts of each investment objective the client holds | string |

## Missing Data and Conversions

1. The data is checked so that only one record per active client is retained. The input fields are then defined, using either the default list or a separately supplied list of fields available in the wide-form data.
2. Fields are removed from the model input that have more than 10% missing values for numeric fields or more than 10 categories for categorical fields.
3. For numeric fields, values that are more than 5 standard deviations higher than the mean are set to this upper boundary value. Equally, values more than 5 standard deviations lower than the mean are set to this lower boundary value.
4. Remaining missing values are set to the mean for numeric fields and 'Unknown' for categorical fields.

## Outputs

1. A segment ID assigned to each customer ID for the customer score table.
2. A segment summary, identifying the features that distinguish each segment from the overall client base.


## Affected Data Tables

The wide form data is created by joining the following tables:

* Customer
* Customer Summary
* Customer Product Summary
* Account
* Account Summary
* Customer Score

## Model Assumptions

* The general segmentation model is trained and applied to all current clients
* All current clients are defined by the client status feature with a status equal to 'Active'
* Clients only have one row per effective date in the wide form data created
* The most recent effective date for a client is their current record

## Definition of API Parameters

| Parameter Name| Description | Example |
| :--- | :---------- | :--- |
|number_of_clusters| The number of segments to generate| 10|
|set_attributes| A list of attributes to be used by the model. If no list of attributes is passed, the model uses the recommended attributes above | |
