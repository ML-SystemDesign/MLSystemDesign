# MagicSharepoint

### **I. Problem definition**

### **i. Origin**

MagicSharepoint is a platform designed for collaborative work and document sharing among clients. Documents can be in text format (Markdown) or scanned/image formats.

- Expected Document Size: Up to 500 pages (system restriction).
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

In the future, we may reuse some components of Q&A solutions as a starting point for building a Knowledge Center for clients, allowing documents to be represented as a graph of facts or knowledge.

### **iii. Expectations**

Client expect answers to be:
- Fast
    - First token within 1 minute.
- Trustfull
    - Limited hallucinations or 'extended' answers. At least 95% of the answers should not contain fact missmatching.
    - In case if clients would have any doubts, they won't need to proofread whole document to resolve the uncertainty. 
- Interactive
    - Ability to provide more details/follow-up questions if the answer is insufficient.
    - Automatically be requested for more details if could not generate insufficient answer.
- Direct
    - Indicate when an answer cannot be provided due to lack of context.

Client need to get answers about:
- A single document in its latest version.
- Multiple documents in their latest versions.
- A single document and its various versions.
- Multiple documents and their various versions.

Client wants to select documents:
- Explicit, using filters.
- Implicit, through dialogue.

Use case examples:

We will categorize the use cases as follows:

- Addressable - The system's response must be useful and relevant.
- Non-addressable - The system cannot provide an answer, or the question is beyond the scope of its capabilities. In such cases, the appropriate action is to implement a proper restriction. Although those questions might not bring benefits, it's very crucial to have a proper fallback.

We will use split the use cases to better define the overall problem space for the solution.
We will index the use cases as `Na` where `N` is a priority and `a/na` is an addressability flag.

Addressable use cases:
- `1a` Specific Question about Document Content Available in the Document
    - e.g., how the Attention mechanism works from 'Attention is All You Need'.
- `2a` Specific Question about Document Version Changes
    - e.g., how section names changed between v2 and v12 from 'Machine Learning System Design'.
- `3a` Specific Question about Multiple Document Version Changes
    - e.g., differences between the first available draft and the published version for all books in the 'Harry Potter' series.
- `4a` Specific Question about Document Metadata (propose to remove - too litle data to use any algorithms)
    - e.g., author and date.

Non-addressable use cases
- `1na` Abstract/Not Relevant Question about Document Content
    - e.g., how are you doing.
- `2na` Specific Question about Document Content Not Available in the Document
    - e.g., how the Attention mechanism works from 'Bible'.


### **iiii. Previous work**

- Implemented 'smart' full-text search to help navigate faster and easier.
    - Similar to the "match" query available in the Elasticsearch out of the box.
- Cataloged frequent questions and used Mechanical Turk to get answers in advance.

### **iv. Usage volumes and patterns**

Every month:
- Platform has ~1000 unique users.
- Each user is having 10 document reading sessions.
- Reading session lasts between 30 and 120 minutes.
- Version is assigned to 500 documents.
    - New documents or edited existing documents.
- 10% of the documents are image based.

### **v. Other details**

- Cloud Object Storage with automated version cataloging.
- OCR is not implemented.
- Documents could be sent to service vendors, provided they are not used for training as per SLA (e.g., OpenAI, Anthropic, etc.).

### **II. Metrics and losses**

**i. Metrics**

The task could be split into independent subtasks: data extraction (OCR) and data retrieval and answer generation. These parts can be evaluated independently to prioritize improvements based on the source of errors as well as overall solution performance.

***Data Extraction Metrics:***

Pre-requirements: Dataset of scanned documents and appropriate them texts. (As a work around: readable documents could be scanned manually, which gives both - scanned image and ground truth text values)

It’s reasonable to measure OCR quality separately, as in the case of poor OCR quality, an accurate result can’t be reached. On the first stage of project let’s skip this metrics step, calculate high-level metrics on markup docs vs scanned images, only in case of significant difference in numbers data extraction metrics are calculated. 

**a. Word Error Rate**

It operates on the word level and shows the percentage of words that are misspelled in the OCR output compared to the ground truth. LLMs usually cope well with misprints; however, there still could be errors in important data, so WER could be used as a quick check of OCR quality. The lower the better. It could be replaced with the Character Error Rate.

**b. Formula Error Rate**

Formulas could be presented in the document as well. They are not a typical OCR problem, so if they are not recognized well, it degrades the system performance. The formula error rate could be measured as the percentage of incorrect OCRed formulas to the total amount of formulas.

**c. Cell Error Rate**

As it is important to extract table-structured data as well, the percentage of incorrectly detected table cells compared to the ground truth could be used as one of the metrics.

***Retrieval Metrics:***

Pre-requirements: Dataset of queries collected from experts and list of N most relevant chunks for each of the query. 

**d. Recall@k**

It determines how many relevant results from all existing relevant results for the query are returned in the retrieval step, where K is the number of results considered, a hyperparameter of the model. 

**e. Normalized discounted cumulative gain (NDCG)**
NDCG is a metric that calculates the average of DCGs for a given set of results, which is a measure of the sum of relevance scores taken from the first N results divided by the ideal DCG. In its turn, DCG is the sum of relevance scores among the first N results in the order of decreasing relevance. 

***Answer Generation Metrics:***

**f. Average Relevance Score**

Measures how well the generated answers match the context and query. 
There are several approaches to calculate that metric:
- automatically with framework (detailed description provided in section IV. Validation Schema)
- with other llms paper to consider https://arxiv.org/pdf/2305.06311
- manually based on experts output (approach is provided in section IX. Measuring and reporting)

  
**g. Hallucination Rate**

As one of the requirements is to avoid hallucinating, it is possible to calculate the percentage of incorrect or fabricated information in the generated answers. 

How to calculate: 

- Manually: prepare dataset of queries (including queries without answer in dataset)  + expected responses; calculate by comparing expected response to provided
- Finetune smaller llms to detect hallucination
- Add guardrails  https://github.com/NVIDIA/NeMo-Guardrails - it will not only improve reponse, but also helps to calculate amount of times, model attempts to hallucinate

**h. Clarification Capability**

Pre-requirements: Dataset of queries (ideally with unambiguous answer) + expected response, domain experts to evaluate the metric manually.

As one of the requirements is the ability to automatically request more details if an insufficient answer is generated, the average number of interactions or follow-up questions needed to clarify or correct an answer could be calculated to measure clarification capability and average relevance of follow-up questions. This metric helps to check the system’s ability to provide comprehensive answers initially or minimise the number of interactions needed for detalization.



*Metrics to pick:*

A lot of metrics were provided, but it’s a good idea to go in the reverse direction: start from the more general and dive deeper into partial ones only when necessary.
*TODO*: build hierarchy of metrics

*Online metrics of interest during A/B tests are (more details are provided in section IX. Measuring and reporting):*
- Time to Retrieve (TTR)
- Average Relevance score
- Average amount of clarification questions
- Average time of dialogue




### **III. Dataset**

We have two types of data:
* data that was used to train the main LLM model;
* data to perform RAG on.

We don't control the data for the main LLM training (meaning that we're coping with the LLM limitations and don't influence this until the moment we realize that we could really benefit from fine-tuning, which will become completely different project).

#### i. Data to perform RAG on - description

We don't distinguish between client roles for data access. Every client would have access to every document. So we basically have a shared dataset for the whole system.

Includes a set of Documents available on the Platform. Documents can be in text format (Markdown) or scanned/image formats.

1. For Markdown documents
    - Expected Document Size: Up to 500 pages.
    - Structure: Documents larger than 10 pages typically include a table of contents and dedicated sections, such as introduction or glossary.
    - Content: Documents may include text with all Markdown features (e.g., quotes, headings, formulas, tables).
2. For documents in Image form (scans/images): no additional description available. They are just files that contain image/scan inside.
3. Each document have origination metadata
4. Documents may have v1-v2-v3-... versions. For some Documents we know the diff between vX and vY. For some Documents we do not know the diff, and only know how Document looked like at each version.

Clients can edit documents online via the platform or upload documents from their local machines. Each document receives a version number upon:

- Saving
- Uploading a new document
- Uploading a version of the existing document

Clients can access all versions of each document.

#### ii. Data cleaning

Data cleaning process should be automatized with reproducible scripts. Script runs once for each new document that gets uploaded to the system and then for each new version of that document.

**1. Markdown Documents:**
- Inter-document links (links that reference another part of the same document or another document in the system): enrich such links with aliases before using them for RAG purposes, i.e. add a descriptive label or name to the link that makes it more meaningful. This helps RAG to understand the context and content of the link. Example: "Section 2" -> "Section 2: Data Cleaning Procedures"
- Plain URLs (links to external web resources): don't modify, keep them as-is for LLM consumption.
- Table of Contents (ToC): Generate or validate the presence of a ToC for documents longer than 10 pages.

**2. Scanned/Image Documents:**
- Enhance the quality of scans (e.g., adjusting brightness/contrast, removing noise).
- Perform Optical Character Recognition (OCR) for scans. Store both initial scan and its recognized content.

We don't perform duplicate removal neither for markdown nor for images.scans, considering that if the client uploaded several duplicating documents, he has the reason to do this, and this is as it should be.

Cleaned documents and images should be stored separately from the original files in a `cleaned_data` directory. This ensures keeping the original versions for reference and debugging.

#### iii. Metadata

**Document Metadata:**
- Document title
- Author
- Creation date
- Last modified date
- Table of Contents (for text documents)
- Summary (need to discuss whether it's necessary)
- Version history
   - Version number
   - Editor
   - Version creation date
   - Changes made in the version (if available)
   - Diff information (if available)

**Handling Metadata:**

For Markdown documents, embed metadata in a YAML format at the top of each document. For images, metadata can be stored in a separate JSON file with the same name as the image.

#### Example Metadata Structure for a Markdown Document:

```yaml
---
title: "Sample Document"
author: "John Doe"
created_at: "2023-01-01"
last_modified: "2024-06-30"
toc:
  - chapter: Introduction
    page_start: 2
    starts_with: In this article we're about to introduce RAG implementation system for high-load cases.
    chapter_summary: Introduction to RAG implementation system for high-load cases with author's motivation and real-world examples
  - chapter: Chapter 1
    page_start: 3
    starts_with: Let's consider a situation where we have a platform designed for collaborative work and document sharing among clients.
    chapter_summary: Problem statement and available data are described.
  - chapter: Chapter 2
    page_start: 6
    starts_with: In order to perform quality RAG, we need the data to be prepared for this.
    chapter_summary: Data cleaning schema and other aspects.
  - chapter: Conclusion
    page_start: 10
    starts_with: Now let's move on to conclusion.
    chapter_summary: Conclusion about the ways we can built a system
summary: "This document provides an overview of..."
version_info:
  - version: "v1"
    editor: "Jane Smith"
    change_date: "2023-02-01"
    diff: "Initial creation of the document."
  - version: "v2"
    editor: "John Doe"
    change_date: "2023-06-15"
    diff: "Added new chapter on advanced topics."
  - version: "v3"
    editor: "Jane Smith"
    change_date: "2024-06-30"
    diff: "Updated the introduction and conclusion sections."
---
```

### **IV. Validation Schema**

For validation purposes, we will use a data set generated from the original documents using the [RAGAS](https://docs.ragas.io/en/stable/)  functionality. This approach allows us to create a comprehensive validation set that closely mirrors the real-world usage of our system.

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


### V. Baseline Solution

#### Document Extraction Process

Considering the minimal variability among the documents and the sufficient coverage of most cases within the current dataset, it is recommended to implement an in-house document extraction pipeline. This pipeline should consist of:

1. File Type Handler: Differentiate and handle file types accordingly, as PDFs and images may require additional processing steps.
2. Text Extraction: Deploy a customized OCR solution designed to handle non-text elements.
3. Markdown Formatting: Ensure that the extracted content is formatted correctly according to markdown standards.
4. Error Management & Spell Checking: This part ensures extraction logging and raises awareness for the maintainer that some documents might not be reliable.

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
    - Given query passed trough the preprocessing layer, Executes parallelized scoring computations
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
    - DB simularity search. This part is usually provided by the same tools utilized for indexing.
3. Chat Service:
    - Manages chat context
    - Prompt template constructor: supports diaologs for clarification
    - Stores chat history
4. Synthesis Component:
    - Utilizes an LLM for response generation
6. Representation Layer:
    - Provides a dialogue mode for user interaction.
    - User Feedback: Collects user input to continuously refine the system.

##### Locating the components

###### Embedder

A good embedder should define the retrieval layer's ability to:

- Store content representations efficiently
- Encode the content and queries to be semantically similar
- Capture nuanced details due to domain-specific content

Considering that the retrieval layer is the first step in the pipeline, we do believe it could become a bottleneck in performance. This is because if the context is provided incorrectly, there is less ability for the upstream generation model to improve upon the irrelevant context.

Taking these factors into account, we might be ready to explore and evaluate the performance of various encoders at our disposal. We seek to avoid being restricted by any particular design solution, ensuring that there is room for continuous enhancement over time. With this perspective, we will consider the potential of implementing an in-house embedding solution.

Here are the benefits we highlight:
- Provides potential for improving this critical component without vendor lock-in
- Provides control over versioning, determinism, availability (not going to be depricated)
- Does not require us to provide per-token costs
- Could potentially benefit from interaction data enhancements

Drawbacks:
- Development and maintenance costs.
- Per-token costs may not be as optimized as those of larger companies.

When it comes to generation levels, considering the number of users and the app economy, there is no clear evidence that the company would like to invest in training or fine-tuning custom LLMs. Therefore, it might be beneficial to keep in mind the use of vendor-based API-accessible LLMs.

Here are the potential benefits:
- LLMs are continually improving, particularly in few-shot learning capabilities
- Competitive market dynamics are driving down the cost of API calls over time
- Switching vendors involves minimal effort since it only requires switching APIs, allowing for potential utilization of multiple vendors.

Drawbacks:
- Less control over the responses
- Data privacy (though not a significant concern)
- There is a possibility of service denial from a vendor on account of policy-related issues, such as content restrictions or economic sanctions

#### Framework Selection

When considering a framework, we would like it to support the following features:
1. Document storage
2. Index storage
3. Chat service
4. Modular design for document extraction that supports custom modules
5. Modular design for retrieval and generation that can utilize both local and vendor-based solutions
6. Built-in logging and monitoring capabilities

We will compare a couple of popular frameworks that might suit our needs: LlamaIndex and LangChain.

Here are some resources that summarize the differences between the two frameworks:
1. [LlamaIndex vs LangChain: Haystack – Choosing the Right One](https://www.linkedin.com/pulse/llamaindex-vs-langchain-haystack-choosing-right-one-subramaniam-yvere/)
2. [LlamaIndex vs LangChain: Key Differences](https://softwaremind.com/blog/llamaindex-vs-langchain-key-differences/)
3. [LangChain vs LlamaIndex: Main Differences](https://addepto.com/blog/langchain-vs-llamaindex-main-differences/)

| Feature/Aspect          | LangChain                                        | LlamaIndex                                      |
|-------------------------|--------------------------------------------------|-------------------------------------------------|
| **Main Purpose**        | Various tasks                                    | Querying and retrieving information using LLMs  |
| **Modularity**          | High, allows swapping of components              | Average, yet sufficient for our current design  |
| **Workflow Management** | High, supports managing chains of models/prompts | Average, primarily focused on querying          |
| **Integration**         | High: APIs, databases, etc.                      | Average: APIs, data sources, but customizable   |
| **Tooling**             | Debugging, monitoring, optimization              | Debugging, monitoring                           |
| **LLM Flexibility**     | Supports various LLMs (local/APIs)               | Supports various LLMs (local/APIs)              |
| **Indexing**            | No primary focus on indexing                     | Core feature, creates indices for data          |
| **Query Interface**     | Complex workflows                                | Straightforward                                 |
| **Optimization**        | Optimization of LLM applications                 | Optimized for the retrieval of relevant data    |
| **Ease of Use**         | Challenging                                      | Easy                                            |

Given the pros and cons listed above, it appears that LlamaIndex provides all the features we are looking for, combined with an ease of use that could reduce development and maintenance costs. Additionally, LlamaIndex offers enterprise cloud versions of the platform. If our solution evolves towards a simpler design, we might want to move to the paid cloud version if it makes economical sense.

### **VI. Error analysis**

**i. Learning Curve Analysis**

During embeddings tuning, it’s a good idea to review loss curves for an overall understanding of training dynamics, hyperparameter tuning, etc. In the case of a composite loss, it’s reasonable to visualize each of the components separately. Beyond the loss curves, it’s also reasonable to have metric curves, as they help understand if the chosen metric and loss are connected.

To draw conclusions about the required amount of data and the potential benefit of data gathering, it is useful to plot the dependency of train dataset size versus the metric. Let’s fix a test dataset, and then train the model on subsamples of the train data to estimate how new data improves overall performance. However, this approach is resource-intensive.

**ii. Residual Analysis**

For each error on the holdout dataset, analyze the query in the following way (e.g., query topic, is the answer presented in the document, is the query on document versions' differences, irrelevant queries, does the query require follow-up questions, etc.). Then, identify the most sensitive category and analyze if it is an expected result. If the metric is not binary, but a score from 0 to 5, consider only errors with the lowest relevance scores, for example, 0-3.

Corner-cases to check:

- Queries with typos/documents with typos
- Toxic text generation
- Data safety
- Formulas/tables search support

Ask an expert to test the process. Whenever the expert does not agree, ask them to provide a reason, explore the reason, and improve the system.


### **VII. Training Pipeline**

### **i. Overview**

For Chat and RAG system built on external APIs the most important:
- **Stable Data Preprocessing.** Should be executed regularly upon new document being submitted. 
- **Stable context selection.** Enabling robust Prompt Engineering.

As we are planning to use external/pretrained solution for Embedding generation, OCR and LLM, we won't cover such aspects in this section for now.

### **ii. Toolset**

The suggested tools for the pipeline are:
- Python
- Cloud Vector DB service (Pinecone, Azure AI Search, etc.)
- On-premise out-of-the-box OCR / Cloud OCR service
- Docker
- LLM service (OpenAI / Azure OpenAI)

### **iii. Data Preprocessing**

The data preprocessing should include:

- **Text recognition.** OCR module to convert image documents into text representation.
- **Metadata extraction.** Store explicit information and statistics.
- **Feature engineering.** Extract required features on different levels.
- **Feature storage.** Make features accessible and searchable.

The main goal - document should be preprocessed withint 1-2 hours after submition/new version being assigned.

### **iv. Context Evaluation**

For evaluating the context selection, we would use RAGAS tool and previously mentioned metrics over generic Prompts.
The process is automated and allowing to evaluate quality of features and expected response.

### **v. Prompt Evaluation**

To evaluating the Prompt engineering approaches, we would use RAGAS tool and previously mentioned metrics over generic Contexts.
The process is automated and allowing to evaluate quality of features and expected response.

### **v. Chat E2E Evaluation**

To evaluate the Chat functionality E2E, we would use RAGAS tool and previously mentioned.
Thou the process is automated and robust, it does not support evaluation of series of questions and back-to-back questions.

### **vi. Continuous Integration and Deployment**

The pipeline should be integrated into the existing CI/CD infrastructure. This includes setting up automated evaluation on a regular basis, ensuring that the latest data is used and pulled, and releasing changes to production with minimal manual intervention.

### **viii. Monitoring and Maintenance**

We should monitor the model's performance in production and set up alerts for significant deviations from expected performance. This will enable us to catch issues early and trigger retraining or model updates when necessary. (See *Monitoring* chapter.)

### **ix. Future Work and Experimentation**

Considering we would like more customised and/or on-premise models, we will need to extend this section to cover training for in-house models.
The section lacks of testing approaches for the specific user needs, such as asking for more details.

### **VIII. Features**

Our key criterias to select features:
1. **Context selection flexibility.** Users may ask a variety of questions and features needs to ba adaptive enough to ensure that context may be selected for any question.
2. **Context selection relevance.** Besides supporting a variety of questions, we need to ensure that whenever context is selected - it is relevant for the question.
3. **Computational time.** We are not working with online data and not very time-restricted and features could be generated in some lag within several hours. However, as we may encounter pretty long documents (up to 500 pages long) some features could be expensive at such scale.

Adding new features should be formulated as new hypothesis, which should originate from covering specific corner cases or improving metrics.

The idea is not to execute any automated feature selection, rather then focusing on providing the most complete and relevant context in the prompt for LLM.

Features are met in the following services, by their components and areas:
- Metadata enriching service
    - Document-level enriching component
        - Storage and accessing the features
    - Text-level enriching component
        - Training
        - Inference
        - Storage and accessing the features
    - Token-level enriching component
        - Training
        - Inference
        - Storage and accessing the features
- OCR service
    - OCR component
        - Training - out of scope, as the current goal to use external/out of the box solution.
        - Inference - out of scope, as the current goal to use external/out of the box solution.
        - Storage and accessing the features
- Embedding service
    - Embedder component
        - Training - out of scope, as the current goal to use external/out of the box solution.
        - Inference
        - Storage and accessing the features
- Chat service
    - Vector search component
        - Search & access the existing features
    - LLM component
        - Out of scope, as the current goal to use external/out of the box solution.
    - Prompt Engineering component
        - Access the existing features

On the high level, all features could be classified into:
- Document level
- Text level
- Token level

**i. Document level features**

The purpose of this features is to enable easier selection of documents by users.
Either by using filters, or non-explicit mentioning in the chat.
Such features are not explictly extracted or crafted, rather then translating the state of a document from other sources.

As a side effect, they could be usefull for Prompt Engineering to represent some structure to the LLM.

Those features represent general state of a document, such as:
- Creation author
- Creation time
- Version number
- Version assignment time
- Version author
- Total number of versions
- Document version name
- Document version size
- etc.

**ii. Text level features**

Such features are targeting context selection for the Prompt.
They are split into 3 high level groups:
1. **Metadata.** 
    - Not extracted features. Represents high level state/statistics of text.
    - Ex.: 
        - (do not have example for now)
2. **Explicit enriching.**
    - Features which are not explicitly available and should be extracted by models / regexp / other approaches.
    - Ex.:
        - Table of Content
        - Sections start-end
        - Sentiment
        - Overall summary
        - Section summary
        - etc.
3. **Embeddings.**
    - Focusing the ability to properly extract the context from the Vector DB.
    - Ex.:
        - Document embedding
        - Section embedding
        - Summary embedding
        etc.

**iii. Token level features**

This set of features further focusing the ability to select context and document selection.
Examples including:
- NER
- Custom NER
- Links identification
- Cross-reference mappings
    - ex.: 'Section 1' -> 'Section 1: Problem definition'
- Semantic Role Labeling
- etc.


### **IX. Measuring and reporting**

#### i. Measuring Results

**Purpose of the Experiment**

We’ve already deployed a baseline API-based LLM solution. While the baseline shows satisfying results, we have devised a new approach with an in-house solution and will compare the two approaches.

**Why the New Approach is Better than the Previous One**

The main reason for this experiment is that the baseline doesn’t meet the following requirements:

- Full control over the responses.
- Data privacy (though not a significant concern).
- Limited hallucinations or 'extended' answers.

The new approach has full control over responses, doesn’t share any data, and was trained specially to reduce hallucinating answers.

**Offline Experiment and Offline Metrics**

We have trained a transformer model on a document dataset. Assessors were then provided with a series of prompts and answers to evaluate their relevance. The assessors used a 5-point scale where 5 is a full match, and 0 indicates no relevance. They also evaluated how offensive, discriminatory, or inappropriate the answers were. Based on these assessments, we calculated the following statistics:

- **Average Relevance Score:** 4.2
    - Trustful (Percent of answers without made-up facts): 95%
    - Interactive (Percent of correct interactive elaborations): 70%
    - Direct (Percent of correct unprovided answers due to lack of context): 98%
- **Offensive/Discriminatory/Inappropriate Responses:** 0.2%
- **Average Latency of a First Token:** 1 minute
- **Number of Assessors:** 200
- **Number of Query-Answer Pairs:** 1000

**Baseline Solution Metrics**

The same experiment was conducted using the baseline solution with the same assessors. Prompts and answers were shuffled to ensure assessors were unaware of the approach used.

- **Average Relevance Score:** 2.0
    - Trustful (Percent of answers without made-up facts): 80%
    - Interactive (Percent of correct interactive elaborations): 60%
    - Direct (Percent of correct unprovided answers due to lack of context): 90%
- **Offensive/Discriminatory/Inappropriate Responses:** 0.01%
- **Average Latency:** 1 second
- **Number of Assessors:** 200
- **Number of Query-Answer Pairs:** 1000

**Correlation Between Offline and Online Metrics**

Based on offline testing, we anticipate similar results in the online A/B testing.

**Online Metrics**

The online metrics should correlate with revenue, assuming that higher user satisfaction leads to increased profits. We will use the same metrics as offline, with additional ones:

- **Percentage of Negative Reports**
- **Percentage of Positive Reports**

#### ii. A/B Tests

**Hypothesis**

- Based on offline metrics, we expect to improve the **Average Relevance Score**.

**Termination Criteria**

- The approach must respond to the user within an average of 5 minutes.
- The percentage of reports with offending answers must be below 1%.

If any termination criteria are met, the experiment will be paused and resumed after corrections.

**Key Metrics**

- Average Relevance Score
- Number of Insufficient Answer Reports

Auxiliary metrics:

- A total number of documents.
- A daily number of new documents.
- A total number of users.
- A daily number of new users.
- A daily number of sessions.

**Splitting Strategy**

Users will be split into two groups by their IDs. Groups will be swapped after a certain period.

**Statistical Criteria**

Welch’s t-test will be used for statistical analysis.

**Experiment Duration**

The experiment will last two weeks. In the first week, Group A will use the baseline solution, and Group B will use the new solution. In the second week, Group A will use the new solution and Group B the baseline solution.

**Future Steps for Experiment Improvement**

Consider adding an A/A test phase

#### iii. Reporting Results

A main report will be generated at the end of the experiment. An interim report may be created mid-experiment for preliminary conclusions for stakeholders.

The report should include:

- Results with a 95% confidence interval for key and auxiliary metrics.
- Distribution plots for key and auxiliary metrics over time.
- Absolute numbers.
- A brief description of each approach with a link to the full description.
- Conclusion.
- Further steps.

### **X. Integration**

### **i. Embeddings Database**

This is one of the core components in the system for efficient document search and retrival.
It consists of
1. Vector representations of uploaded/created by users files.
2. Chat communications and response ratings, structured with fields of user queries, responses, timestamps, ratings and session id.

**i.i. Embeddings Generation**

- **Query Embeddings:** Converts client's query into embeddings for further selecting nearest neighbours within the database.
- **Document Embeddings:** Creates embeddings using a pre-trained BERT-based model for new documents. The model processes documents via API request, resulting in document ID, document metadata and embeddings. The original file is stored in Documents Storage with the same document ID to avoid overloading the vector DB.
- **Updates:** Automatically updates embeddings when document version changes to maintain vectors relevance.

**i.ii. Database Features**

A cloud and scalable database, e.g. Pinecone. It is designed to scale horizontally to handle large volumes of embeddings.
- Supports nearest neighbor search, using cosine or Euclidean similarity.
- Supports filtering based on metadata.
- The following fields are stored for further mapping with Documents Storage:
  1. Document ID
  2. Version number
  3. Document's Metadata (document title, author, creation date)
  4. Model's Metadata (Baseline / Main embedding tool, model's realease version)
  5. Embeddings representation
- Embeddings, metadata, and queries are encrypted to ensure security. Strict access control managment.

**Database quantified requirements:**
- Should return top-10 nearest neighbors within 100ms for up to 1 million vectors.
- Should support at least 1000 Queries Per Second for nearest neighbor searches on a dataset of 1 million vectors.

### **ii. Documents Storage**

A scalable cloud service (e.g. AWS S3) for scalable storage and files managment for original files uploaded by clients, including their versions. The service returns a URL (Document ID) for each uploaded file, which is stored in the embeddings database metadata.

### **iii. Chat UI**

An intuitive and responsive interface for clients to query and receive results.

**Features.**
  1. Clients can upload new documents, which automatically triggers embedding generation and storage.
  2. Consists of a positive/negative feedback on answers.
  3. Clients can report offesnive or unproper responses, which triggers another LLM as a fallback scenario.
  4. Allows to save a chat history and responses for future reference.

### **iv. OCR**

MagicSharepoint utilizes Optical Character Recognition technology to convert text from image-based documents into machine-readable text. This offers users greater flexibility in input data formats. The algorithm takes an intermediate place between the user upload process and embeddings creation.

**Features.**
1. **Document upload.** The system automatically identifies the format of uploaded file and OCR gets triggered if image-based inputs.
2. **Engine.** A cloud solution is used to be scalable enough to handle large volumes of documents simultaneously.
3. **Storage.** Both the original image and the extracted text is stored in the Documents Storage, linked by the same Document ID.
4. **Multi-language Support.** The OCR engine supports multiple languages to serve wide range of clients.

### **v. Backend API Design**

Below are provided events, when a corresponding API action gets triggered, while interacting with a user.

**Documents Management.**
- Upload a new document
- Retrieve document metadata
- Retrieve all versions of a document
- Retrieve a specific version of a document
- Apply OCR technology for image-basede documents, if required

**User Queries Management.**
- Retrieve a query result
- Rate a query response
- Report an inappropriate response

**Embeddings Management.**
- Generate embeddings for a new document
- Update embeddings for a document version change, keeping previous embiddings, corresponding to the same session id.

**Chat Session Management.**
- Start a new chat session
- Retrieve chat history
- Save chat history

### **vi. Parallel processing**
To efficiently handle simultanneous queries from users, the system uses queue of requests and pool of worker nodes.

Worker Allocation:
- **General Workers.** Handle initial document processing and embedding generation.
- **Dedicated Workers.** Preserve workers for entire chat sessions to maintain context and improve response relevance.

### **vii. SLAs**

In order to control system performance and meeting defined standards, the MagicSharepoint service is integrated with a monitoring tool.

Key validated compontents
- **Response Time.** Guarantee first token response within 1 minute.
- **Uptime.** Ensure a high availability rate, aiming to 99.9% uptime.

### **viii. Fallback Strategies**

Fallbacks are crucial for maintaining operational efficiency in the face of unforeseen circumstances. MagicSharepoint uses a multi-tiered fallback system to ensure seamless service:

- **Primary fallback.** The primary model is served by the chosen vendor. It is used unless negative user feedback on the model outcome and if the latency out of the accepted range.
- **Secondary fallback.** Our next layer of fallback involves using a pretrained LLM from Hugging Face, installed locally. This approach addressed to adress both potential issues.

The system has latency and feedback based switchings, which reroutes requests to the secondary model. Once conditions are improved, it switches to the primary model.

### XI. Monitoring

#### Engineering Logging & Monitoring

1. **Ingestion Layer**: 
    - Process and I/O timings 
    - Code errors

2. **Retrieval**:
   - **Embedder**: Monitor preprocessing time, embedding model time, and utilization instances of the embedding model.
   - **Database (DB)**: Monitor the time taken for each retrieval operation and DB utilization.
   
3. **Generation**:
   - **LLM**: Monitor latency, cost, error rates, uptime, and the volume of generated content to predict scaling needs.

#### ML Logging & Monitoring

1. **Ingestion Layer** 
    - Every step of the ETL pipeline for document extraction must be fully logged to ensure the process is reproducible and help issue resolution
    - Statistics for documents during ingestion should be monitored, including word count, character distribution, document length, paragraph length, detected languages, and the percentage of tables or images
    - Monitor the preprocessing layer to bring awareness of not ingested documetns or documents with too many errors

2. **Retrieval**: 
    - Logging the details of each query, including the tokenizer used, the document context found within a particular document version, and other relevant metadata for future analyses
    - Keep track of the indexes found, similarity scores

3. **Chat History**: Storing all chat history is crucial for a thorough analysis and debugging process, providing valuable insights into user interactions and system performance

4. **Augmented Generation**: 
    - Quality of generated content through user feedback

5. **Alerting Mechanisms**: Have an alerting mechanisms for any anomalies or exceeded thresholds based on the metrics being monitored.

#### Tooling

1. **For RAG Operations - Langfuse Callback**:
    - Integrates with LlamaIndex
    - Supports measuring the quality of the model through user feedback, both explicit and implicit
    - Calculates costs, latency, and total volume
    - For more information about analytics capabilities, see: [Langfuse Analytics Overview](https://langfuse.com/docs/analytics/overview)

2. **For System Health Metrics, Ingestion Layer, Alerting - Prometheus & Grafana**: 
    - Prometheus is an open-source system monitoring and alerting toolkit
    - Grafana is used to visualize the data collected by Prometheus
    - Since LLMs logging is stored within Langfuse, there is no need to build additional solutions for this

    **Why not a standard ELK stack?**

    For more details, please read this great blogpost [Prometheus-vs-ELK](https://www.metricfire.com/blog/prometheus-vs-elk/)

    | Feature/Aspect                 | Prometheus                                        | ELK (Elasticsearch, Logstash, Kibana)              |
    |-------------------------------|---------------------------------------------|---------------------------------------------------|
    | **Primary Use Case**          | Metrics collection and monitoring           | Log management, analysis, and visualization       |
    | **Data Type**                 | Numeric time series data                    | Various data types (numeric, string, boolean, etc.)|
    | **Database Model**            | Time-series DB                              | Search engine with inverted index                 |
    | **Data Ingestion Method**     | Pull-based metrics collection via HTTP      | Log collection from various sources using Beats and Logstash |
    | **Data Retention**            | Short-term (default 15 days, configurable)  | Long-term                                          |
    | **Visualization Tool**        | Grafana                                     | Kibana                                             |
    | **Alerting**                  | Integrated with Prometheus                  | Extensions                                         |
    | **Operational Complexity**    | Lower (single-node)                         | Higher (clustering)                               |
    | **Scalability**               | Limited horizontal scaling                  | High horizontal and vertical scalability           |
    | **Setup and Configuration**   | Simple                                      | Complex                                            |

    **Pros for this solution:**
    1. Metric-Focused Monitoring: Prometheus is optimized for collecting and analyzing time-series data, making it ideal for tracking metrics
    2. Ease of Setup and Configuration: Prometheus's pull-based model simplifies the setup process
    3. Operational Simplicity: It is advantageous without needing a large, dedicated team to manage it
    4. Real-Time Alerts and Querying: Prometheus provides a powerful query language (PromQL) and supports real-time alerting

    **Cons:**
    1. No vertical scaling.
    2. Limited logs data retention. This might become a problem if we change the RAG framework and want to store ML logs elsewhere

3. **Code error reports - Sentry.io**:
    - Sentry is a widely-used error tracking tool that helps developers monitor, fix, and optimize application performance
    - We may choose between self-hosted versions and the paid cloud version in the future.


### **XII. Serving and inference**

The inference part of a system would contain from 3 major on-premise services:
- **Embedding service**
- **OCR service**
- **Chat service**

Also on-remise we should have the following infrastructure services:
- **Load Balancer service**
- **Cacher service** 

While following parts of a solution are considered as external cloud services:
- **Vector database**
- **Document storage**
- **Metadata database**
- **LLM**

### **i. Serving architecture**

On-premise services would be hosted as REST API services hosted in Docker containers and orchestrated by Kubernetes cluster.
Chat service should support an option to stream a responce via https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events (default OpenAI tool to stream response).

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

**Load Balancer service** should be hosted on a regular node. It should be responsible for routing requests and start-stopping for **Embedding service** and **OCR service**.

**Cacher service** would have Redis under the hood, and to be hosted on a regular node. 

**Embedding service** and **OCR service** should be hosted on GPU nodes.
As we expect around 500 new document versions per month, they could be hosted on Spot machines or be start-stopped on demand (if it will take less than 2 minutes).
Both services would be stopped if they won't receive new requests within 30 (??) minutes.

**Chat service** could be hosted on non-GPU node. More focus on the RAM to manage retrieved contexts and assemble prompts, then on CPU. Minimal RAM should be 4 gb. Will be connected with **Cacher service** to manage cached/frequently similar requests.

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
