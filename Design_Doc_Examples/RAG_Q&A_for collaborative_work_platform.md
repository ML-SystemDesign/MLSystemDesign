# MagicSharepoint

### **I. Problem definition**

### **i. Origin**

MagicSharepoint is a platform designed for collaborative work and document sharing among clients. Documents can be in text format (Markdown) or scanned/image formats.

- Expected Document Size: Up to 500 pages.
- Structure: Documents larger than 10 pages typically include a table of contents and dedicated sections, such as introduction or glossary.
- Content: Documents may include text with all Markdown features (e.g., quotes, headings, formulas, tables).

Clients can edit documents online via the platform or upload documents from their local machines. Each document receives a version number upon:

- Saving
- Uploading a new document
- Uploading a version of the existing document

Clients can access all versions of each document.

The project's goal is to provide clients with a tool to get answers about document content and version changes more easily and quickly than by proofreading and comparing documents on their own.

### **ii. Relevance & Reasons**

**ii.i. Existing flow**

To get answers about the content of a document, clients need to read through the document or use search functionality. 

Since documents are domain-specific, clients must have relevant expertise depending on the nature of the question. Answers must be cataloged manually in an external tool. 

Additionally, if another client has the same or a similar question, they have no way of knowing that the question was already asked and answered.

**ii.ii. Other reasons**

The proposed tool could be reused to support frequent questions or bulk inquiries.

It has the potential to build a Knowledge Center for clients, allowing documents to be represented as a graph of facts or knowledge.

### **iii. Expectations**

Client expect answers to be:
- Fast
    - First token within 1 minute.
- Trustfull
    - No hallucinations or 'extended' answers.
    - Do not proofread the entire document if there are doubts.
- Interactive
    - Ability to provide more details/follow-up questions if the answer is insufficient.
    - Automatically be requested for more details if could not generate insufficient answer.
- Direct
    - Indicate when an answer cannot be provided due to lack of context.
    - Avoid hallucinating complete answers.

Client need to get answers about:
- A single document in its latest version.
- Multiple documents in their latest versions.
- A single document and its various versions.
- Multiple documents and their various versions.

Client wants to select documents:
- Explicit, using filters.
- Implicit, through dialogue.

Use case examples:
- Specific Question about Document Metadata
    - e.g., author and date.
- Specific Question about Document Content Available in the Document
    - e.g., how the Attention mechanism works from 'Attention is All You Need'.
- Specific Question about Document Content Not Available in the Document
    - e.g., how the Attention mechanism works from 'Bible'.
- Abstract/Not Relevant Question about Document Content
    - e.g., how are you doing.
- Specific Question about Document Version Changes
    - e.g., how section names changed between v2 and v12 from 'Machine Learning System Design'.
- Specific Question about Multiple Document Version Changes
    - e.g., differences between the first available draft and the published version for all books in the 'Harry Potter' series.

### **iiii. Previous work**

- Implemented 'smart' full-text search to help navigate faster and easier.
- Cataloged frequent questions and used Mechanical Turk to get answers in advance.

### **iv. Usage volumes and patterns**

Every month:
- Platform has ~1000 unique users.
- Each user is having 10 document reading sessions.
- Reading session lasts between 30 and 120 minutes.
- Version is assigned to 500 documents.
    - New documents or edited existing documents.
- 10% of the documents are image based.

### **v. Other issues & Risks**

- Cloud Object Storage with automated version cataloging.
- OCR is not implemented.
- Documents could be sent to service vendors, provided they are not used for training as per SLA (e.g., OpenAI, Anthropic, etc.).


### **II. Metrics and losses**

Original idea to use RAG approach over vendor LLMs.
Metrics targeting Document Extraction and Answer Generation.
Optional idea to evaluate how good system is at not-clear questions with requesting for additional information.

- **Key Takeaways:**
    1. The choice of loss functions and metrics is pivotal in guiding a machine learning system towards achieving its intended objectives. These choices directly influence the model's learning focus and evaluation criteria.
    2. While every loss function can serve as a metric, not all metrics are suitable as loss functions due to requirements for continuity and differentiability.
    3. Consistency metrics play a crucial role in practical applications, ensuring that models remain stable and reliable across varying inputs and retraining cycles.
    4. The development of a machine learning system should include a thoughtful selection of both offline and online metrics, with a clear understanding of how these metrics relate to the system's ultimate goals. Proxy metrics and a well-defined hierarchy of metrics can facilitate this alignment, enabling more effective system evaluation and optimization.

### **III. Dataset**

Documents available on the Platform
Some documents are in Image form
Some document are in Text form
Each document have origination metadata
Documents may have v1-v2-v3-... versions
For some Documents we know the diff between vX and vY
For some Documents we do not know the diff. Only know how Document looked like at each version

- **Key Takeaways:**
    1. The success of an ML system is heavily dependent on the quality and relevance of its underlying datasets. Identifying and utilizing the right data sources is crucial for system effectiveness.
    2. Data preparation is a critical step in ML system development, requiring a thoughtful balance between automation and manual oversight to ensure data quality and relevance.
    3. Metadata plays a vital role in maintaining data consistency and supporting effective data management throughout the lifecycle of an ML system.
    4. Addressing the cold start problem requires creative approaches to data acquisition and utilization, emphasizing the need for flexibility and innovation in early system development stages.
    5. Establishing a healthy data pipeline is foundational to the long-term success and scalability of ML systems, underscoring the importance of reproducibility, consistency, and availability in data management practices.

### **IV. Validation Schema**

No ideas

- **Key Takeaways:**
    1. The selection of a validation schema is crucial for accurately measuring a model's performance on unseen data, requiring careful consideration of the specific characteristics of the dataset and the problem at hand.
    2. Standard validation schemas provide a solid foundation for most machine learning applications, but non-trivial schemas may be necessary to address unique challenges or specific data characteristics.
    3. Updating validation splits in response to new data or changing distributions is essential for maintaining the relevance and accuracy of performance estimates.
    4. Detailed planning and documentation of the chosen validation schemas within the design document are vital for ensuring the evaluation process is aligned with the project's goals and constraints.


### V. Baseline Solution

#### Document Extraction Process

Considering the minimal variability among the documents and the sufficient coverage of most cases within the current dataset, it is recommended to implement an in-house document extraction pipeline. This pipeline should consist of:

1. File Type Handler: Differentiate and handle file types accordingly, as PDFs and images may require additional processing steps.
2. Text Extraction: Deploy a customized OCR solution designed to handle non-text elements.
3. Text Preprocessing: Remove unwanted characters, whitespace, or any artifacts.
4. Markdown Formatting: Ensure that the extracted content is formatted correctly according to markdown standards.
5. Error Management & Spell Checking: Integrate an error handler and a spell checker to maintain data quality.

#### Retrieval-Augmented Generation Framework

The Retrieval-Augmented Generation (RAG) framework can be broken down into two main components:

- Retrieval
- Augmented Generation

Augmented Generation is a recent advancement, while the concept of document retrieval is something that has been with us since the emergine of web search. While there is little to no sense in building the second part using solutions other than LLMs, it might make sense to implement a simple baseline for the retrieval.

#### Retrieval: Sparse Encoded Retrieval Baseline

Objectives:
- Create a robust baseline with minimal effort.
- Validate the hypothesis that an enhanced search capability is beneficial.
- Gather a dataset based on retrieval, incorporating both implicit and explicit feedback for future refinement.

Applicability:
This covers use case `1a`. The solution is not applicable to the use cases `1na` and `2na`, thus also addressed.

The system enables content search within documents using the BM25 algorithm.

Components:
1. Preprocessing Layer
    - Tokenizes input data
    - Filters out irrelevant content
    - Applies stemming / lemmatization
2. Indexing Layer
    - Maintains a DB-represented corpus
    - Creates indexes for Term Frequency (TF) and Inverse Document Frequency (IDF)
3. Inference Layer
    - Executes parallelized scoring computations
    - Manages ranking and retrieval of results
4. Representation Layer
    - Highlights the top-k results for the user
    - Handles an explicit user feedback dialogue ("Have you found what you were looking for?")

##### Pros & Cons

Pros:
+ Simple to implement, debug, and analyze
+ Fast retrieval due to lightweight computation
+ Scalable, as computation jobs can process document segments independently
+ Popular, with many optimized implementations available
+ Low maintenance costs, suitable for junior engineers

Cons:
- No semantic understanding: snonyms are not supported by default
- Bag-of-words approach: word order is not considered
- Requires updates to accommodate new vocabulary

#### RAG: Baseline Implementation

A basic RAG system consists of the following components components:

1. Ingestion Layer:
    - Embedder
    - DB-indexing
2. Retrieval Layer:
    - Embedder
    - DB simularity search
3. Chat Service:
    - Manages chat context
    - Prompt template constructor
4. Synthesis Component:
    - Utilizes an LLM for response generation
6. Representation Layer:
    - Provides a dialogue mode for user interaction.
    - User Feedback: Collects user input to continuously refine the system.

We have opted to develop an in-house embedder while utilizing API calls to vendor-based LLMs.

In-house embedder:
- Provides potential for improving this critical component without vendor lock-in
- Offers deterministic behavior
- Does not require us to provide per-token costs
- Could potentially benefit from interaction data enhancements

Drawbacks:
- Development and maintenance costs.
- Per-token costs may not be as optimized as those of larger companies.


API-based LLMs:

- LLMs are continually improving, particularly in few-shot learning capabilities. We don't want to invest in LLM traning.
- Competitive market dynamics are driving down the cost of API calls over time
- Switching vendors involves minimal effort since it only requires switching APIs, allowing for potential utilization of multiple vendors.

Drawbacks:
- Less control over the responses
- Data privacy (though not a significant concern)

### **VI. Error analysis**

Ask expert to test the process
Whenever expert do not agree - ask him to provide reason
explore the reason and improve the system

- **Key Takeaways:**
    1. Error analysis is an indispensable step in refining machine learning systems, providing a deeper understanding of model errors and guiding targeted improvements.
    2. Learning curve analysis offers early insights into model adequacy, highlighting issues of convergence, overfitting, and underfitting that need addressing.
    3. Residual analysis serves as a powerful tool for verifying model assumptions and identifying biases, enabling the detection of specific error patterns and guiding the development of more robust models.
    4. Identifying commonalities in residuals through various analytical approaches, including adversarial validation and group analysis, helps pinpoint specific areas for improvement, ensuring the model performs well across diverse scenarios and datasets.

### **VII. Training Pipeline**

For RAG the most important - to have Embeddings for documents as soon as documents are available on the platform and saved to a specific version

- **Key Takeaways:**
    1. Training pipelines are foundational to the success of ML projects, ensuring that models are not only trained but also prepared, evaluated, and deployed in a consistent and reproducible manner.
    2. The choice of tools and platforms for training pipelines should be guided by the project's scale, infrastructure, and specific needs, with an emphasis on flexibility and future growth.
    3. Scalability and configurability are critical attributes of effective training pipelines, enabling them to handle large datasets and adapt to changing requirements without excessive complexity.
    4. Comprehensive testing, including both smoke tests and unit tests, is essential for maintaining the integrity of training pipelines and ensuring the reliability of deployed models.

### **VIII. Features**

Sentence-paragraph-document level Embeddings
Document structure

- **Key Takeaways:**
    1. Effective feature engineering is crucial for enhancing the performance and interpretability of machine learning models. It involves not only the generation of new features but also the careful selection and analysis of these features to ensure they contribute positively to the model's predictions.
    2. The process of feature engineering requires a balance between creativity, domain knowledge, and technical skills. It's an iterative process that often involves collaboration across teams to identify and implement the most impactful features.
    3. Feature importance analysis is essential for understanding the contribution of each feature to the model's predictions. This analysis aids in model transparency and can guide further feature engineering efforts.
    4. Implementing a feature store can offer significant benefits in terms of feature management, reusability, and collaboration across teams. However, it requires careful consideration of the specific needs and infrastructure of the organization.
    5. Ultimately, the goal of feature engineering is to create a set of features that are not only predictive but also interpretable, manageable, and aligned with the business objectives of the machine learning system.

### **IX. Measuring and reporting**

Time to get response
Time to first token
% of new Q&A sessions
If users keep using it after some time
% of positive/negative feedbacks

- **Key Takeaways:**
    1. Effective measurement and reporting are foundational to translating machine learning advancements into business value. They ensure that technical improvements are accurately assessed and communicated in terms of their impact on key business metrics.
    2. A/B testing serves as a critical tool for validating the real-world effectiveness of machine learning systems. Properly designed and executed A/B tests provide a reliable basis for making data-driven decisions about deploying new models or system changes.
    3. Reporting results goes beyond stating statistical significance; it involves a comprehensive analysis of how changes affect various metrics, the potential for scaling these changes, and their implications for future strategies.
    4. The process of measuring and reporting results is iterative and should inform ongoing development and refinement of machine learning systems. It requires close collaboration between technical teams and business stakeholders to ensure that the insights gained drive actionable improvements.

### **X. Integration**

Embeddings db
Chat UI
Platform integration
Parallel processing
SLAs

- **Key Takeaways:**
    1. Integration is a continuous and essential process that ensures the success and longevity of ML systems. It requires careful planning, from API design to deployment and operation.
    2. API design should prioritize simplicity and predictability, with a focus on creating interfaces that hide complexity while allowing for necessary customization and ensuring deterministic behavior.
    3. The release cycle of ML systems presents unique challenges, necessitating a balance between agility and stability. Techniques like blue-green and canary deployments can facilitate safer updates and minimize disruptions.
    4. Operational robustness is achieved not only through technical means such as CI, logging, and monitoring but also by addressing non-technical aspects like compliance and user data management. Overrides and fallbacks are critical for maintaining service continuity and adapting to changes or failures in real-time.

### **XI. Monitoring**

Time to get answer
Time to first token
???

1. **Monitoring is Essential**: Without proper monitoring, even the most sophisticated ML models can fail, highlighting the need for robust monitoring frameworks that include software health, data quality, and model performance.
2. **Proactive Maintenance**: Proactive strategies in monitoring can mitigate risks associated with data drift and model decay, ensuring that ML systems continue to perform optimally over time.
3. **Integrated Approach**: Effective monitoring combines traditional software monitoring techniques with new approaches tailored to the nuances of ML systems, integrating data quality checks, performance benchmarks, and business KPIs to create a holistic view of system health.
4. **Continuous Improvement**: The field of ML monitoring is evolving, necessitating ongoing adjustments to monitoring practices as new challenges and technological advancements arise.

### **XII. Serving and inference**

Cloud Embedding DB
Vendor API for LLM

1. **Critical Balance of Factors**: Effective inference optimization requires a careful balance between competing factors such as latency, throughput, and cost. Understanding and prioritizing these based on specific application needs is key to successful deployment.
2. **Choice of Tools and Frameworks**: Selecting the right tools and frameworks is crucial and should be guided by the specific requirements of the deployment environment and the nature of the machine learning tasks.
3. **Continuous Monitoring and Optimization**: Continuous performance monitoring and iterative optimization are essential to maintain and improve the inference capabilities of machine learning systems in production.
4. **Strategic Planning for Scalability**: Planning for scalability from the outset can mitigate future challenges and help manage costs effectively as system demand grows.
