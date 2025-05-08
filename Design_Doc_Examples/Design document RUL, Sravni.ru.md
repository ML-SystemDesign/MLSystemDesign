## **Failure Monitoring System** 

## **I. Problem definition**

### **i. Origin**

Modern technology is becoming increasingly complex and integrated. Ensuring reliable system performance is critical to the smooth operation of information systems. Hardware failures can lead to system downtime, data loss, and loss of productivity. Reliability analysis helps to identify weaknesses in equipment, determine the causes of failures, and develop measures to prevent them.

Equipment failure statistics are collected from various sources such as event logs, failure reports, and user feedback. Predictive management systems reduce operation and maintenance costs by detecting, diagnosing, predicting, and proactively managing equipment degradation leading to failure.

Our goal is to develop a surveillance system that predicts the remaining useful life (RUL) and Failure Prediction (FP) of degrading equipment. Using predictions, engineers can plan maintenance, optimize operational efficiency, and avoid unplanned downtime.

Our solution should cover 2 types of models: **static** and **dynamic**. **Static** forecasting is used for quick analysis of new equipment and is based on the point status of the device. **Dynamic** forecasting analyzes the history of performance measurement and is used to accurately analyze long-supported equipment.

### **ii. Relevance & Reasons**

One of the key problems in predictive maintenance is to predict equipment failures early enough to perform maintenance before breakdown. In practice, complete data on equipment failures are not available. For example, equipment may have been sent for maintenance or disposed of. Such observations are called censored observations. The most common is right censoring, where the time to exit the study is determined before the event occurs.

![](https://github.com/iuliivasilev/MLSystemDesign/blob/main/Design_Doc_Examples/rul-image/DataCollection.jpg)

We consider two target variables: the event time T and the censoring flag C. The feature vector X is the status of equipment at entry into the study (static) or a chain of statuses (dynamic). The figure depicts the process of determining the target values. All observations are centered on the zero point in time. The problem is solved by predicting two values:

1. Remaining Useful Life (**RUL**) is the mathematical expectation of time **E(T|X)**, that estimates how much time is left before the equipment reaches the end of its useful life. It is useful for long-term planning of spare parts supply and maintenance scheduling.   
2. Failure Prediction (**FP**) or hazard function is the probability of an event in time **P(t=T|X)**, that estimates the probability that a failure will occur within a typically short period. 

The system uses data from device sensors, analyzes it and produces estimates of the time and probability of failure. According to the predictions, we rank equipment and notify technicians which components need repair (email, text messages or alarm database). Following tasks can be solved with our system:

1. **Choose the best quality equipment for long-term exploitation**. Equipment candidates can be pre-test during one day and then based on the collected data system will predict the service life of the equipment. Based on the predictions the best ones will be used in production.  
2. **Scheduling repair works and replacement.** At the end of each workday, the system forecasts the likelihood of equipment breakdown. If any equipment's probability exceeds the predefined threshold, employees will arrive early the next day to replace it. The used equipment can then be either depreciated or repaired.  
3. **Monitoring intraday quality.** A single equipment breakdown can trigger a series of additional failures, making it crucial to monitor the condition of all components throughout the day to prevent such cascading issues.

**Objectives:**  
1\. Interpretable forecasts for individual equipment.  
2\. Work with censored cases. Censoring means that the true failure time is unknown. For example, if the equipment is decided to be replaced with new one.  
3\. On demand/scheduled forecasts (frequency is set externally and does not exceed 1 minute).  
5\. An alerting and load balancing system operates based on the forecasts (represented as recommendations for system administrator).  
6\. Scaling of the system: increase of equipment types (for each its own model), meta-analysis of forecasts (sequence analysis).

**Antiobjectives:**  
1\. Formalisation of equipment status (selection of sensors) and organization of data collection at the client.  
2\. We are not considering the case where data is left-censored or interval censoring. For right censoring, we take into account the fact of the event as well as the disposal or maintenance of the equipment (going out of service without breakage). The other types of censoring add cases where the fact of breakdown was ignored and the true time is unknown.  
3\. Streaming storage. The system/service is located on the customer's side. Data is not transmitted or stored on third-party sources.

**iii. Previous work**  
In the literature, a number of techniques have been used for the RUL estimation and failure prediction, mostly utilizing the temporal models using time-series analysis, explicit degradation modeling, hidden Markov models, and deep learning methods.

Related fields:

1. **Healthcare**. In medical research models estimate the life expectancy of patients with certain diseases. The outcome of the patient's illness is considered as an event. Statistical non-parametric (Kaplan-Meier, Nelson-Aalen), parametric (Accelerated Failure time) and semi-parametric methods (Cox Proportional Hazards) are used.   
   Compared to equipment, there is slightly less control over observations and many unexplained outliers (due to unaccounted for factors and body characteristics).  
2. **Credit scoring** models determine the probability of a borrower defaulting on a loan. The event is the borrower's default and the model tries to predict the probability of this event over a certain period of time.  
3. **Click lifetime value** (CLTV) in marketing. An event can be the termination of a customer's interaction with a brand or the cancellation of a purchase. The CLTV model is used to predict the length of a customer's "life" and their value to the company.  
4. **Sociological research**. The event is, for example, a job change, a relocation or divorce. Models aimed at predicting the likelihood of these events in the future.

### **iv. Other issues & Risks**

1. Changing infrastructure (new engines and sensors)  
2. The problem with fine-tuning: there will be dominance of early terminal or late censored events.  
3. Estimating prediction accuracy is straightforward when equipment fails, as you can easily measure the time between the failure and the prediction. However, if the equipment is discontinued, assessing accuracy becomes much more complex and may even be impossible.  
4. Deal with multiple operating conditions and diversified degradation patterns   
5. Feature selection and efficient feature comprehension relies heavily on labor and requires extensive domain knowledge (prior knowledge of the entire system)

## **II. Metrics and losses**

### **i. Metrics**

There are several selfmade metrics:

1. **Operational efficiency**  
   The percentage increase in operating time compared to continuous use. For example, if the operating time is increased by 30 percent, the equipment will run 30 percent longer before needing replacement. The total operating time of the equipment (between maintenance) is taken as the operating time.  
   In addition, the payback of the equipment can be calculated.  
2. **Investment efficiency (empirical)**  
   Reduction in replacement costs by increasing the number of repairs.  
   Assume:  
- (R) replacement \= (V) equipment cost  
- (E) operation \= V / time from manufacturer \* time to failure  
- (M) maintenance \= k % \* V (k set by customer).  
  Efficiency is calculated as the sum of cost/gain for all equipment.   
  For terminal events: If the forecast is less than the true failure time, the profit is ‘R \+ E \- M’ (we had time to test the equipment before it broke down). If the prediction is greater, the profit is 0 (will not operate after a true failure).  
  For censored events: If the prediction is less than the exit time, then ‘E \- M’. If the forecast is larger, then ‘M \- E \+ R \* (1 \- FP)’.  
3. **Risk reduction (optional)**  
   The magnitude of risk reduction for each point in time. It is calculated as the average cumulative risk function divided by the sum of accumulated critical failures at each point in time.

### **ii. Loss Functions**

1. **Static and dynamic approaches**  
* Time-to-event ordering:  
  1. [**Concordance Index (CI)**](https://www.sciencedirect.com/science/article/pii/S1532046420301246) is a ratio of correctly ordered pairs relative to the event time. The metric uses point forecasts and does not evaluate the whole survival function.  
* Quality of survival function:  
  2. [**Integrated Brier Score (IBS)**](https://www.jmlr.org/papers/v21/18-772.html) is based on calculating the squared deviation of the predicted survival function from the true one. For an event with a time T, the true function equals 1 before the event occurs and 0 after.  
  3. [**Survival area under Precision-Recall curve (AUPRC)**](http://proceedings.mlr.press/v115/avati20a.html) measures the quality of the threshold of the survival function in various areas of the true time. In the case of terminal events, the quality assessment is reduced to averaging the prediction quality at different intervals. The metric allows estimation of the error for a single observation and is stable with respect to the distribution.  
* Quality of hazard function:  
  4. [**Integrated area under ROC curve (IAUC)**](https://journals.sagepub.com/doi/full/10.1177/0962280213515571?casa_token=cDx25kFY9d0AAAAA%3A0Z3Gc4ji20fYvnKMDiX-ozrh2pUFSAbe7ayic_Pjzndqf_xyv8_2OA6iOpA7F3pJhsUYWHuR7ffG) measures the weighted proportion of right-ordered pairs (by cumulative hazard) of observations for partial times (occurred events should have a higher cumulative hazard at time t) and integrated by the whole timeline.

We need at least two losses to evaluate: quality of ranking time-to-event  (Concordance Index), quality of ranking continuous functions (Integrated area under ROC curve). Additionally, the properties of the functions can be evaluated using IBS and AUPRC metrics.

2. **Static approach**

To build static models, it is recommended to use the IBS metric or likelihood to prune the tree and select the number of trees in ensembles. [**Full likelihood**](https://www.jmlr.org/papers/v20/18-424.html) estimates the joint probability of occurrence of events at true moments of time. [**Partial likelihood**](https://www.jmlr.org/papers/v20/18-424.html) estimates the adjusted probability of occurrence of events given the Cox assumption. The metrics use just one point of the survival and hazard function and tend to robust models (decreases with rare strong errors).

3. **Dynamic approach**  
1. [Discrete two-parameter Weibull log-likelihood function](https://www.researchgate.net/publication/341823148_Revisiting_Maximum_Log-Likelihood_Parameter_Estimation_for_Two-Parameter_Weibull_Distributions_Theory_and_Applications).  
   The most commonly used distributions in survival-analysis and reliability engineering are the exponential, log-logistic, Rayleigh, Erlang and the Weibull distribution in the continuous case and the poisson, geometric and the discrete weibull in the discrete case.  
   If a system breaks with the failure of any of its independent identical components then the time to failure is approximately Weibull distributed. Even if this is not true, it is a good enough approximation of the true distribution.  
   The Weibull distribution is simple enough to avoid sparsity and can easily be regularized to avoid overfitting but is still expressive enough to encode concepts like increasing, stationary or decreasing risk and can converge to a point-estimate if allowed.  
2. [A three-component loss for censoring data.](http://proceedings.mlr.press/v80/chapfuwa18a.html) First component measures the correspondence of uncensored data to their distribution. The second component evaluates the closeness of forecasts (separately for censored and terminal data). The third component penalizes the model for scattered predictions.

**Conclusions:** To build static models, it is recommended to use the IAUC or IBS metric (for FP) and CI (for RUL) to prune the tree and select the number of trees in ensembles. For building dynamic models (RNN) we will use discrete Weibull loss.

## **III. Dataset**

### **i. Data Source**

As a practical example of how we can approach predictive maintenance using sensor data, we will predict the likelihood of failure and remaining useful life using sensor data from BackBlaze (for static model) and C-MAPSS (for RNN model) datasets. 

BackBlaze data-center maintains the record of the hard disk and any failures encountered by hard disk’s manufacturer and make.The data consists of 26 S.M.A.R.T. (Self-Monitoring, Analysis and Reporting Technology) features like temperature, error rates and other performance metrics aggregated for each day. Suitable for static baseline solution.

C-MAPSS data consists of multiple multivariate time series measurements. There are 21 sensor measurements and three operational settings. Each row is a snapshot taken during a single operation time cycle, for a certain engine. The engine is operating normally at the start of each time series, and develops a fault at some point. Suitable for dynamic baseline solution.

### **ii. Data Labeling**

Data collection is performed on request/scheduled basis. The database stores information about the observation status at a certain point in time. After a failure occurs (or is sent for maintenance/disposal), all observations during operation are assigned an event time (event time \- observation time) and an event flag.

There should be 2 target variables in the training data: event time and event fact.   
The **static** model is fed with information about the state of the equipment at a particular point in time (working with new or repaired equipment).   
The **dynamic** model is fed with the chain of equipment parameters for the period of its operation up to a specific point in time. All time series of indicators should be divided into time intervals and synchronized in time. For noise filtering, you can use the signal acquisition window and extract the mean or median instead of the raw values.

## **IV. Validation Schema**

### **i. Requirements**

1. Data is received by schedule or by request  
2. If all equipment is repaired in time, a class imbalance (early breakdown after maintenance or no breakdown for a long time) will occur in fine-tuning.  
3. To demonstrate cost reduction it needs to run A/B tests on departments with and without prediction. Or theoretically compare with the basic approach (no prediction, wait for failure or scrapping by time).

### **ii. Inference**

The validation approaches are shown in the figure. For the static model, we consider a single status point at the time the equipment enters exploitation. For the dynamic model, we consider a chain of statuses, at the last of which we predict RUL and FP.

![](https://github.com/iuliivasilev/MLSystemDesign/blob/main/Design_Doc_Examples/rul-image/ValidationScheme.jpg)

**The scheme replicates real-world usage:** the model is training on one piece of equipment, and hazard prediction is performed on new equipment.  
Quality assessment is performed on nested-cross-validation with stratification. Stratification is performed on 10 quantiles of time distribution and class balance by event flag.  
The prediction is built for the last observation status, and then the quality metrics are calculated.

In splitting, **observations are considered independent**, and there are no equipment duplicates in the samples. Note that, unlike time series, we cannot separate the chain of a single observation. In the training sample, we must have complete information about the occurrence and time of the event for all observations in sample.

## **V. Baseline Solution**

We propose two approaches for analyzing equipment data. The static approach considers single state indicators and does not require training on a sequence of indicators. It can be used for the hot start of the model or when introducing new equipment. The dynamic approach analyzes sequences of indicators and is applied after accumulating a history of equipment behavior.

### i.  Nonparametric Static Baseline

Nonparametric survival analysis methods (Kaplan-Meier, Nelson-Aalen) allow the construction of estimates of expected failure times and survival analysis functions. Estimates are constructed from a sample of equipment without taking into account the characteristics, but have a higher accuracy than a given manufacturer's estimate.

### ii. Tree-based Static Baseline (interpretable)

To build individual predictions, we propose to use survival trees, which partition the trait space into samples with different survival functions. The models have a strong interpretation and deal with heterogeneous data: continuous and categorical traits and outliers.

### iii. Ensemble Static Baseline

To improve the quality of forecasting and the robustness of forecasts (to prevent overfitting of models), we propose to use survival tree aggregation models. In particular, the runaway and random survival forest methods assume equal contribution of each model. 

### iv. RNN Baseline

Predicting the distribution of future time-to-failure using raw time-series data from engine sensors as input of a step-to-step RNN (char-RNN) with a 2-dimensional output layer (alpha and beta output values). The parameter alpha is an indicator of where the expected value and mode of the distribution is positioned in time while the beta parameter is an indicator of the variance of our prediction (the larger the less confident).

## **VI. Error analysis**

1. Impact of outliers: rare and valuable equipment is broken. Resolved by changing thresholds (sensitive to changes).  
2. Aging and degradation of the entire equipment system  
3. Penalty for constantly replacing equipment at the top (replace when reliability improves by a certain percentage).  
4. Рredicting a Remaining Useful Life (RUL) that is shorter than the actual one is not as bad as overestimating the values.

## **VII. Training Pipeline**

### **i. Overview**

**The static model**

The static model is responsible for prediction based on point status and can be pre-trained on public data from equipment sources. It is machine-independent and is linked only to the sensor readings. The dynamic model relies on the analysis of sequences of observations and must be trained on sequences of equipment indicators of the target system. The model depends on the frequency of data capture in the system. Data collection and accumulation is necessary for fitting and deploying dynamic models.

**RNN**

The model estimates the distribution of time to the next event as having a discrete two-parameter Weibull distribution with parameters being the output of a recurrent neural network. The model is trained using a special objective function (log-likelihood-loss for censored data). The predicted Weibull-parameters can be used to predict expected value and quantiles of the time to the next event. It also leads to a natural 2d-embedding of future risk which can be used for monitoring and exploratory analysis.

The predictions of both models are the expected event time and survival function. Based on these, the quality of equipment ranking is assessed and a pool of models is selected for maintenance.

### **ii. Toolset**

1. Python as the primary programming language.  
2. [Survivors](https://github.com/iuliivasilev/dev-survivors?ysclid=lzstyzi418158060473) for static machine learning models.  
3. Keras for deep learning models.  
4. MLflow for tracking experiments and managing the machine learning lifecycle.   
5. (Optional) Docker for containerization and reproducibility.

### **iii. Data Preprocessing**

The data preprocessing stage should include:

1. Data cleaning: removal of duplicates, truncated and outlier events.  
2. Train-test split: splitting the dataset into training and validation sets, ensuring that they have similar time distribution and event class balance.  
3. RNN: removing constant features and normalizing between \-1 and 1 the min and max of each variable.

### **iv. Model Training**

Different types of models are considered in the training:

1. Baseline models:  
   Ranking of equipment by manufacturer's lifetime. Prediction of survival function with nonparametric Kaplan-Meier model on empirical service life.  
2. Machine learning models: Survival Trees, Random Survival Forest.  
3. Recurrent neural network (RNN)  
   The architecture is a LSTM network with Adam optimizer. Hidden layers use tanh-activation and the output layer uses the softplus. The samples in the training data are each subsequence between 0 and t with t ranging from 1 up to the latest observation of each engine. The last observation corresponds to the step preceding the failure event. The event flag represents whether the corresponding failure was observed or censored.  We predict the remaining useful life (RUL) of training engines at each possible time-step until the last one where the time-to-failure is equal to one so the target variable is a countdown function of the time.

Selection of the best hyperparameters is done by grid-search or Bayesian optimization.

### **v. Model Evaluation**

**Static model (decision tree)**  
The interpretation is preserved, as well as the obtained model metrics at the training and validation stage. 

**RNN**  
Predicted parameters create a two-dimensional embedding or vector representation for each engine current risk to fail. The parameters of the Weibull-distribution have a nice interpretation. Alpha is a location parameter so it gives us a prediction about when. The inverse of beta is a scale parameter so beta is roughly a prediction about dispersion or how sure we are about the predicted time. Plotting them is a tool to monitor the whole health of the engine system and allows customers to rank the engine parts by the predicted life time and probability to fail.

The quality of ranking is assessed using CI and IAUC metrics. When the customer provides data on the cost of equipment support and maintenance, it is possible to compare the costs for the selected pool of objects (top-N) for a given period (hyperparameter).

### **vi. FUTURE WORK AND EXPERIMENTATION**

In addition, it is necessary to carry out experimental studies:

1. Selection of equipment status monitoring frequency, required number (window) of observations for dynamic prediction.  
2. Different loss functions for RNN;  
3. Construction of stratified statistical models.  
4. Using state aggregation attributes for previous time points in static models.  
5. Adding external state attributes of the whole system (load, free space, ...).  
6. Consideration of quality degradation when the used attributes are reduced. Allow to reduce the load on sensors at the moment of obtaining the status of equipment.

## **VIII. Features**

Key criteria for feature selection:

1. Prediction quality. Traits should improve prediction accuracy.  
2. Understandability and interpretability. Traits that are easy to understand and interpret are more important for a monitoring system.  
3. Computational efficiency and complexity. Traits that require significant computational resources or a large amount of computation time are less favored.

The classes of features under consideration are:

1. Equipment data: model, whether it has been repaired/maintained, number of repairs/maintenance performed.  
2. Various available parameters from sensors at the time of measurement.  
3. Use of values from the last N measurements (lag features).  
4. Number of failures and warnings during operation.  
5. Aggregated features (min/max/sum/mean/std/median) with rolling windows with different window widths (N days/hours).  
   

Therefore, additional features should be extracted from the collected data. There are three types of methods to deal with the raw data: time domain analysis, frequency domain analysis, and time-frequency domain analysis. Here is a list of features that we plan to experiment with:

* Other statistics: kurtosis, skewness, peak-peak (P-P).  
* Statistical features using Deep Feature Synthesis ([DFS](https://featuretools.alteryx.com/en/stable/getting_started/afe.html)). DFS is an automated method for performing feature engineering on relational and temporal data.  
* Intrinsic Mode Functions using empirical mode decomposition (EMD)  
* Fitting Curve Derivative Method of Maximum Power Spectrum Density ([FDMPD](https://www.researchgate.net/publication/349333837_Feature_Extraction_for_Data-Driven_Remaining_Useful_Life_Prediction_of_Rolling_Bearings))

After feature creation we could analyze them to select the most relevant.

* Values analysis/Outlier detectors  
* Correlation analysis of feature interactions  
* Sequential Feature Selection wrapper method  
* Dimensionality reduction methods

We will use built-in methods in forest models to estimate the importance of features. Also, model interpretability methods like LIME or SHAP should be used for estimation of feature importance.

## **IX. Integration**

### **i. Fallback Strategies**

In production we use 2 models for different tasks: static model for quick analysis and dynamic for long-term predictions. In case of problems in feature creation or inconsistency of the model there are several backup options that are less complicated than the main one or based on simpler heuristics.

1. **Primary fallback:** The primary models are trained on a subset of the most significant features. They will be used if no feature drift or problems are detected within this subset. An RNN model is used for dynamic tasks, while a survival tree model is used for static tasks.  
2. **Secondary fallback:** The dynamic model is more data-demanding. Due to significant noise in the data or issues with sensors, the accuracy of predictions may be low. Therefore, it is essential to monitor the quality of the data. In the worst-case scenario, only the static model should be used for long-term predictions.  
3. **Tertiary fallback.** If the sensor system is broken and we can’t retrain models on fresh data, a nonparametric survival analysis method should be applied. It uses only historical samples and calculates the survival function as a step function, adjusting at each observed event time based on the number of subjects still at risk and the number of events that occur.

### **ii. API design**

The API connection should be designed to establish a clear contract between the system and its users. There are several key points for using the API:

**Send data from the sensor system to the server.**  
Each equipment condition update should be sent to the server with a post request.

***Content-Type:*** json

***Json example:***  
{ "equipment\_id": "1002342134",    
  "timestamp": "2024-01-11T14:23:45Z",  
  "sensor": \[ { "sensor\_id": "TEMP001",  
	         "type": "characteristic1",   
	         "value": 50.2},  
	       { "sensor\_id": "VIB002",   
	         "type": "characteristic2",   
	         "value": 0.81 }, … \] }

**Request model prediction for the equipment**  
Use the calculated features to request predictions. Since we have multiple models, one of the variables should indicate the model type.

***Request format:*** GET   
/predictions?query=\<query\_string\>\&equipment\_id=\<equipment\_id\>\&model\_type=\<static or dynamic\>\&version=\<model version\>  
***Response format:***

{ "predictions":   
\[{ "time": \<request\_time\>,  
   "equipment": \<equipment\_id\>,  
   “RUL”:\<time\_value\>,  
   “survival\_function”:\<array\>, }, ... \] }

**Request equipments status**

Request fresh information about equipment conditions. Users can get information for each piece of equipment or filter by type. Additionally, equipment can be sorted by work status: warehouse, in use, or depreciated. If the equipment is depreciated, the reason should also be included in the response.

***Request format:*** POST   
/set\_status?query=\<query\_string\>\&serial\_number=\<equipment\_id\>\&parameters=\<equipment\_parameters\>\&type=\<model\_version\>\&work\_status=\<work\_status\>\&depreciation\_reason=\<depreciation\_reason\>

***Response format:***  
{"equipment\_updating": \[  
{ "status\_code": \<int\>}, … \]}

### **iii. Releases**

### Model updates

* The frequency of updates will depend on the equipment's lifespan. It is essential to add additional samples to the database, particularly if the equipment's lifetime was shorter than the model predictions.   
* After training and validation of new static and dynamic models it is crucial to test them in shadow mode to validate in a real-world scenario.  
* Both performance metrics (CI and IAUC) should be monitored during the work day. Significant decreases in accuracy (fixed  threshold) can signal data drifts or model quality issues. These excesses should trigger an alarm mechanism and prompt the system to switch to fallback strategies.

### Platform updates

We are not focusing on infrastructure improvements; therefore, updates will depend solely on software changes or customer requirements.

### **iv. Operational concerns**

We need to consider feedback from customers, particularly from employees who work with the equipment, as it is crucial for preventing incorrect predictions. For instance, understanding failures that are not dependent on the state of the equipment and recognizing changes in usage conditions are vital. Four crucial examples include:

* Identifying equipment failures caused by external factors rather than the equipment's condition.  
* Noticing shifts in how and where the equipment is used, which may affect its performance and longevity.  
* Detecting variations in maintenance practices that could influence the equipment's reliability.  
* Observing environmental changes, such as temperature or humidity, that might impact the equipment's functionality.

[image1]: <data:image/png;base64,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>

[image2]: <data:image/png;base64,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>