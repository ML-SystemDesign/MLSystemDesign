**Table of contents**

**[I. Problem Definition	2](\#i.-problem-definition)**

[i.  Origin	2](\#i.-origin)

[**II. Relevance & reasons	2**](\#ii.-relevance-&-reasons)

[i. Existing flow analysis	2](\#i.-existing-flow-analysis)

[ii. Possible problems with item cards on platform	2](\#ii.-possible-problems-with-item-cards-on-platform)

[iii. Previous work product matching has been addressed in various forms:	3](\#iii.-previous-work-product-matching-has-been-addressed-in-various-forms:)

[iv. Other issues & risks	3](\#iv.-other-issues-&-risks)

[**III. Metrics and losses	3**](\#iii.-metrics-and-losses)

[i. Offline metrics	3](\#i.-offline-metrics)

[ii. Online metrics	4](\#ii.-online-metrics)

[iii. Loss functions:	4](\#iii.-loss-functions:)

[1\. Binary cross-entropy loss. Given the binary nature of the matching task (match/no-match), this loss is appropriate.	4](\#binary-cross-entropy-loss.-given-the-binary-nature-of-the-matching-task-(match/no-match),-this-loss-is-appropriate.)

[2\. Triplet loss. Useful in embedding-based models, where the goal is to bring the representations of matching products closer and push non-matching products farther apart.	4](\#triplet-loss.-useful-in-embedding-based-models,-where-the-goal-is-to-bring-the-representations-of-matching-products-closer-and-push-non-matching-products-farther-apart.)

[**IV. Dataset	5**](\#iv.-dataset)

[i. Data sources	5](\#i.-data-sources)

[ii. Data labeling	5](\#ii.-data-labeling)

[i. Labeling strategy	5](\#i.-labeling-strategy)

[ii. Data quality and consistency	6](\#ii.-data-quality-and-consistency)

[iii. Labeling tools and automation	6](\#iii.-labeling-tools-and-automation)

[iv. Labeling Process Management	7](\#iv.-labeling-process-management)

[iii. Final ETL pipeline	7](\#iii.-final-etl-pipeline)

[i. Data ingestion	8](\#i.-data-ingestion)

[ii. Data preprocessing	8](\#ii.-data-preprocessing)

[iii. Data validation and quality assurance	9](\#iii.-data-validation-and-quality-assurance)

[iv. Feature engineering and transformation	9](\#iv.-feature-engineering-and-transformation)

[v. Data storage and management	10](\#v.-data-storage-and-management)

[vi. Loading and deployment	10](\#vi.-loading-and-deployment)

[**V. Validation	11**](\#v.-validation)

[**VI. Baseline solution	11**](\#vi.-baseline-solution)

[i. Extraction with regular expressions.	11](\#i.-extraction-with-regular-expressions.)

[ii. Extraction with deep learning models	11](\#ii.-extraction-with-deep-learning-models)

[iii Siamese model for picture comparison.	12](\#iii-siamese-model-for-picture-comparison.)

## 

# **I. Problem Definition** {#i.-problem-definition}

## **i.  Origin**  {#i.-origin}

MegaMarketplace is an e-commerce platform that aggregates products from various vendors, providing a vast selection for customers. With thousands of items listed, product matching becomes a significant challenge. Different vendors might list their products with mistakes which can cause mistakes in the recommendation system and sales of these products. 

Accurate product matching is crucial for a seamless shopping experience, efficient inventory management, and minimizing customer confusion. The objective is to develop a ML system that can determine whether two products from different vendors are the same and have the correct description and photo that matched together.

# **II. Relevance & reasons**  {#ii.-relevance-&-reasons}

## **i. Existing flow analysis** {#i.-existing-flow-analysis}

Right now life cycle of every item in the market is the following:

1. Vendor creates a card for the good with tags, photos, description  
2. MegaMarketplace workers check manually each new product for compliance with the rules of the platform and for compliance of the product with the description  
3. Item starts popping up in the recommendations and in similar goods  
4. If there were  complaints about product, there is a need to check such item again and ban it from the platform or ask for changing some details in the item card

## **ii. Possible problems with item cards on platform** {#ii.-possible-problems-with-item-cards-on-platform}

1. **Manual inspection of the new items.** This costs the company some amount of money for the workers that check not only the rule enforcement, but also the matching descriptions, tags, pictures to each other. Also, people are not ideal checkers so some of the products can accidently bypass such a stage.  
2. **Bad product recommendations.** Due to the first reason for the recommendation system it can be difficult to find the real same products or such that users need. This leads to the situation when the user did not manage to find what he needed and just left the app. Thus, we can lose not only money, but a user base.  
3. **Second check of the products.** Again, people do some work because previously it was done not the way it must.

## **iii. Previous work product matching has been addressed in various forms:**  {#iii.-previous-work-product-matching-has-been-addressed-in-various-forms:}

1. **Rule-based systems.** Simple rules to match products based on exact title or attribute matching. However, this approach is limited and fails to capture slight variations in data.

2. **Traditional ML models.** Logistic regression and decision trees have been used with engineered features like Jaccard similarity for text and color histograms for images. These methods have seen moderate success but struggle with complex scenarios involving multiple modalities (text, attributes, and images).

3. **Deep learning approaches.** Recent work leverages deep learning for text embeddings and image feature extraction, combining them into a unified model. This approach has shown promising results but requires careful handling of large-scale data, balancing multiple input types and proper data preprocessing that can work good even with bad or inaccurate descriptions/photos/tags

## **iv. Other issues & risks** {#iv.-other-issues-&-risks}

1. **Incompatibility with the already existing recommendation system.** The structure of the current model may need to be changed to achieve best performance. Possible is was trained on data that contains bad data as example of such items, but with the new matching model we can think only about good data, thus increasing the overall performance

2. **False positives/negatives.** Incorrectly matched or unmatched products can negatively impact customer trust and operational efficiency. 

3. **Data.** Thus could contain problems with permission of using already existing cards for model training and problems connected with creating a well data set with a whole range of products with proper labels.

4. **Сhanging behavior over time.** There can be scammers that will change the way of filling cards and may pass the system in the future. Also, behavior of all vendors can change in the future as the result of some trends. Thus, the model should be retrained (or fine-tuned).

# **III. Metrics and losses** {#iii.-metrics-and-losses}

## **i. Offline metrics** {#i.-offline-metrics}

1. **Precision and Recall**. Important for balancing false positives and false negatives, where precision shows the percentage of identified matches that are correct and recall \- the percentage of actual matches that are correctly identified. Here it is important to find a balance between them. Increasing precision leads to more good matches and thereafter more bad matches. Targeting it decreases the price for manual labor but can also increase the number of bad cards in the store which leads to the second review. Recall, otherwise, tries to maximize the number of finding wrong and bad matches. This helps with a better user experience but increase the cost of manual reviews

2. **F1 Score.** The harmonic mean of precision and recall, used for overall evaluation.

3. **ROC-AUC.** Evaluates the model's ability to distinguish between matches and non-matches across different threshold settings

4. **Specificity.** Shows the ability of a model to predict true negative cases (bad matches). Most likely the model will be tested or even trained on different samples where class balance may not be the same. This influences all metrics except Recall and Specificity. It is an important idea to consider.

## **ii. Online metrics** {#ii.-online-metrics}

Model must be tested in the real life 

1. **Conversion to purchase**. Key metric we try to achieve. It can be evaluated from the opened card which indicates: how well description, photos met expectations. 

2. **Average clicks on item cards**. Increase means better performance of recommendation system (important not to test a new version of it simultaneously)

3. **Average duration before clicking on an item from the main page**. This metric can show how faster users find the needed product because of better cards quality

## **iii. Loss functions:** {#iii.-loss-functions:}

1. ## **Binary cross-entropy loss.** Given the binary nature of the matching task (match/no-match), this loss is appropriate. {#binary-cross-entropy-loss.-given-the-binary-nature-of-the-matching-task-(match/no-match),-this-loss-is-appropriate.}

2. ## **Triplet loss.** Useful in embedding-based models, where the goal is to bring the representations of matching products closer and push non-matching products farther apart. {#triplet-loss.-useful-in-embedding-based-models,-where-the-goal-is-to-bring-the-representations-of-matching-products-closer-and-push-non-matching-products-farther-apart.}

3. **Contrastive loss**. Helps for working with several embeddings representing goods. Takes average of distances between vectors in square if pairs are in match or average deviation from the maximum squared difference  
4. **Focal loss** – dynamically scalable cross-entropy loss. Reduces the importance of simple examples during training and can quickly focus the model on difficult examples

# **IV. Dataset** {#iv.-dataset}

## **i. Data sources** {#i.-data-sources}

Only internal sources due to specific of our task primary for one online market

1. **Product catalog**. Titles, descriptions, and attributes for all products listed by vendors.

2. **Vendor images**. Images provided by vendors for each product.

## **ii. Data labeling** {#ii.-data-labeling}

Data labeling is a crucial step in the development of a machine learning (ML) system, particularly in the context of product matching for an e-commerce platform like MegaMarketplace. The quality and accuracy of labeled data directly impact the performance of the ML models, as they rely on these labels to learn patterns and make predictions.

### **i. Labeling strategy** {#i.-labeling-strategy}

1. **Bad pairs identification:**  
   * **Historical data utilization.** The company should leverage its existing database of rejected or flagged product cards. These cards can serve as negative examples (bad pairs) in the training dataset.  
   * **Synthetic data generation.** To augment the dataset, synthetically generated bad pairs can be created by introducing slight modifications to valid cards, such as altering tags, descriptions, or images. Regular expressions and automated text/image manipulation techniques can help generate these variations.  
   * **Diversity coverage.** It's essential to cover all product categories, especially those with fewer samples, to ensure the model generalizes well across different types of products.  
2. **Good Pairs Identification:**  
   * **High-quality products.** Products with consistently high ratings (4-5 stars) should be manually paired as good examples. This ensures the dataset includes instances where the descriptions, tags, and images are correctly aligned and represent high-quality matches.  
   * **Manual verification.** Despite being labor-intensive, manual pairing of good products is crucial to maintain the integrity of the dataset. This process should be performed by trained annotators who understand the nuances of product matching.  
3. **Key characteristics annotation:**  
   * **Feature extraction.** For each product, key characteristics such as brand, size, color, and material should be extracted from the descriptions and annotated. This can be done manually or with the assistance of large language models (LLMs) that can automate part of the process.  
   * **Labeling for ML models.** These key characteristics should be used to create a structured dataset where each characteristic is labeled, aiding the model in learning the importance of different features in product matching.

### **ii. Data quality and consistency** {#ii.-data-quality-and-consistency}

1. **Data cleaning:**  
   * **Image consistency**. Ensure that all images are of consistent quality, size, and angle to avoid discrepancies that could confuse the model.  
   * **Deduplication.** Remove duplicate entries from the dataset to prevent the model from learning redundant or biased information.  
   * **Standardization.** Text descriptions should be standardized by removing stop words, correcting spelling errors, and normalizing terms to ensure uniformity across the dataset.  
2. **Handling data imbalance:**  
   * **Class balancing.** If there is an imbalance between good and bad pairs, techniques such as oversampling, undersampling, or generating synthetic data should be employed to balance the dataset. This is crucial for preventing the model from becoming biased towards the more frequent class.

### **iii. Labeling tools and automation** {#iii.-labeling-tools-and-automation}

1. **Annotation tools:**  
   * **Human-in-the-loop systems.** Employ annotation tools that allow human experts to verify and correct automatically generated labels. This ensures the dataset's quality and helps refine automated processes. Yandex Toloka can be used.  
   * **Automated labeling.** Leverage tools that can automatically label data based on predefined rules or models. These should be used for straightforward tasks, with complex cases being escalated to human annotators.  
2. **Continuous labeling:**  
   * **Active learning.** Implement active learning strategies where the model identifies uncertain cases and queries human annotators for labels. This approach optimizes the labeling process by focusing human effort on the most challenging examples.  
   * **Feedback loop.** Continuously update the dataset with new labeled data as the model is deployed and starts making predictions. This feedback loop helps in refining the model over time, especially in handling evolving product descriptions and tags.

   ### **iv. Labeling Process Management** {#iv.-labeling-process-management}

1. **Quality assurance:**  
   * **Inter-annotator agreement.** monitor the consistency between different annotators using metrics such as Cohen's kappa to ensure labeling quality.  
   * **Regular audits.** periodically review a random sample of labeled data to assess and maintain quality standards.  
2. **Documentation:**  
   * **Labeling guidelines.** Maintain comprehensive documentation outlining the rules and guidelines for labeling. This ensures that all annotators follow a consistent approach, reducing variability in the labeled data.  
   * **Version control.** Use version control for the labeled dataset to track changes over time and ensure that the model can be retrained on the latest, most accurate data.

## **iii. Final ETL pipeline** {#iii.-final-etl-pipeline}

The final ETL (Extract, Transform, Load) pipeline is a critical component of the ML system, ensuring that data is efficiently collected, processed, and prepared for model training and inference. This pipeline must be designed to handle the specific needs of MegaMarketplace, particularly in managing diverse data sources, maintaining data quality, and supporting the iterative development and deployment of the machine learning model.

### **i. Data ingestion** {#i.-data-ingestion}

**Daily data collection**:

* **Product catalog.** Continuously ingest product data, including titles, descriptions, and attributes, from the internal database. This ensures that the most up-to-date information is always available for model training and inference.  
* **Vendor images.** Collect images associated with each product daily. Images are crucial for visual matching tasks and must be kept current to reflect any updates or changes made by vendors.  
* **Feedback and reviews.** Integrate data from customer reviews and feedback to enrich the dataset. High-rated products can be flagged for good pairs, while products with negative feedback can contribute to the identification of bad pairs.

### **ii. Data preprocessing** {#ii.-data-preprocessing}

**Text preprocessing**:

* **Cleaning and normalization.** Implement text cleaning procedures to remove noise from the data. This includes removing HTML tags, special characters, and stop words, as well as normalizing text by converting it to lowercase and lemmatizing words.  
* **Feature extraction.** Extract key characteristics from product descriptions using techniques such as named entity recognition (NER) or regular expressions. These characteristics are essential for matching products based on their attributes.

**Image preprocessing**:

* **Image standardization.** Resize and standardize all images to a uniform format (e.g., consistent resolution and aspect ratio). This helps the model focus on content rather than variations in image dimensions.  
* **Data augmentation.** Apply image augmentation techniques such as rotation, flipping, and color adjustments. This increases the robustness of the model by exposing it to a wider variety of image conditions.

**Data augmentation**:

* **Synthetic data creation,** Generate synthetic data by altering existing records to simulate bad pairs. This could involve modifying product descriptions, swapping tags, or making slight changes to images, thereby enriching the dataset and improving the model's ability to detect mismatches.

### **iii. Data validation and quality assurance** {#iii.-data-validation-and-quality-assurance}

**Validation checks**:

* **Schema validation.** Ensure that incoming data adheres to a predefined schema, checking for required fields, correct data types, and valid value ranges. This prevents corrupted or incomplete data from entering the pipeline.  
* **Duplicate detection**: Implement checks to detect and remove duplicate records from the dataset. Duplicates can skew model training and lead to biased predictions.

**Quality assurance**:

* **Anomaly detection.** Use anomaly detection techniques to identify outliers or suspicious data points that could indicate errors in the data collection process.  
* **Inter-annotator agreement.** For labeled data, periodically measure inter-annotator agreement to ensure consistent labeling quality across different data sources and annotators.

### **iv. Feature engineering and transformation** {#iv.-feature-engineering-and-transformation}

**Feature engineering**:

* **Derived features.** Create new features from existing data, such as calculating the similarity between product titles or extracting dominant colors from images. These features can provide additional context for the ML model.  
* **Encoding.** Convert categorical variables into a numerical format suitable for model consumption, such as one-hot encoding or embedding representations.

**Data transformation**:

* **Normalization and scaling.** Apply normalization or scaling techniques to numerical features to ensure they are on a consistent scale, which can improve model performance and convergence during training.  
* **Dimensionality reduction.** Use techniques like Principal Component Analysis (PCA) to reduce the dimensionality of the dataset, especially for image data, helping to focus on the most important features while reducing computational complexity.

### **v. Data storage and management** {#v.-data-storage-and-management}

**Versioned data storage**:

* **Dataset versioning.** Implement version control for datasets to track changes over time. This is crucial for reproducibility, allowing the model to be retrained or validated on specific versions of the data.  
* **Data partitioning.** Organize the data into training, validation, and test sets in a way that ensures representative samples from all product categories. This helps in maintaining a balanced and comprehensive dataset for model development.

**Data warehouse integration**:

* **Centralized storage.** Store all processed and transformed data in a centralized data warehouse that supports efficient querying and retrieval. This storage system should be optimized for both batch processing and real-time access.

### **vi. Loading and deployment** {#vi.-loading-and-deployment}

**Model training and retraining**:

* **Automated training pipelines.** Set up automated pipelines to load processed data into the ML model training environment. This pipeline should support regular retraining to incorporate new data and adapt to changes in product listings or vendor behavior.  
* **Real-time inference.** Deploy models in a real-time inference environment, where the latest data is continuously fed into the model to make product matching decisions on the fly.

**Monitoring and feedback loop**:

* **Continuous monitoring**. Implement monitoring tools to track the performance of the model in production, including metrics such as precision, recall, and latency. This allows for prompt detection of issues such as model drift or data inconsistencies.  
* **Feedback integration**. Create a feedback loop where the outcomes of model predictions (e.g., successful matches, user corrections) are fed back into the ETL pipeline. This loop helps in continuously improving the model’s accuracy and robustness.

# **V. Validation** {#v.-validation}

**i. Requirements**

What assumptions should we pay attention to when developing an assessment process?

New data is coming in every second. Along with the new data, new products appear. The assortment matrix changes by N% every month.

**ii. Reliable Evaluation**

To achieve a fair assessment of the quality of the model, we need to use a sample unknown to the model in order to avoid "peeping" into the validation part of our dataset. This can be achieved in at least two ways: take the already marked-up data from the product, or collect and send the dataset to people for independent markup from Toloka.

**iii. Standard schemes**

In case we don't have or don't have enough validation data, we can use K-Fold (K=5). 

**iv. Non-trivial schemes**

If there is enough validation data, we can use bootstrap to obtain the statistics of our model, and then calculate the confidence interval for the quality of the model's predictions.

# **VI. Baseline solution**  {#vi.-baseline-solution}

Idea consists of retrieving key characteristics from description for grouping them with field “characteristics”. If some was not matched, then the card is going on review where these mismatches are seen. If there are no mismatches we can compare with other cards on the platform.

For such cards about 5-20 closest cards are generated by an already existing recommendation and matching system. Then the cards are compared by images of goods and by characteristics. Cards can be declined if the image does not match others or if there is no intersection between characteristics of a new good and some already existing good. If the card does not look like some other card it goes to review, if yes, pass without it. Next, methods of matching are described.

## **i. Extraction with regular expressions.** {#i.-extraction-with-regular-expressions.}

We could just have lists of all characteristics of all products. We need to find them in text and take the next or previous words indicating such characteristics. It is a light decision as this can be done without hard models and speed will be very good. But for that we need to know all characteristics of all products and update them with time. If there is such a base, we could just generate synonyms and search them in text. Fast and easy solution, but it can not cover all cases and there will be bad data in some cases.

## **ii. Extraction with deep learning models** {#ii.-extraction-with-deep-learning-models}

For that there is a need for creating a data set where for each word will be a label indicating to which characteristic this word refers to. This can be done fully manually or with a combination with regular expression that could cover some cases. The return value of such a model can be a pseudo dict structure with key and value. Later keys should be standardized to cope with synonyms.

## **iii Siamese model for picture comparison.**  {#iii-siamese-model-for-picture-comparison.}

For every picture of the new card we need to compare with all images of the other cards (this can be done in O(n) if we have already stored vectors for every image. Or contrastive models can be used to evaluate differences between new images and already passed images. Here can be used VIT and CNN models

The model training stage should accommodate various model architectures and configurations, including: 

Baseline models: simple compare characteristic files with two cards or adding key elements from description with regular expressions 

Deep learning models: Recurrent neural networks (RNNs), Long Short-Term Memory (LSTM) networks, and transformers for taking extra characteristics from description (check if this even necessary)

We should also implement a mechanism for hyperparameter tuning, such as grid search or Bayesian optimization, to find the best model configurations.