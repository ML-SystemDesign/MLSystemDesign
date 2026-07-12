# Mega retail

### **I. Problem definition**

### **i. Origin**

Supermegaretail is a retail chain operating through a network of thousands of stores across different countries in various regions. The chain’s customers buy various goods, primarily groceries, household essentials, personal care, sports supplements, and many more.

To sell these goods, Supermegaretail must purchase or produce them before delivering them to a store’s location. The number of purchased goods is the key figure that needs to be defined, and there are different possible scenarios here.

For easier calculations, we can assume that Supermegaretail brought 1000 units of Item A to the specific store.

1. Supermegaretail brought 1000 units and sold 999 until the next delivery. This is an optimal situation. With only 0.1% of leftovers, the retailer is close to the optimal revenue and margin.
2. Supermegaretail brought 1000 units and sold 100 until the next delivery. This is usually an awful situation for an apparent reason. Supermegaretail wants to sell almost as many units as it purchased without going out of stock. The more significant the gap, the more considerable Supermegaretail’s losses.
3. Supermegaretail brought 1000 units and sold 1000. This should be considered a terrible situation because we don't know how many units people would buy, had they an opportunity. It could be 1001, 2000, or 10,000. An out-of-stock situation like that obscures our understanding of the world. Even worse than that—it drives customers from Supermegaretail to its competitors, where they can buy the stuff with no shortages.

An additional constraint is that we have a lot of perishable foods and can’t wait too long; it’s either gone or wasted.

The project goal is to reduce the gap between delivered and sold items, making it as narrow as possible, while avoiding an out-of-stock situation with a specific service-level agreement (SLA) to be specified further. To do that, we plan to forecast the demand for a specific item in a specific store during a particular period with the help of a machine learning system.

### **ii. Relevance & Reasons**

*This section highlights the problem's relevance, backed by exploratory data analysis.*

**ii.i. Existing flow analysis**

What is the current way of ordering, delivering, and selling goods in Supermegaretail?

For Supermegaretail, the possible list might be as follows.

1. Planning horizon for making a deal with goods manufacturers:
2. It’s a one-year deal with the opportunity to adjust 90 days ahead within the first nine months.
3. Additional discount with an increased volume:
4. It's an extra 2% off for every additional 20M dollars.
5. The number of distribution centers serving as logistics hubs between manufacturers and stores:
6. There are 47 distribution centers around the country, making them a point of presence and aggregated entity for the forecast.
7. Delivery cadence between distribution centers and stores:
8. Usually, every two days there is a truck connecting the distribution center and the grocery store.
9. Presence or absence of in-store warehouses:
10. There’s no warehouses in most of the stores. However, the loading bay zone can be (and is) effectively used to store offloaded items for 2–3 days.
11. Who and at what stage decides what and where to deliver:
12. There’s a delivery plan coming down from the distribution center. A store’s manager can override and adjust it.
13. Forecast horizon:
14. The primary forecast horizon is week-long and month-long. However, a one-year horizon is needed when dealing with goods manufacturers.
15. Business owner of the process:
16. Logistics department.
17. Procurement department.
18. Operational department (store managers).

**ii.ii. How much does Supermegaretail lose on the gap between forecasted and factual demand?**

While it is relatively easy to calculate the loss due to overstock and expired items, it is much harder to calculate the loss due to out-of-stock situations. The latter can be estimated either through a series of A/B tests or an expert opinion, which is usually much quicker and cheaper than running those tests.

The overall loss can be approximated by summing up the two, providing an estimate of the gain with an ideal and non-achievable solution.

**Initial calculation showed the loss to be around 800M USD during the last year.**

*Starting from section 1.2.3 of the design document (but only for this chapter), we’ve sketched questions only to avoid this being too voluminous. Answering these questions will help decide on further actions, while the answers are to be revealed in the later chapters with us pacing through different stages of the system.*

**ii.iii. Other reasons**

- Can other teams use our solution, making development more appealing and reasonable?
- Perhaps we can sell demand forecast solutions to other retail companies (obviously not to direct competitors).

### **iii. Previous work**

*This section covers whether this is an entirely new problem or something has been done before. Usually, it is a list of questions you ask to avoid doing double work or repeating previous mistakes.*

- What if Supermegaretail was aware of this issue and had already implemented some demand forecast approach? It has various stores in different locations; its demand forecast is probably already pretty efficient. How do they do it?
- Rolling window?
- Experts committee?
- Rule of thumb + extra quick delivery?
- Do we have some limitations to consider that we can't avoid? Like minimum or maximum order size?
- Can we quickly improve the existing solution, or do we need an entirely new one?
- What if the Supermegaretail current forecasting is good enough for some categories and useless for others. In other words, can we use a hybrid approach here, at least in the very beginning and start doing from the least successful categories, where the existing gap between predictions and actual sales is the widest
- If our approach unintentionally breaks something, it is not that dangerous. We are testing it for categories where we always had problems while not touching categories where everything is good.
- In other words, we need to run an extensive and fresh exploratory data analysis of the existing solution

### **iv. Other issues & Risks**

- Do we have a required infrastructure, or do we need to build it?
- If we pick something sophisticated, it can go crazy. What necessary checks and balances do we need to implement to avoid a disaster? Do we have a fallback in case something is broken?
- How sure are we that we can significantly improve the quality and reduce the manual load? Can we really solve this?
- What is the price of a mistake? Probably out-of-stock and overstock have different costs of errors.
- If we deal with an out-of-stock situation, can we handle increased traffic?
- How often and on what granularity do we need to perform predictions?

As you can see, even a brief overview of the problem to solve and research using the previously gathered data can easily force us to write down a 10-page doc. This draft will help us decide if we need to go further or it is better to stop right now and avoid a complicated ML solution.

The next section of Chapter 4 is no less important though, as it gives a practical example on how to review a design document. If you’re new to ML system design, you probably haven’t reached the stage of your career where you have enough experience and credibility to be involved in this kind of a working routine. However, stepping up to review your first design doc is just a matter of time, so better be prepared beforehand, and you will see some practical advice on the reviewing basics.

### **II. Metrics and losses**

### **i. Metrics**

Before picking up a metric on our own, it makes sense to do a preliminary research. Fortunately, there are many papers related to this problem, but the one that stands out is *Evaluating predictive count data distributions in retail sales forecasting*.

Let’s recall the project goal, which is to reduce the gap between delivered and sold items, making it as narrow as possible, while avoiding an out-of-stock situation with a specific service-level agreement (SLA) to be specified further. To do that, we plan to forecast the demand for a specific item in a specific store during a particular period using a machine learning system.

In this case, this paper abstract looks like almost a perfect fit to address.

*Massive increases in computing power and new database architectures allow data to be stored and processed at increasingly finer granularities, yielding count data time series with lower and lower counts. These series can no longer be dealt with using approximative methods appropriate for continuous probability distributions. In addition, it is not sufficient to calculate point forecasts alone: we need to forecast the entire (discrete) predictive distributions, particularly for supply chain forecasting and inventory control, but also for other planning processes.*

(*Count data is an integer-valued time series. It is essential for the supply chain forecasting we are facing, where most products are sold in units.)

With that in mind, we can briefly review this paper (within the lettered list below) and pick the metrics that are most appropriate for our end goal.

**a. Measures based on absolute errors**

MAE optimizes the median, weighted mean absolute percentage error (wMAPE) is MAE divided by the mean of the out-of-sample realizations, and the mean absolute scaled error is obtained by dividing the MAE by the in-sample MAE of the random walk forecast.

Optimizing for the median does not differ much from optimizing for the mean in a symmetric predictive distribution. However, the predictive distributions appropriate for low-volume count data are usually far from symmetric, and this distinction makes a difference in such cases and yields biased forecasts.

**b. Percentage errors**

The mean absolute percentage error (MAPE) is undefined if any future realization is zero, so it is singularly unsuitable for count data.

The symmetric MAPE (sMAPE) is an ‘‘symmetrized’’ version of the MAPE, which is defined if the point forecasts and actuals are not both zero at all future time points. However, in any period with a zero actual, its contribution is two, regardless of the point forecast, making it unsuitable for count data.

**c. Measures based on squared errors**

Minimizing the squared error naturally leads to an unbiased point forecast. However, the mean squared error is unsuitable for intermittent-demand items because it is sensitive to very high forecast errors. The same argument stands for non-intermittent count data.

**d. Relative errors**

Prominent variations are the median relative absolute error (MdRAE) and the geometric mean relative absolute error (GMRAE).

In the specific context of forecasting count data, these suffer from two main weaknesses:

- Relative errors commonly compare absolute errors. As such, they are subject to the same criticism as MAE-based errors, as detailed above.
- On a period-by-period basis, simple benchmarks such as the naive random walk may forecast without errors, and thus, this period’s relative error would be undefined because of a division by zero.

**e. Rate-based errors**

Kourentzes (2014) recently suggested two new error measures for the intermittent demand,  MSR and MAR, which aim to assess whether an intermittent demand point forecast captures the average demand correctly over an increasing period of time. This is an interesting suggestion, but one property of these measures is that they implicitly weigh the short-term future more heavily than the mid-to-long-term future. One could argue that this is exactly what we want to do while forecasting, but even then, a case could be made that such weighting should be explicit, e.g., by using an appropriate weighting scheme when averaging over future time periods.

**f. Scaled errors**

Petropoulos and Kourentzes (2015) suggest a scaled version of the MSE, the sMSE, which is the mean over squared errors that have been scaled by the squared average actuals over the forecast horizon. The sMSE is well-defined unless all actuals are zero, is minimized by the expectation of *f* and, due to the scaling, can be compared between different time series. In addition (again because of the scaling) it is not quite as sensitive to high-forecast errors as the MSE. Specifically, it is more robust to dramatic underforecasts, although it is still sensitive to large overforecasts.

**g. Functionals and loss functions**

An alternative way of looking at forecasts concentrates on point forecasts that are functionals of the predictive distribution. One could argue that a retailer aims at a certain level of service (say 95%) and that therefore they are only interested in the corresponding quantile of the predictive distribution. This would then be elicited with appropriate loss functions or scoring rules. This approach is closely related to the idea of considering forecasts as part of a stock control system. In this perspective, quantile forecasts are used as inputs to standard stock control strategies, and the quality of the forecasts is assessed by valuing the total stock position over time and weighting it against out-of-stocks.

Though the authors did not see this as the best solution and proposed an alternative, the last paragraph of the paper review is quite promising. Not only does it make sense from a business perspective to predict different quantiles to uphold SLA, but it is desirable from the point of view of having the loss function equal to the metric. Thus, Quantile metrics for quantiles of 1.5, 25, 50, 75, 95, and 99 look like a proper choice. Moreover, suppose we need to pay more attention to a specific SKU, item group or cluster. In that case, Quantile metrics support the calculation of object/group weights (for example, item price).

**i.ii. Metrics to pick**

Quantile metrics for quantiles of 1.5, 25, 50, 75 and, 95, and 99 both as is and with weights equal to SKU price and an additional penalty for underforecasting or overforecasting if deemed necessary. Calculated as point estimates with 95% confidence intervals (using bootstrap or cross-validation). In addition , we can further transorm this metric, representing it not as absolute value, but absolute percentage error at a given quantile. All consideration from the article review above regarding percentage errors have to be taken into account. Ultimately, a set of experiments will help to decide a final form. Most probably we will have both, as it makes sense to check both absolute values in money\pcs and percentage error

Online metrics of interest during A/B test are:

- Revenue (expected to increase),
- Level of stock (expected to decrease or maintain the same),
- Margin (expected to increase).
- Alpha — coefficient used in quantile-based losses
- W — weights
- I — indicator function
- A — model output
- T — label

### **ii. Loss Functions**

With metrics equal to our loss functions, it is straightforward to pick the latter. We will train six models using a quantile loss of 1.5, 25, 50, 75, 95, and 99, resulting in six different models, providing us with various guarantees for the corresponding quantile of the predictive distribution.

As a second line of experimentation, we will additionally review the Tweedie Loss Function. Tweedie distributions are a family of probability distributions, including the purely continuous normal, gamma, and inverse Gaussian distributions, the purely discrete scaled Poisson distribution, and the class of compound Poisson–gamma distributions that have positive mass at zero but are otherwise continuous. These qualities make it an attractive candidate for out Count data.

### **III. Dataset**

The atomic object of the dataset is a bundle of (date, product, store), and the target variable we aim to predict is the number of units sold.

### **i. Data Sources**

There are multiple sources of data we can utilize for our objective.

### Inner sources

1. *Historical data on purchases (i.e. transaction history)* is collected from the chain of stores of Supermegaretail and saved to a centralized database. It will be our *primary source* of truth: the number of sales, the amount of money spent, the discounts applied, transaction ID, and so on.
2. *Stock history* is the second most important source of truth for our problem since it directly determines how many units of each product can be sold in each store. This source can help estimate how many products are available for sale at the beginning of each day and how many were expired and withdrawn from sale.
3. Metadata of *each product, store, and transaction*.
4. Calendar of planned *promo activities*. A significant factor affecting future sales that definitely needs to be taken into account.

### Outer sources: manually gathered data

1. *Price monitoring*. Prices and other product info collected from our competitors. They are manually gathered daily from a subset of stores of different competitors. It could be done either by our in-house team or by a third party (outsourced). A hybrid approach also can take place. Each product should also contain a global product identifier (barcode), so we can easily match collected data with our product. Knowing aggregated competitors’ prices and their dynamics helps us understand what is happening in the market.

### Outer sources: purchased data

1. *Weather history and its forecast* we buy from the meteorological service. Weather is an important factor directly affecting consumer behavior.
2. *Customer Traffic* estimation nearby our stores (from telecom providers).
3. *Global market indicators*.

### Mobile app and website data (optional)

1. Supermegaretail has a *delivery service* (even if it takes less than 5% of revenue). We will collect *additional data* about specific sales in a specific location. Sometimes this information can be a valuable predictor.
2. Also, mobile and web services collect implicit feedback about user activity, including views, clicks, or adding to the cart, which also can predict sales in physical stores.

### **ii. Data Labeling**

Since we are dealing with a demand forecasting problem, we don’t need extra data labeling derived directly from the transaction history.

### **iii. Available Metadata**

We forecast demand based on the SKU per store level, with three key elements: products, stores, and transactions.

### Products

- *Product ID and barcode*.
- *Category codes of different levels (1, 2, 3)*. We can use a hierarchy of categories for a rule-based measurement of similarity between products. Also, other categorical info like *brand, manufacturer, or pricing group*.
- *Shelf life* determines how bad it is to overpredict sales for this product.
- Date when the product was *added to the assortment matrix* of the chain.
- *Dimensions* and *weight* of the product.

### Stores

- *Store ID*.
- *Location (coordinates)*, with the support of third-party sources—we can use it to add information about the weather, flow of people, and distance to critical points. Other related stuff like city, region, and associated logistics center.
- *The nearest competitors’ stores* (with their IDs and distances).
- The *size of the store* and its *format*. They determine which products and how many unique products will be in the assortment of this store.
- The dates when the store is open and when it was closed.

### Transactions

- *Timestamp*. It allows us to enrich the dataset with things like holidays.
- *Customer ID* (if a loyalty card was applied). Despite the fact that the final unit of the dataset is (product, store), a bundle of (customer, product) can be used in a separate data pipeline for calculating product embeddings via aggregating transactions to a user-item matrix and its factorization. The embeddings will contain patterns of purchasing behavior.
- *Store ID* and *product ID*.

### **iv. Available History**

**Demand forecast is nothing new for Supermegaretail**. Critical ETL processes are already in place. Supermegaretail has been collecting data for more than three years.

This history is essential for our forecasting model to learn patterns, catching the seasonality of sales, estimate trends, etc. The same is applicable for products and stores metadata. Weather data (which we take from external sources) has been available for a period in the past as long as we need.

Stocks history and promo activities have been gathered as well.

Price monitoring data of competitors has been collected for the last 2 years.

### **v. Data Quality Issues**

Transactions, stock, and promo data may contain missing or duplicated values, so additional filtering or preprocessing is required before aggregation.

The external data we bought has already been cleaned and passed some quality control before coming to us. However, necessary checks to be implemented.

The competitors' prices cover about 25% of SKU and have gaps.

### **vi. Final ETL Pipeline**

The top-level scheme is as follows:

1. The transactions data is aggregated daily
2. Adding newly aggregated partition to the table of transaction aggregates
3. (optionally) we rewrite not only the last day but the previous 2-3 days too to fix possible corruptions in the data (duplicates, incomplete data, duplicated data, and so on)
4. We join other sources of internal/external data based on data, product ID, or store ID
5. Finally, we calculate features based on the joined dataset

Optionally, we can add a data pipeline for product embeddings, as described in **3.3. Available Metadata** section, if needed.

### **IV. Validation Schema**

### **i. Requirements**

What are the assumptions that we need to pay attention to when figuring out the evaluation process?

1. New data is coming daily.
2. Data can arrive with a delay of up to 48 hours.
3. New labels (number of units sold) come with the new data.
4. Recent data is most probably more relevant for the prediction task.
5. The assortment matrix changes by 15% every month.
6. There’s seasonality presented in the data (weekly/annual cycles).

Despite the fact that the data is naturally divided into categories, it is irrelevant for the choice of validation schema.

### **ii. Inference**

After fixing a model (within the hyperparameters optimization procedure), we train it on the last 2 years of data and predict future demand for the next 4 weeks. This process is fully reproduced in both inner and outer validation.

It is important to note that there should be a gap of 3 days between training and validation sets in order to be prepared for the fact that data may arrive with a delay. Subsequently, this will affect which features we can and cannot calculate when building a model.


### **iii. Inner and outer loops**

We use two layers of validation. The outer loop is used for the final estimation of the model’s performance, while the inner loop is used for hyperparameter optimization.

**Outer loop**. Given we are working with time series, rolling cross-validation is an obvious choice. We set K=5 to train five models with optimal parameters. Since we are predicting 4 weeks ahead, the validation window size also consists of 28 days in all splits. There is a gap of 3 days between sets and the step size is 7 days.

*Example of the outer loop:*

*1st outer fold:*

- *Data for the testing is from **2022-10-10** to **2022-11-06** (4 weeks)*
- *Data for the training is from **2020-10-07** to **2022-10-06** (2 years)*

*2nd outer fold:*

- *Data for the testing is from **2022-10-03** to **2022-10-30***
- *Data for the training is from **2020-09-29** to **2022-09-28***

*…*

*5th outer fold:*

- *Data for the testing is from **2022-09-12** to **2022-10-09***
- *Data for the training is from **2020-09-09** to **2022-09-08***

**Inner loop**. Inside each “train set” of the outer validation, we perform additional rolling cross-validation with a 3-fold split. Each inner loop training sample consists of a 2-year history as well to capture both annual and weekly seasonality. We use the inner loop to tune hyperparameters or for feature selection.

*Example of the inner loop (for the 2-nd fold of the outer loop):*

*Training data for the 2nd outer fold is from **2020-10-03** to **2022-10-02**.*

*1-st inner fold:*

- *Data for the testing is from **2022-09-05** to **2022-10-02** (4 weeks)*
- *Data for the training is from **2020-09-02** to **2022-09-01** (2 years)*

*2nd inner fold:*

- *Data for the testing is from **2022-08-29** to **2022-9-25***
- *Data for the training is from **2020-08-26** to **2022-08-25***

*3rd inner fold:*

- *Data for the testing is from **2022-08-22** to **2022-09-18***
- *Data for the training is from **2020-08-19** to **2022-08-18***

If the model does not require model tuning yet, we can skip the inner loop.


### **iv. Update frequency**

We update the split weekly along with new data and labels (so that each validation set always consists of a whole week). This will help us catch local changes and trends in model performance.

Additionally, we have a separate holdout set as a benchmark (a “golden set”). We update it every three months. It helps us track long-term improvements in our system.

### **V. Baseline Solution**

### **i. Constant Baseline**

As a constant baseline for Supermegaretail’s demand forecasting system, we plan to use the actual value of the previous day per SKU per grocery. Knowing that data sometimes could appear with delay and that grocery sales experience strong weekly seasonality, we will go one full week back instead of going one day back. As a result, our prediction for a specific item on Sep 8, 2022 will be the actual sales value for this item on Sep 1, 2022.

### **ii. Advanced constant Baseline**

The Metrics and Losses chapter mentioned quantile losses of 1.5, 25, 50, 75, 95, and 99th percentiles. We can calculate the same with our baseline using a yearly window.

### **iii. Linear model Baseline**

We will use a basic set of features to use linear regression with quantile loss; for a start, we can only use target variables but with multiple lags and aggregations like sum/min/max/avg/median or corresponding quantiles for the last 7/14/30/60/90/180 days or different rolling windows of different sizes. The same magic could be done with other dynamic data beyond sales date, like price, revenue, average check, or a number of unique customers.


### **iv. Time series-specific baseline**

ARIMA (Autoregressive integrated moving average) and SARIMA (seasonal ARIMA). Both are autoregressive algorithms for forecasting; the second one considers any seasonality patterns.

Both require fine-tuning multiple hyperparameters to provide satisfying accuracy. To avoid this, we may prefer a SOTA forecasting procedure that works great out-of-the-box and is called Prophet (https://github.com/facebook/prophet). The nice advantage of Prophet is that it's robust and doesn’t require a lot of preprocessing: outliers, missing values, shifts, and trends are handled automatically.

### **v. Feature Baselines**

What is additional information for some baselines and possible future models to benefit from?

We will include extra static info about products (brand, category), shops (geo features), and context (time-based features, seasonality, day of the week)—all of them with preprocessing and encoding appropriate for a chosen model.

Features that are also suitable for the baseline are counters and interactions.

Examples include:

- Difference between current and average price (absolute and relative)
- Penetration: the ratio of product sales to sales of a category (of levels 1, 2, 3) for rolling windows of different sizes
- Number of days since the last purchase
- Number of unique customers

etc.

### **VI. Error analysis**

Remember we have six quantile losses for 1.5, 25, 50, 75, 95, and 99th quantiles of the target and corresponding six models for each. Constant baseline estimates these quantiles for each product based on the last N days of its sales. These baselines already have some residual distribution with some specific bias that is helpful to consider.

Comparing more complex models (linear models and gradient boosting) with these dummy baselines will give us an understanding of whether we are moving in the right direction in modeling and feature engineering or not.

### **i. Learning Curve Analysis**

### Convergence analysis

A step-wise learning curve based on the number of iterations comes into play only when we start experimenting with the gradient-boosting algorithm.

What are the key questions we should answer when examining the loss curve:

1. Does the model converge at all?
2. Does the model beat baseline metrics (quantile loss, MAPE, etc.)?
3. Are issues like underfitting/overfitting presented or not?

Once we ensure the model converges, we can pick a sufficient number of trees on a rough grid (500-1000-2000-3000-5000) and move fixate for future experiments.

For simpler baselines, convergence analysis is not the case.

### Model complexity

We will use a model-wise learning curve to decide an optimal number of features and overall model complexity.

Let's say we fixate all hyperparameters except the number of lags we use: the more we take, the more complicated patterns and seasonalities our model can capture – and the easier it will be to overfit training data. Should it be N-1, N-2, N-3 days? or N-1, N-2, …, N-30 days? The optimal number can be determined by the “model size vs. error size“ plot.

Similarly, we can optimize window sizes. For instance, windows “7/14/21/…” are more granular than “30/60/90/…” ones. The appropriate level of granularity can be chosen, again, by using a model-wise learning curve.

In the same fashion, we tweak other key hyperparameters of the model during the initial adjustments, for instance, regularization term size.

### Dataset size

Do we need to use all the available data to train the model? How many last months is enough and relevant? Do we need to utilize all (day, store, item) data points, or can we downsample 20% / 10% / 5% of them without noticeable downgrading in metrics?

Here comes the rescue, the sample-wise learning curve analysis that determines how many samples are necessary for the error on the validation set to reach a plateau.

We should make an important design decision of whether we should use (day, store, item) as an object of the dataset or move to less granular (week, store, item). The last option reduces the number of required computations by a factor of 7, while model performance can either be left unchanged or even be increased.

This design decision affects not only the demand forecasting service speed and performance but also the overall product (a stock management system), drastically reshaping the landscape of its possible use cases. Therefore, despite the possible advantages, this decision should be agreed upon with our product managers, users (category managers), and stakeholders.

### **ii. Residual analysis**

Remember, we have an asymmetric cost function: overstock is far less harmful than out-of-stock problems. We have either expired goods or missed profit. The uncovered demand problem is a much worse scenario, and in the long run, it is expressed in customers’ dissatisfaction and an increased risk that they will pass to competitors.

### Residual Distribution

The mentioned peculiarity of the demand should guide us throughout the residual analysis of our forecasting model: positive residuals (overprediction) are more preferred than negative ones (underpredictions). However, too much overprediction is bad as well.

Therefore, we plot the distribution of the residuals along with their bias (a simple average among raw residuals). We expect this to be true in one of the following possible scenarios:

1. A small positive bias reveals slight overprediction, which is the desirable outcome. If, in addition, residuals are not widely spread (low variance), we get a perfect scenario.
2. Equally spread residuals in both negative and positive directions would be ok but is less preferred than A. We should force the model to produce more optimistic forecasts to ensure we minimize missed profit.
3. The worst scenario is when we have a skew in favor of negative residuals. It means our model tends to increase customers’ dissatisfaction. This would definitely be a red flag for the current model version deployment.
4. If we have a skew but favor positive residuals, this is unambiguously a good case for Supermegaretail as well, hence, less preferred than the first case.
5. These scenarios are applicable when we try to estimate unbiased demand (we use median prediction for that). But as mentioned, we also have a bunch of other models for other quantiles (1.5%, 25%, 75%, 95%, 99%).


For each of them, we analyze the basic assumption behind each model.

For example:

- Is it true that 95% of residuals are positive for a model that predicts a 95%-quantile?
- Is it true that 75% of residuals are negative for a model that predicts a 25%-quantile?

And so on.

### Elasticity

We should validate the elasticity assumptions using elasticity curves. There is no solid understanding of whether all the goods are expected to demonstrate elasticity, and this needs to be confirmed with stakeholders.

If we face problems related to elasticity, we have two options to improve the elasticity capturing:

1. **Post-processing (fast, simple, ad hoc solution).** We can apply an additional model (e.g., isotonic regression) for prediction post-processing to calibrate forecasts.
2. **Improve the model (slow, hard, generic solution).** It requires additional modeling, feature engineering, data preprocessing, etc. There is no predefined set of actions that will solve the problem for sure.

### Best-case vs. Worst-case vs. Corner-case

Each time we roll out a new version of the model, we automatically report its performance in best/worst/corner cases and save top-N% cases as artifacts of the training pipeline. Here is a draft of a checklist of questions for which we should find answers in this report:

- What’s the model’s prediction error when the sales history of an item is short? Are the residuals mostly positive or mostly negative?
- What about items with a high price or with a low price?
- How does prediction error depend on weekends/holidays/promotion days?
- What are the commonalities among the items with almost zero residuals? Is a long sales history necessarily required for them? How long should the sales history be in order to get acceptable performance? Does the model require other conditions that can help us to distinguish those cases where we are certain about the quality of the forecast?
- What are the commonalities among the items with the largest negative residuals? We would 100% prefer to exclude these cases or whole categories from A/B-testing groups or pilots. We should also focus on these items when we start to improve the model.
- And finally, what do the items with the largest positive residuals have in common?

### **VII. Training Pipeline**

### **i. Overview**

1. The demand forecasting model for Supermegaretail aims to predict the demand for specific items in specific stores during a particular period. To achieve this, we need a training pipeline that can preprocess data, train the model, and evaluate its performance. We assume the pipeline should be scalable, easy to maintain, and allow for experimentation with various model architectures, feature engineering techniques, and hyperparameters.

### **ii. Toolset**

The suggested tools for the pipeline are:

- Python as the primary programming language for its versatility and rich ecosystem for data processing and machine learning.
- Spark for parallel and distributed computing.
- PyTorch for deep learning models.
- MLflow for tracking experiments and managing the machine learning lifecycle.
- Docker for containerization and reproducibility.
- AWS Sagemaker or Google Cloud AI Platform for cloud-based training and deployment.

### **iii. Data Preprocessing**

The data preprocessing stage should include:

- Data cleaning: Handling missing values, removing duplicates, and correcting erroneous data points.
- Feature engineering: Creating new features from existing ones, such as aggregating sales data, extracting temporal features (day of the week, month, etc.), and incorporating external data (e.g., holidays, weather, and promotions).
- Data normalization: Scaling numeric features to a standard range.
- Train-test split: Splitting the dataset into training and validation sets, ensuring that they do not overlap in time to prevent data leakage.

### **iv. Model Training**

The model training stage should accommodate various model architectures and configurations, including:

- Baseline models: Simple forecasting methods like moving average, exponential smoothing, and ARIMA.
- Machine learning models: Decision trees, random forests, gradient boosting machines, and support vector machines.
- Deep learning models: Recurrent neural networks (RNNs), Long Short-Term Memory (LSTM) networks, and transformers. (If needed!)

We should also implement a mechanism for hyperparameter tuning, such as grid search or Bayesian optimization, to find the best model configurations.

### **v. Model Evaluation**

Model performance should be evaluated using metrics we derived prior to that, such as Quantile metrics for quantiles of 1.5, 25, 50, 75, 95, and 99 both as is and with weights equal to SKU price. Calculated as point estimates with 95% confidence intervals (using bootstrap or cross-validation) plus standard metrics such as Mean Absolute Error (MAE), Mean Squared Error (MSE), or Root Mean Squared Error (RMSE). We should also include custom metrics that are specific to Supermegaretail's business requirements, such as the cost of overstock and out-of-stock situations. (See *Validation* chapter.)

### **vi. Experiment Tracking and Model Management**

Using a tool like MLflow, we should track and manage experiments, including:

- Model parameters and hyperparameters.
- Input data and feature engineering techniques.
- Evaluation metrics and performance.
- Model artifacts, such as trained model weights and serialized models.

### **vii. Continuous Integration and Deployment**

The training pipeline should be integrated into Supermegaretail's existing CI/CD infrastructure. This includes setting up automated training and evaluation on a regular basis, ensuring that the latest data is used to update the model, and deploying the updated model to production with minimal manual intervention.

### **viii. Monitoring and Maintenance**

We should monitor the model's performance in production and set up alerts for significant deviations from expected performance. This will enable us to catch issues early and trigger retraining or model updates when necessary. (See *Monitoring* chapter.)

### **ix. Future Work and Experimentation**

The training pipeline should be flexible enough to accommodate future experimentation, such as incorporating additional data sources, trying new model architectures, and adjusting loss functions to optimize for specific business objectives.

### **VIII. Features**

Our key criteria to select the right features (outside of prediction quality):

1. **Prediction quality.** The more accurate forecasts we get, the better.
2. **Interpretability and explainability.** We prefer features that are easy to describe and explain (“black box” solutions are neither transparent nor trustworthy, especially in the initial phases of the project).
3. **Computation time** (and computational complexity). Features that take a lot of time to compute (also features from multiple data sources and with complex dependencies) are less preferred unless the improvement in prediction quality they give is worth it. That’s because they slow down the training cycle and reduce the number of hypotheses we can test.
4. **Risks** (and feature stability). Features that require external/multiple data sources, auxiliary models (or simply poorly designed features), and features based on data sources with low data quality make the pipeline more fragile, which should be avoided.

If a feature adds a statistically significant improvement to the model’s performance but violates one of the other criteria (e.g., it takes 2 days to compute), we prefer not to add this feature into a pipeline.

Primary sources of new features:

- Adding more internal and external data sources (e.g., monitoring competitors).
- Transforming and combining existing features.

Here is a list of features we will experiment with that will guide our further steps of model improvements after initial deployment:

1. Competitors' prices and how they differ from our prices (external sources).
2. Special promotion and discount calendars.
3. Prices (old price, discounted price).
4. Penetration (ratio between sales of an SKU and sales of a category).
5. SKU’s attributes (brand, categories of different levels).
6. Linear elasticity coefficient.
7. A sum/min/max/mean/std of sales of SKU for previous N days.
8. A median/quantiles of sales of SKU for previous N days.
9. Predicted weather (external sources).
10. Store’s traffic (external sources).
11. Store’s sales volume.
12. Sales for this SKU 1 year ago.
13. Economic indicators (external sources).

We formulate them as a hypothesis. An example: *Using a promo calendar will help the model capture an instant increase in demand during marketing activities, which will decrease overstock in that period.*

We will use model-agnostic (SHAP, LIME, shuffle importance) and in-built methods (linear model’s coefficients, number of splits in gradient boosting) to measure feature importance. Main goal: to understand the contribution of each feature to the model’s outcome. If a feature doesn’t contribute much, we drop it.

For automatic feature selection during the first stages (when we haven’t determined the basic feature set yet), we use RFE (rotation feature elimination).

Also, we include feature tests in the training pipeline before and after training the model:

- Test feature ranges & outlier detectors (e.g. 0.0 <= discount < X).
- Test that correlation between any pair of features less than X.
- Test that feature’s coefficient/number of splits > 0.
- Test that computation time is less than 6 hours for any feature.
- Etc.

To compute and access features easier, we can reuse a centralized feature store that collects data from different sources in DWH and, after different transformations and aggregations, merge it into one datamart (SKU, store, day). It re-calculates features on a daily basis, making it easy to experiment with new ones and track their versions, dependencies, and other meta-info.


### **IX. Measuring and reporting**

### **i. Measuring results**

As a first step to improve the prediction quality of the already deployed sales forecasting model, we plan to experiment with combining existing models (one per each category) into one. The reasoning behind that is that we will encode information about the group of items without loss in specificity but will gain more data and, thus, better generalization. Even with no improvement in quality, it is still much easier to maintain one model than many.

As offline metrics, we looked into different quantiles, percentage errors, and biases. But instead of evaluating only the overall score, in addition to that, we checked on metrics and the error analysis of specific categories. These offline tests yielded the following intermediate results:

- The general prediction quality steadily increases across all metrics, and the majority of validation folds when switching to a split model.
- The categories with a small number of products showed an increase in the offline metrics compared to a baseline (multi) model. The amount of data they had was insufficient to learn meaningful patterns. The large categories didn’t change many wins when switching to a unified model. The result is reproducible through different seasons and key geo regions.

Previously, A/B tests provided some estimation on what uplift to expect in each major category based on offline metrics.

*If we reduce metric M for product P by X%, it leads to a decrease in missed profit (out-of-stock) by Y% and cuts losses due to the overstock situation by Z%.*

According to our estimates, the total increase in revenue for the pilot group is expected to be 0.3–0.7%.

### **ii. A/B tests**

**Hypothesis.** The experiment hypothesis:

- According to the offline metrics improvement, we expect revenue to increase by at least 0.3.

**Key metrics.** The best proxy metric for revenue we can use in the A/B experiment is the average check. It perfectly correlates with the revenue (assuming the number of checks has not changed). The atomic observation for the statistical test will be a single check.

**Splitting strategy.** We split by distribution center and, through them, acquire two sets of stores, as each center serves a cluster of stores. From those sets, we pick subsets that are representative of each other and use them as groups A and B.

**Additional metrics.** Control metrics for the experiment:

- Number of checks per day: whether the sales volume has no significant drop.
- Model’s update frequency: does the model accumulate newly gathered data regularly?
- Model’s offline metrics: quantile metric, bias, WAPE.

Auxiliary metrics:

- Daily revenue.
- Daily profit.

**Statistical criteria.** We’ll use Welch’s t-test to capture the difference between samples.

**Error rates.** We set the significant level to 5% and the type II error to 10%.

**Experiment duration.** According to our calculations, two weeks will be enough to check the results. However, given the distribution center's replenishment cadence of one week, we will extend this period to one full month.

### **iii. Reporting results**

A report containing the following chapters is to be provided:

- **Results,** shown as 95% confidence intervals for primary and auxiliary metrics.
- **Graphical representation** (value of specific metric at a specific date) of all metrics from both control and treatment group for ease of consumption.
- **Absolute numbers.** E.g., the number of shops in each group, the total number of checks, and total revenue.
- **Methodology** to be supplied in the appendix. E.g., how groups were picked to be representative of each other, simulations run to check for type 1 and type 2 errors, etc.
- **Recommendation / Further steps:** what to do next based on the received results.

### **X. Integration**

### **i. Fallback Strategies**

Fallbacks are crucial for maintaining operational efficiency in the face of unforeseen circumstances. Supermegaretail has adopted a multi-tiered fallback system:

- **Primary fallback.** The primary model is trained on a subset of the most significant features. It will be used if there isn't any feature drift/problems detected within this subset.
- **Secondary fallback.** Our next layer of fallback involves time-series models like SARIMA or Prophet, which we explored in section 4.4. These models are less dependent on external features, allowing for more robust predictions if drift occurs.
- **Tertiary fallback.** As a last resort, we would predict sales akin to the previous week's data, with modifications for expected events and holidays.

The system monitors for data drifts and quality issues, triggering alarms that would automatically switch to the appropriate fallback to ensure the most accurate predictions possible.

### **ii. API design**

- **HTTP API Handler.** This component will manage requests and responses, interfacing with users in a structured JSON format.
- **Model API.** This will extract predictions directly from the model.

Request format:

GET /predictions?query=<query_string>&parameters=<parameters>&version=<version>&limit=<limit>&request_id=<request_id>&sku=<sku>&entity_id=<entity_id>&group=<group_type>

Response format:

{

"predictions": [

{

"sku": <sku_id>,

"demand": <demand>,

"entity": <entity_id>,

"period": <time_period_for_demand>,

},

...

]

}

### **iii. Release Cycle**

**Release of the wrapper vs. release of the model**

Within our integration strategy, the release of the wrapper and the release of the model represent two distinct processes. Below are the nuances for each.

**For the release of the wrapper (infrastructure):**

- **Frequency & timeline.** The release typically happens less frequently than that of the model. As demand patterns can shift over the night, it is important to be able to incorporate them into the model through training.
- **Dependencies.** Infrastructure releases are mostly dependent on software updates, third-party services, or system requirements. Any changes in such areas may necessitate a new release.
- **Testing.** Comprehensive integration testing is a must to ensure all components work harmoniously. It is also crucial to ensure backward compatibility, so existing services are not disrupted.
- **Rollout.** Usually employs standard software deployment strategies. Depending on the nature of the changes, a blue-green deployment might not always be necessary, especially if the changes are not user-facing and do not affect batch jobs.
- **Monitoring.** The focus will be on system health, uptime, response times, and any error rates.

**For the release of the model:**

- **Frequency & timeline.** Model releases are more frequent and are tied to the availability of new data, changes in data patterns, or significant improvements in modeling techniques.
- **Dependencies.** Predominantly rely on the quality and quantity of new training data. Any drifts in data patterns or introduction of new data sources can trigger the model’s update.
- **Testing.** Before rolling out, the model undergoes a rigorous offline validation. Once validated, it might be tested in a shadow mode, where its predictions run alongside the current model but are not used. This helps in comparing and validating the new model's performance in a real-world scenario without any risks.
- **Rollout.** When introducing a new model, it's not just about deploying the model file. There's also a need to ensure that any preprocessing steps, feature engineering, and other pipelines are consistent with what the model expects.
- **Monitoring.** The primary focus remains on model performance metrics. Also, keeping an eye on data drift is essential. See the Monitoring chapter for more details.

**Interplay between wrapper and model releases**

In cases where the infrastructure has updates that would affect the model (e.g., changes in data pipelines), coordination between the two releases becomes vital. Additionally, any significant changes to the model’s architecture might require updates to the wrapper to accommodate the changes.

By treating them as separate processes yet ensuring they're coordinated, we maintain the system’s stability while continuously improving its capabilities.

### **iv. Operational сoncerns**

Feedback is integral for continuous improvement. A feedback mechanism, inclusive of an override function, will be available to internal users. Not only does this aid in refining the predictions, but also gives business users a sense of control and adaptability based on real-time insights.

### **v. Non-engineering сonsiderations**

The integration strategy will also take into account non-engineering factors.

For instance:

- **Admin panels.** They will be crucial for managing the system and obtaining a high-level overview.
- **Integration with company-level dashboards.** For company-wide visibility and decision-making.
- **Additional reports.** Essential for deeper insights and analysis.
- **Overrides.** A necessary feature to account for manual adjustments based on unforeseen or unique circumstances.

Furthermore, standard CI tools used in the company, along with a typical scheduler, will be integrated to maintain consistency and optimize workflow.

### **vi. Green-Blue Deployment**

Given our audience primarily consists of internal customers and the frequent batch jobs, there's no immediate need for green-blue deployment. The absence of end-user traffic eliminates the need for such staggered deployments, simplifying our rollout strategy.

### **XI. Monitoring**

### **i. Existing Infrastructure Analysis**

Unfortunately, demand forecasting is among the pioneering machine learning projects for Supermegaretail, meaning that there is no proper ML monitoring infrastructure in place. Luckily, quick preliminary research proved to be fruitful, and we found Evidently AI - an open-source Python library (https://github.com/evidentlyai/evidently) that helps with monitoring. With the motto - “We build tools to evaluate, test and monitor machine learning models, so you don't have to” perfectly suits our goals until and if we decide to build our own platform (see Preliminary research chapter, subchapter -  Build or buy, open source-based or proprietary tech). According to the description, they cover: Model Quality, Data Drift, Target Drift and Data Quality. This means that we still have to build some foundations for this to implement.

### **ii. Logging**

We will keep model prediction logs in column-oriented DBMS. We should record data on every prediction: the features that went in and the model output + timestamps. We will use an open source such as clickhouse as it is already used for other similar needs in the company.

In addition to that, we will log basic statistics: RPS, resource utilization, error rate, p90, p99, p999 latency and error rate. A number of model calls per hour, day and week and, average, median, min and max prediction from the mode on the same aggregation level. We will use kafka + prometheus + grafana for that. We will keep the last month of data.

We will use this stack as well for real-time ML monitoring and visualisation (https://grafana.com/docs/grafana-cloud/data-configuration/metrics/metrics-prometheus/prometheus-config-examples/evidently-ai-evidently/).

### **iii. Data quality**

In addition to basic ETL and data quality checks; we will monitor for the following:

- Missing data, as a percentage from the whole dataset and separately as percentage from the most important features according to the feature importance(see the subchapter). We will use historical data (cleaning out occurrences with broken pipelines) to calculate the z-score. We will set an alarm of 3-z scores for important features and 4-z scores for the rest. In addition, we will use several Test Suite presets from the Evidently AI library. There is a preset to check for Data Quality and another for Data Stability.
- Schema compliance. Are all the features there? Do their types match? Are there new columns?
- Feature ranges and stats. To ensure that the learning model is being fed good quality data, we will manually define expected ranges for each important feature, as three z-scores + checks for invalid stats (e.g. Negative data for the amount of sales, min >= 0)
- Correlations. To detect any abnormalities in the data we will plot the correlation matrix between features and compare the difference between the two plots. Basic alarm will be set for residual higher than |0.15|

### **iv. Model quality**

We are very fortunate to have the availability of true labels with very minor delay. We receive daily sales information 15 minutes after they happen. With that in mind, we will monitor quantile metrics for quantiles of 1.5, 25, 50, 75, 95, and 99, both as is and with weights equal to the SKU price. In addition, we will monitor RMSE and MAE to track the mean and median. We will set up final thresholds after we receive the first three months of the data; we will pick initial thresholds based on the historical data and model performance on the validation.

In addition, we set up alarms for Negative values and max values. It will fire if the new max value is 50% higher than the previously seen max value.

We will set up prediction drift monitoring. We will use it as an early alarm for the next day, week and month predictions. We will test two approaches: Population Stability Index > 0.2 and Wasserstein distance > 0.1. For Wasserstein distance, we will apply growth multiplayer to the control dataset. For example, when comparing April 2021 to April 2022, knowing that overall growth is expected to be 15% we multiply everything in 2021 by 1.15. We will further adjust this based on historical data experimentation.

### **v. Data drift**

While we have true data available with a minor delay, we don't need to track input drift as a proxy for understanding the model relevance. However, it would still help us detect upcoming change before it affects the model quality. After reading this post: *Which test is the best? We compared 5 methods to detect data drift on large datasets,* https://www.evidentlyai.com/blog/data-drift-detection-large-datasets, we decided to pick Wasserstein distance to alert us in case of data drift. We start from the threshold of the mean drift score.

We can later try to apply this paper: *Feature Shift Detection: Localizing Which Features Have Shifted via Conditional Distribution Tests,* https://papers.nips.cc/paper/2020/file/e2d52448d36918c575fa79d88647ba66-Paper.pdf.

### **vi. Business metrics**

Business metrics of interest remain the same as we describe in chapter 2.1.1 Metrics to pick -  Revenue (expect to increase), level of stock(expect to decrease or maintain the same), Margin (expect to increase), which will monitor through series of A/B tests switching and swapping control groups over the course of time.

### **XII. Serving and inference**

The primary considerations for serving and inference are:

- Efficient batch throughput, as forecasts will be run daily, weekly, and monthly on large volumes of data.
- Security of the sensitive inventory and sales data.
- Cost-effective architecture that can scale batch jobs.
- Monitoring data and prediction quality.

### **i. Serving architecture**

We will serve the batch demand forecasting jobs using Docker containers orchestrated by AWS Batch on EC2 machines. AWS Batch will allow defining resource requirements, dynamically scaling the required number of containers, and queuing large workloads.

The batch jobs will be triggered on a schedule to process the input data from S3, run inferences, and output results back to S3. A simple Flask API will allow on-demand batch inference requests if required.

All data transferring and processing will occur on secured AWS infrastructure, isolated from external access. Proper credentials will be used for authentication and authorization.

### **ii. Infrastructure**

The batch servers will use auto-scaling groups to match workload demands. Spot instances can be used to reduce costs for flexible batch jobs.

No specialized hardware or optimizations are required at this stage, as batch throughput is the priority, and the batch nature allows ample parallelization. We will leverage the horizontal scalability options provided by AWS Batch and S3.

### **iii. Monitoring**

Key metrics to track for the batch jobs include:

- Job success rate, duration, and failure rate.
- Number of rows processed per job.
- Server utilization: CPU, memory, disk space.
- Prediction accuracy compared to actual demand.
- Data validation checks and alerts.

The above monitoring will help ensure the batch process remains efficient and scalable and produces high-quality predictions. We can assess optimization needs in the future based on production data.
