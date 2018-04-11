---

copyright:
  years: 2018
lastupdated: "2018-04-11"

---

<!-- Common attributes used in the template are defined as follows: -->
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}


# Wealth Management: Life-Events Prediction

## Purpose

The Life-Events Prediction model is used to determine how likely clients are to experience a chosen life-event within a given forecast horizon. The model takes a multi-period approach using a combination of current and historic details of the client's past wealth and life-event, along with core demographic and summary communication information. The model provides a score (propensity) which allows the ranking of clients according to how likely they are to experience the life-event.

The model automatically scores all customers who haven't experienced the event over the last four months. Note that this is a parameter to the model. 

## Model Usage

This model can be deployed to support a more meaningful engagement with wealth clients. Wealth managers and marketers can use the model to understand what life events are likely to come up for their clients, which investment options are therefore the most relevant, and prioritize who to engage with. Marketers can also use this model's output to decide who receives direct marketing or adjust the offers customers receive.


## Model Summary

The model is trained by looking at client's events history for those who did (Positives) and others who didn't (Negatives) experience the predicted life-event.

The model assigns a propensity for the client to experience the life-event.

| Purpose | Identify and calculate life-event propensity |
| :--- | :--- |
| Input | Wealth and life-event history and demographic information |
| Target | Whether a client experienced the life-event previously |
| Output | Score for life-event propensity |


The model is based on a Random Forest algorithm from the Spark ML library. 
Random Forest is an ensemble algorithm whereby the model trains over multiple decision trees in parallel. Each decision tree is trained to separate positives (customers having the life-event) and negatives (customers who don't) based on the input features. This process allows the model to capture non-linear effects and feature interactions.  Having trained multiple decision trees, the model then combines the prediction of each tree, using a *majority voting* system, aggregating the prediction of each tree for each record to determine the final predictions. This approach reduces the variance and reduces overfitting in the model.  The model produced provides a prediction and probability of each outcome, in this case experiencing a chosen event.

## Data

The data for this model consists of the the client's wealth and life events history.

The model inputs are collected from the following tables:

- Event
- Event Type

## Inputs

The events time-series is captured in the EVENT table:

| **Field** | **Details** | **Example** | **Format** | **Requirement** |
| :--- | :--- | :--- | :--- | :--- |
|CUSTOMER_ID||243563|STRING|MANDATORY|
|EVENT_DATE||2017-02-03|DATE|MANDATORY|
|EVENT_TYPE_ID||Mention_Home_Purchase|STRING|MANDATORY|
|TIMESTAMP||987654321|LONG|RECOMMENDED|
|UNIQUE_ID_IN_SOURCE_SYSTEM||9003475897|STRING|OPTIONAL|

This table captures both wealth events and life-events. The EVENT_TYPE table provides the full meaning of the events occurring in the time-series. 

| **Field** | **Details** | **Example** | **Format** | **Requirement** |
| :--- | :--- | :--- | :--- | :--- |
|CATEGORY||Interaction|STRING|RECOMMENDED|
|DESCRIPTION||Advisor told about home purchase|STRING|OPTIONAL|
|EFFECTIVE_DATE||2017-01-01|DATE|MANDATORY|
|EVENT_TYPE_ID||Mention_Home_Purchase|STRING|MANDATORY|
|EXTERNAL_ID||HOME_PURCH_231431|STRING|OPTIONAL|
|LIFE_EVENT_INDICATOR|Flag specifying whether EVENT_TYPE_ID is a life-event|0|BOOLEAN|MANDATORY|
|NAME||Home purchase|STRING|OPTIONAL|
|PROOF_POSITIVE_INDICATOR||1|BOOLEAN|RECOMMENDED|
|PROOF_POSITIVE_OF_EVENT||HOME_PURCHASE|STRING|RECOMMENDED|
|SOURCE||Phone|STRING|OPTIONAL|
|SUNSET_DATE||2018-01-01|DATE|OPTIONAL|
|SUNSET_INDICATOR||1|BOOLEAN|RECOMMENDED|

Life-events are those EVENT_TYPE_IDs for which LIFE_EVENT_INDICATOR=1. 

Examples of life-events are:

- Birth of a child
- Marriage
- Divorce
- Retirement
- Home purchase
- Relocation
- Job change

Proof-positives are events that may directly derive life-events. For example, opening a joint account or provision of a marriage certificate could be used as proof-positives of a marriage life-event. Proof-positives must have PROOF_POSITIVE_INDICATOR=1 and the value of PROOF_POSITIVE_OF_EVENT must be the EVENT_TYPE_IDs (their associated life-event).

## Cleaning and Filling Data

The data preparation performs a number of functions identifying the fields to be used in the model and cleaning the data.

1. Historical Positives and Negatives are identified and derived attributes are created.
2. Two time-windows are used for each client:
	- One at the most recent observation month
	- One ranging back observation-window months back from the most recent observation month
3. Clients who don't have at least observation-window + forecast-horizon consecutive months of historical data available are filtered out from training.
4. Clients who don't have any events in the observation-window + forecast-horizon months prior to a life event are also filtered out from training.
5. Clients who don't have at least observation-window consecutive months of historical data available are filtered out from scoring.
6. Life-events for which we don't have at least 100 distinct clients experiencing the life event are filtered out from the life-event set.
7. Fields are removed from the model input that have more than 50% missing values for the numeric field or more than 10 categories for categorical fields.
8. Date fields are transformed to integer, calculating the difference between each policy's effective date and each respective date field.
9. Remaining missing values are set to -1 for numeric fields and *Unknown* for categorical fields.

## Model Outputs

An event propensity is assigned to each client.
