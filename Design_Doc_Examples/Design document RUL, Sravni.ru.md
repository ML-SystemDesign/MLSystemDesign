## **Failure Monitoring System** 

## **rus: Система мониторинга отказоустойчивости оборудования**

## **I. Problem definition**

### **i. Origin**

Modern technology is becoming increasingly complex and integrated. Ensuring reliable system performance is critical to the smooth operation of information systems. Hardware failures can lead to system downtime, data loss, and loss of productivity. Reliability analysis helps to identify weaknesses in equipment, determine the causes of failures, and develop measures to prevent them.

Equipment failure statistics are collected from various sources such as event logs, failure reports, and user feedback. Predictive management systems reduce operation and maintenance costs by detecting, diagnosing, predicting, and proactively managing equipment degradation leading to failure.

Our goal is to develop a surveillance system that predicts the remaining useful life (RUL) and Failure Prediction (FP) of degrading equipment. Using predictions, engineers can plan maintenance, optimize operational efficiency, and avoid unplanned downtime.

Our solution should cover 2 types of models: **static** and **dynamic**. **Static** forecasting is used for quick analysis of new equipment and is based on the point status of the device. **Dynamic** forecasting analyzes the history of performance measurement and is used to accurately analyze long-supported equipment.

### **ii. Relevance & Reasons**

One of the key problems in predictive maintenance is to predict equipment failures early enough to perform maintenance before breakdown. The problem is posed in two ways:

1. Remaining Useful Life (RUL) estimates how much time is left before the equipment reaches the end of its useful life. It is useful for long term planning of spare parts supply and maintenance scheduling.   
2. Failure Prediction (FP) estimates the probability that a failure will occur within a typically short period of time. It is more useful for handling unexpected failures that may occur in a short period of time. 

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

There are several proxy-metrics:

1. Time-to-event ordering:  
   [**Concordance Index (CI)**](https://www.sciencedirect.com/science/article/pii/S1532046420301246) is a ratio of correctly ordered pairs relative to the event time. The metric uses point forecasts and does not evaluate the whole survival function.  
2. Quality of survival function:  
   [**Integrated Brier Score (IBS)**](https://www.jmlr.org/papers/v21/18-772.html) is based on calculating the squared deviation of the predicted survival function from the true one. For an event with a time T, the true function equals 1 before the event occurs and 0 after.  
   [**Survival area under Precision-Recall curve (AUPRC)**](http://proceedings.mlr.press/v115/avati20a.html) measures the quality of the threshold of the survival function in various areas of the true time. In the case of terminal events, the quality assessment is reduced to averaging the prediction quality at different intervals. The metric allows estimation of the error for a single observation and is stable with respect to the distribution.  
3. Quality of hazard function:  
   [**Integrated area under ROC curve (IAUC)**](https://journals.sagepub.com/doi/full/10.1177/0962280213515571?casa\_token=cDx25kFY9d0AAAAA%3A0Z3Gc4ji20fYvnKMDiX-ozrh2pUFSAbe7ayic\_Pjzndqf\_xyv8\_2OA6iOpA7F3pJhsUYWHuR7ffG) measures the weighted proportion of right-ordered pairs (by cumulative hazard) of observations for partial times (occurred events should have a higher cumulative hazard at time t) and integrated by the whole timeline.  
   

**Conclusions:** we need at least two quality metrics to evaluate: 

1.  Quality of ranking time-to-event  (Concordance Index)  
2.  Quality of ranking continuous functions (Integrated area under ROC curve)

Additionally, the properties of the functions can be evaluated using IBS and AUPRC metrics.

### **ii. Loss Functions**

1. **Static (tree-based) approach**

To build static models, it is recommended to use the IBS metric or likelihood to prune the tree and select the number of trees in ensembles. [**Full likelihood**](https://www.jmlr.org/papers/v20/18-424.html) estimates the joint probability of occurrence of events at true moments of time. [**Partial likelihood**](https://www.jmlr.org/papers/v20/18-424.html) estimates the adjusted probability of occurrence of events given the Cox assumption. The metrics use just one point of the survival and hazard function and tend to robust models (decreases with rare strong errors).

2. **Dynamic approach**  
1. [Discrete two-parameter Weibull log-likelihood function](https://www.researchgate.net/publication/341823148\_Revisiting\_Maximum\_Log-Likelihood\_Parameter\_Estimation\_for\_Two-Parameter\_Weibull\_Distributions\_Theory\_and\_Applications).  
   The most commonly used distributions in survival-analysis and reliability engineering are the exponential, log-logistic, Rayleigh, Erlang and the Weibull distribution in the continuous case and the poisson, geometric and the discrete weibull in the discrete case.  
   If a system breaks with the failure of any of its independent identical components then the time to failure is approximately Weibull distributed. Even if this is not true, it is a good enough approximation of the true distribution.  
   The Weibull distribution is simple enough to avoid sparsity and can easily be regularized to avoid overfitting but is still expressive enough to encode concepts like increasing, stationary or decreasing risk and can converge to a point-estimate if allowed.  
2. [A three-component loss for censoring data.](http://proceedings.mlr.press/v80/chapfuwa18a.html) First component measures the correspondence of uncensored data to their distribution. The second component evaluates the closeness of forecasts (separately for censored and terminal data). The third component penalizes the model for scattered predictions.

**Conclusions:** To build static models, it is recommended to use the IBS metric and likelihood to prune the tree and select the number of trees in ensembles. For building dynamic models (RNN) we will use discrete Weibull loss.

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

For static models, quality assessment is performed on nested-cross-validation with stratification. Stratification is performed on 10 quantiles of time distribution and class balance by event flag.  
For both models, the raw data are divided into training and test samples. Observations are considered independent, there are no equipment duplicates in the samples. A model with the best hyperparameters is trained on the training sample (selection by internal cross-validation with stratification), while the expected time of event occurrence and the hazard function are predicted on the test sample (using events from the same time scale does not lead to estimation bias). The CI metric is calculated for the expected time and the IAUC metric for the hazard function. We check the quality of the ranking of events by the risk of the event at the time of status.   
**The scheme replicates real-world usage:** the model is trained on one piece of equipment, and hazard prediction is performed on new equipment. After the equipment breakdown, the sequence of observations is already collected on it and the dynamic model can be applied.

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
* Statistical features using Deep Feature Synthesis ([DFS](https://featuretools.alteryx.com/en/stable/getting\_started/afe.html)). DFS is an automated method for performing feature engineering on relational and temporal data.  
* Intrinsic Mode Functions using empirical mode decomposition (EMD)  
* Fitting Curve Derivative Method of Maximum Power Spectrum Density ([FDMPD](https://www.researchgate.net/publication/349333837\_Feature\_Extraction\_for\_Data-Driven\_Remaining\_Useful\_Life\_Prediction\_of\_Rolling\_Bearings))

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