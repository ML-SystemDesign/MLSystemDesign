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

### **iv. Other issues & Risks**

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

For validation purposes, we will use a data set generated from the original documents using the RAGAS  functionality. This approach allows us to create a comprehensive validation set that closely mirrors the real-world usage of our system.

#### i. Question Selection and Dataset Creation
RAGAS takes the original documents and their associated metadata and generates a structured dataset with the following components

* Question: Simulation of user queries
* Context: Relevant parts of the document(s)
* Answer: The expected answer

This structure allows us to evaluate both the retrieval and generation aspects of our RAG system.

To create a comprehensive and representative validation dataset, we'll employ a multi-faceted approach to question selection:

1. Automated Question Generation
    * Use natural language processing (NLP) techniques to automatically generate questions from the documents.
    * Apply techniques such as named entity recognition, key phrase extraction and syntactic parsing to identify potential question targets.
    * Use question generation models (e.g. T5 or BART fine-tuned for question generation) to create different types of questions.

2. Human-in-the-Loop Curation
    * Engage subject matter experts to review and refine auto-generated questions.
    * Have experts create additional questions, especially for complex scenarios or edge cases that automated systems might miss.
    * Ensure questions cover various difficulty levels and reasoning types.

3.  Real User Query Mining
    * Analyse logs of actual user queries (if available) to identify common question patterns and topics.
    * Include anonymised versions of real user questions in the dataset to ensure relevance to actual use cases.

4. Question Diversity. Ensure a balanced distribution of question types:
    * Factual questions (e.g. "Who is the author of this document?")
    * Inferential questions (e.g. "What are the implications of the findings in section 3?)
    * Comparative questions (e.g. "How does the methodology in version 2 differ from that in version 1?)
    * Multi-document questions (e.g. "Summarise the common themes across these three related documents.)
    * Version-specific questions (e.g. "What changes have been made to the conclusion between versions 3 and 4?)

5. Context Selection
    * For each question, select a relevant context from the document(s).
    * Include both perfectly matching contexts and partially relevant contexts to test the system's ability to handle nuanced scenarios.

6. Answer Generation
    * Generate a gold standard answer for each question-context pair.
    * Use a combination of automated methods and human expert review to ensure answer quality

7. Metadata Inclusion
    * Include relevant metadata for each question-context-answer triplet, such as document version, page numbers or section headings.

8. Edge Case Scenarios
    * Deliberately include edge cases, such as questions about rare document types or extremely long documents.
    * Create questions that require an understanding of document structure, such as tables of contents or footnotes.

9. Negative Examples
    * Include some questions that cannot be answered from the given context to test the system's ability to recognise when it doesn't have sufficient information.


#### ii. Periodic Updates
The validation dataset will be updated periodically to maintain its relevance and comprehensiveness. This includes:

* Addition of newly uploaded documents
* Including new versions of existing documents
* Updating the question set to reflect evolving user needs

We recommend updating the validation set monthly or whenever there's a significant influx of new documents or versions.

#### iii. Stratified Sampling
To ensure balanced representation, we'll use stratified sampling when creating the validation set. Strata may include:

* Document length (short, medium, long)
* Document type (text, scanned image)
* Topic areas
* Query complexity (simple factual, multi-step reasoning, version comparison)

- **Key Takeaways:**
    1. The selection of a validation schema is crucial for accurately measuring a model's performance on unseen data, requiring careful consideration of the specific characteristics of the dataset and the problem at hand.
    2. Standard validation schemas provide a solid foundation for most machine learning applications, but non-trivial schemas may be necessary to address unique challenges or specific data characteristics.
    3. Updating validation splits in response to new data or changing distributions is essential for maintaining the relevance and accuracy of performance estimates.
    4. Detailed planning and documentation of the chosen validation schemas within the design document are vital for ensuring the evaluation process is aligned with the project's goals and constraints.

### **V. Baseline Solution**

Regexp/full text search (Elasticsearch) to get Documents and Sentences
Show to user highlighted parts of document

- **Key Takeaways:**
    1. Establishing a baseline is a critical first step in machine learning system design, serving as a simple, operational starting point for iterative improvement.
    2. The choice of baseline should be guided by a trade-off between desired accuracy and the effort required for development, with simplicity often providing significant advantages in terms of robustness, scalability, and interpretability.
    3. In deep learning applications, leveraging pretrained models or training simple models from scratch can provide effective baselines, with the choice influenced by the specific requirements and constraints of the project.
    4. Continuous evaluation and comparison against the baseline are essential for guiding the development process, ensuring that complexity is added only when it yields proportional benefits in performance.

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

### **i. Embeddings Database**

This is one of the core components in the system for efficient document search and retrival.

**i.i. Embeddings Generation**

- **Query Embeddings:** Converts client's query into embeddings for further selecting nearest neighbours within the database.
- **Document Embeddings:** Creates embeddings using a pre-trained BERT-based model for new documents. The model processes documents via API request, resulting in document ID, document metadata and embeddings. The original file is stored in Documents Storage with the same document ID to avoid overloading the vector DB.
- **Updates:** Automatically updates embeddings when document version changes to maintain vectors relevance.

**i.ii. Database Features**

- A cloud and scalable database.
- Supports nearest neighbor search, using cosine or Euclidean similarity.
- The following fields are stored for further mapping with Documents Storage:
  1. Document ID
  2. Version number
  3. Metadata storage (document title, author, creation date)
  4. Embeddings representation 
- Embeddings, metadata, and queries are encrypted to ensure security. Strict access control managment. 

### **ii. Documents Storage**

A scalable cloud service (e.g. AWS S3) for scalable storage and files managment for the following data types:
1. Original files uploaded by clients, including their versions. The service returns a URL (Document ID) for each uploaded file, which is stored in the embeddings database metadata.
2. Chat communications and response ratings, structured as JSON objects with fields for user queries, responses, timestamps and ratings.

### **iii. Chat UI**

An intuitive and responsive interface for clients to query and receive results.

**Features.**
  1. Clients can upload new documents, which automatically triggers embedding generation and storage.
  2. Consists of a five star rating system for feedback on answers.
  3. Clients can report offesnive or unproper responses, which triggers another LLM as a fallback scenario.
  4. Allows to save a chat history and responses for future reference.

### **iv. Backend API Design**

Below are provided events, when a corresponding API action gets triggered, while interacting with a user.

**Documents Management.**
- Upload a new document
- Retrieve document metadata
- Retrieve all versions of a document
- Retrieve a specific version of a document
- Query Processing Endpoints

**User Queries Management.**
- Retrieve a query result
- Rate a query response
- Report an inappropriate response

**Embeddings Management.**
- Generate embeddings for a new document
- Update embeddings for a document version change

**Chat Session Management.**
- Start a new chat session
- Retrieve chat history
- Save chat history

### **v. Parallel processing**
To efficiently handle simultanneous queries from users, the system uses queue of requests and pool of worker nodes.

Worker Allocation:
- **General Workers.** Handle initial document processing and embedding generation.
- **Dedicated Workers.** Preserve workers for entire chat sessions to maintain context and improve response relevance.


### **vi. SLAs**

In order to control system performance and meeting defined standards, the MagicSharepoint service is integrated with a monitoring tool.

Key validated compontents
- **Response Time.** Guarantee first token response within 1 minute.
- **Uptime.** Ensure a high availability rate, aiming to 99.9% uptime.

### **vii. Fallback Strategies**

Fallbacks are crucial for maintaining operational efficiency in the face of unforeseen circumstances. MagicSharepoint uses a multi-tiered fallback system to ensure seamless service:

- **Primary fallback.** The primary model is served by the chosen vendor. It is used unless negative user feedback on the model outcome and if the latency out of the accepted range, described in ? section.
- **Secondary fallback.** Our next layer of fallback involves using a pretrained LLM from Hugging Face, installed locally. This approach addressed to adress both potential issues.

The system has latency and feedback based switchings, which reroutes requests to the secondary model. Once conditions are improved, it switches to the primary model.

### **XI. Monitoring**

Time to get answer
Time to first token
???

1. **Monitoring is Essential**: Without proper monitoring, even the most sophisticated ML models can fail, highlighting the need for robust monitoring frameworks that include software health, data quality, and model performance.
2. **Proactive Maintenance**: Proactive strategies in monitoring can mitigate risks associated with data drift and model decay, ensuring that ML systems continue to perform optimally over time.
3. **Integrated Approach**: Effective monitoring combines traditional software monitoring techniques with new approaches tailored to the nuances of ML systems, integrating data quality checks, performance benchmarks, and business KPIs to create a holistic view of system health.
4. **Continuous Improvement**: The field of ML monitoring is evolving, necessitating ongoing adjustments to monitoring practices as new challenges and technological advancements arise.

### **XII. Serving and inference**

The inference part of a system would contain from 3 major on-premise services:
- **Embedding service**
- **OCR service**
- **Chat service**

While following parts of a solution are considered as external cloud services:
- **Vector database**
- **Document storage**
- **Metadata database**
- **LLM**

### **i. Serving architecture**

On-premise services would be hosted as REST API services hosted in Docker containers and orchestrated by Kubernetes cluster.

#### **Embedding service**

Invoked upon every document receiving a version.
Should pull required metadata from other databases to enrich embeddings with metadata upon saving.

For every document:
- Invoke OCD service.
    - If document is Image based.
- Pull text representation from Document storage.
- Embedding service will generate embeddings.
    - on different aggregations - from document down to sentence level.
- Import embeddings with corresponding metadata into the Vector database.

#### **OCR service**

Invoked from Embedding service.

For every image document:
- Invoke OCR process.
- Import text representation into the Document storage

#### **Chat service**

Invoked on every question.

For every question:
- Retrieve scope metadata from Metadata database.
- Confirm scope with user on Chat UI.
    - If scope request was non-explicit.
    - ~~Can start generating response before client would confirm it.~~
- Retrieve chat history.
    - If any.
- Use internal cache.
    - If scope, chat history and question match.
    - No fuzzymatch
- Context search in Vector database.
- Construct Prompt & invoke LLM.
- Receive response ~~and stream generated tokens to the Chat UI~~.
- Invoke guardrails.
- Calculate performance and consumption statistics.
- Calculate automated quality metrics (if any).
- Make decision and execute it: 
    - Return answer to the Chat UI.
    - Request more details from user on Chat UI.
    - Adjust response according to standards.
- Save record to the internal cache.
- Save chat history to Metadata database.

### **ii. Infrastructure**

**Embedding service** and **OCR service** should be hosted on GPU nodes.
As we expect around 500 new document versions per month, they could be hosted on Spot machines or be start-stopped on demand (if it will take less than 2 minutes).
Both services would be stopped if they won't receive new requests within 30 (??) minutes.

No need in scaling services, unless bulk processing is expected. If so - then we may consider scaling by the number of machines or by GPU grade/size.

**Chat service** could be hosted on non-GPU node. More focus on the RAM to manage the internal cache, then on CPU.

### **iii. Monitoring**

Key **inference** metrics:
- ~~Time to first token~~
- Time to show full reponse.
- % of questions covered by internal cache.
- % of answers rejected by guardrail.
- % of answers requested more details from user.
- % of questions having empty context.
- % of explicit 'we can not answer' answers. 
- Average chat history lenght.
