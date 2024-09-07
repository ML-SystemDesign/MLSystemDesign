# **Project Design Document**

### "Downtime Cost Estimation Based on Incident Outcomes in a Financial Marketplace"

### **Table of Contents**

1. [Document Purpose](#document-purpose)
2. [Abbreviations and Definitions](#abbreviations-and-definitions)
3. [Task Statement](#task-statement)
   - [Background](#background)
   - [Relevance and Reasons](#relevance-and-reasons)
   - [Description of Incident Types and Frequencies](#description-of-incident-types-and-frequencies)
   - [Existing Data Flow Analysis](#existing-data-flow-analysis)
   - [Domain Problems](#domain-problems)
   - [Task Solution Constraints](#task-solution-constraints)
   - [Existing Solutions](#existing-solutions)
4. [Metrics and Loss Functions](#metrics-and-loss-functions)
   - [Metrics](#metrics)
   - [Loss Functions](#loss-functions)
5. [Data](#data)
6. [Validation Scheme](#validation-scheme)
7. [Baseline Model Development](#baseline-model-development)

## Document Purpose

This design document outlines the steps for solving the task and is necessary for:

* Fixing and justifying decisions, assumptions, and methods/technologies used during project implementation;  
* Introducing a new project participant to the key aspects of the subject area and the project implementation process;  
* Enabling the analysis of the research quality and subsequent identification of potential errors, problems, failed hypotheses, etc.;  
* Forming further project artifacts based on the intermediate results of the research.

## Abbreviations and Definitions

* Company – A financial organization acting as the Client for the development of the design document.

* **DWH** – Data Warehouse

* **ETL** – Extract, Transform, Load

* **JIRA** – Task and incident management system

* **Snowflake** – A cloud-based data warehouse that provides capabilities for storing, processing, and analyzing large volumes of data.

* **Transaction** – A record of the sale of a service, including information about the product, the commission amount, and the time of sale.

* **Commission** – Financial remuneration received by the company for successful transactions.

* **Incident** – An event causing system downtime or a decrease in its performance.

## Task Statement

The task is to estimate the cost of downtime resulting from an incident for the company's products.

### Background

The company sells services for selecting products of various types of insurance, such as car, home, etc.

Each product has a storefront where the user can choose offers from various insurance companies and select the most suitable one.

The company's economy is built on commissions from each successful user transaction. Each such sale is logged, and it is known which product was sold, how much commission the company received from this product, and the transaction time. All data is stored in the Snowflake data warehouse.

### Relevance and Reasons

Estimating the cost of product downtime is important for the company as it allows determining financial losses due to service unavailability. This helps to understand which incidents are the most costly and when they occur, contributing to improved incident management and increased business efficiency.

### Description of Incident Types and Frequencies

#### **1. External Service Provider Failure**

**Description:** This is an incident when an external service that the company relies on to provide its services becomes unavailable or starts working incorrectly. This could be, for example, a credit history check service, a payment gateway, or a user identification service.

**Example:** The external API for checking insurance policies temporarily does not respond due to technical problems at the provider. 

**Frequency:** Occurs periodically, depending on the reliability of the external provider.

#### **2. DDoS Attack**

**Description:** A Distributed Denial of Service (DDoS) attack is a malicious action aimed at overloading the company's infrastructure by simultaneously sending a large number of requests from multiple devices. This leads to service unavailability for legitimate users. 

**Example:** Hackers launch a massive attack on the company's website, resulting in significant slowdowns or complete unavailability of the service. 

**Frequency:** Rare but can have serious consequences.

#### **3. Critical Software Failure**

**Description:** This is a critical error in the software that completely blocks the operation of a particular product or the entire system. Such failures often require urgent intervention by developers to fix the problem.

**Example:** An error in the code update prevents transactions from being completed on the insurance product storefront. 

**Frequency:** May occur randomly, usually during the introduction of new features or updates.

#### **4. Internal Infrastructure Unavailability**

**Description:** This incident occurs when one or more components of the company's internal infrastructure fail. This may be due to hardware failures, software errors, or network issues. 

**Example:** The internal database used to store information about users and transactions becomes unavailable due to hardware failure. 

**Frequency:** Occurs rarely but can lead to significant losses due to complete service unavailability.

### Existing Data Flow Analysis

Incident Recording:

* Incident registration: When an incident occurs, the incident management team creates a task in JIRA. The task contains the following data: incident identifier (INCIDENT_ID), incident start date and time (INCIDENT_START_DTM), incident end date and time (INCIDENT_END_DTM). Products (PRODUCT_NAME) will include Credits, Deposits, Loans, Insurance.  
* Data storage: Data from JIRA is automatically transferred to the Snowflake data warehouse for subsequent analysis.

Sales Logging:

* Sales registration: Each sale is logged, recording the event identifier (EVENT_ID), product name (PRODUCT_NAME), timestamp (EVENT_DATETIME), and income (INCOME).  
* User information: In some cases, the user identifier is recorded if the purchase was made with registration.  
* Data storage: All sales data is stored in a relational database.

### **Domain Problems**

- **Data Imbalance:** There are only 1-6 incidents per month, with an average of 2000-3000 sales per day, leading to a significant class imbalance.

- **Data Quality:** Incidents are manually added, which can reduce data accuracy.

- **Target Variable Gaps:** In most cases, the target value is 0 since system downtime rarely occurs.

- **Lack of True Labels:** There is no reliable data on losses during downtimes for model training.

- **Forecast Granularity:** Different incidents require different levels of forecast detail. The system should be able to estimate downtime at both the minute and day levels, with a minimum data unit of one hour.

### Task Solution Constraints

* Data is only available from 2022, which limits the historical context.
* The system is being developed for use by incident managers and product owners affected by the incident.
* There will be no need for a forecast earlier than a day after the breakdown, as incident reviews are conducted later. A one-day delay is acceptable to the business and gives engineers more freedom in model development.
* The company has both CPU and GPU resources, which will allow efficient hypothesis testing during model development. Using a GPU is less desirable and requires justification in the form of significant model quality improvement.
* The business wants an interpretable model to clearly see why it produced a particular result. A probabilistic assessment of the model's confidence in its answer is also required.

### Existing Solutions
None.

## Metrics and Loss Functions

  ### Metrics

   Our quality metric will be WAPE (WMAPE) - Weighted Absolute Percent Error as the least sensitive to distortions in the numerical series. 

  ![](https://github.com/PsihAnalitik/Design_document/blob/feature-add-image/pics/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-08-27%20%D0%B2%2022.11.43.png)

1. The absolute forecast error is calculated for each position (forecast minus actual, absolute value) — Absolute Error  
2. The sum of all actuals for all positions is found (total actual volume)  
3. The sum of all absolute errors is divided by the sum of all actuals — WAPE

  ### Loss Functions

   As the loss function for the baseline, we will try using MSE, MAE, RMSE. For more advanced models, we will try using quantile losses. Training a separate model to predict each quantile will allow us to assess the confidence interval of our error, which can be useful when interpreting results for the business. Since we cannot assess the real error during incident periods, it will be a plus if we can at least provide information on the probability that our losses were within a certain range of values. 

## Data
  In fact, there is no data labeling. During periods when there are no incidents, it is known how much revenue the marketplace products generated. During incidents, due to the downtime of any system, it is unknown how much revenue the company lost as a result of unrealized sales.
To solve this problem, it was decided to train the model on non-downtime data to estimate how much revenue each product brought in over a certain period of time, optimize the model quality on labeled data, and apply it to incident data. The model's predictions on such data will be the downtime estimate.
## Validation Scheme

The validation scheme for a single product will be as follows:
![](https://github.com/PsihAnalitik/Design_document/blob/kmo/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-08-27%20%D0%B2%2016.12.01.png)

We allocate a validation set that is time-equivalent to the duration of the incident. We train our model or calculate the baseline heuristic on **Train 1**. **Train 1** represents a dataset with revenue from a period prior to validation (the size will be determined experimentally). Validation will be used as the basis for tuning the hyperparameters of ML models. We then combine **Train 1** and **Val**, train a new model, and then obtain predictions on the incident data. As a proxy metric, we use the performance indicators on **Val**.

To better understand the scheme, we also add a dataset sample
![](https://github.com/eliassz/MLSystemDesign/blob/Downtime-Cost-Estimation/pics/Pasted%20image%2020240907175941.png)
and a time series plot.
![](https://github.com/eliassz/MLSystemDesign/blob/Downtime-Cost-Estimation/pics/Pasted%20image%2020240907172657.png)

## Baseline Models Development

### Naive Prediction by Averaging

This approach involves using the average target value from a previous time period equal to the downtime or several periods within the sales seasonality of the product.

**Example:** Product A was partially unavailable because some users couldn't access the payment service from 12:00 to 15:00 on Tuesday. Our baseline suggests using the average sales of this product during this time on Tuesdays over the past three weeks (as an example). We subtract the actual revenue obtained from this average to determine our losses.

### ARIMA and sARIMA Models

ARIMA (AutoRegressive Integrated Moving Average) models are used as an initial application of machine learning due to their simplicity in use.

### Prophet

This forecasting tool is useful for time series data and incorporates seasonal effects and trends.

### Linear Regression

Starting with linear models, we can further engage in feature engineering, which requires additional effort but may improve model performance.

### Gradient Boosting

By creating features such as past sales (averages, moving windows with mean, median, minimum, maximum calculations), sales frequency, and historical service prices, we can use gradient boosting on trees to achieve good model accuracy.

### Recurrent Neural Networks (RNN, GRU, LSTM)

Recurrent neural networks (GRU and LSTM) can be tested if needed, though they require more development time and computational resources.

### Transformers

If previous models indicate that neural network approaches are worthwhile, transformers can be tested for solving our task.
