# MagicSharepoint

### **I. Problem definition**

### **i. Origin**

MagicSharepoint is a platform designed for collaborative work and document sharing among clients. Documents can be in text format (Markdown) or scanned/image formats.

- Expected Document Size: Up to 500 pages (system restriction).
- Structure: Documents typically include a table of contents and dedicated sections, such as introduction or glossary.
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
    - Limited hallucinations or 'extended' answers. At least 95% of the answers should not contain fact missmatching of level 1 and level 2 (described below).
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

Fact missmatching levels:
1. Fact Presence
- Numbers / Terms / Facts in the answer were not present in the document
2. Fact Integrity
- Numbers / Terms / Facts in the answer were present in the document but had different context and meaning
3. Reasoning
- Numbers / Terms / Facts in the answer were correct and had the same context and meaning, but the conclusion was off

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

The task could be split into independent subtasks: OCR, Intent classification -> Search/Retrieval -> answer generation. These parts can be evaluated independently to prioritize improvements based on the source of errors as well as overall solution performance.


***Question Intent Classification Metrics:***

Pre-requirements: Define all possible labels (intents). Prepare dataset of questions/sentences and appropriate labels.

It shows how accurately a model can classify the intent of questions, which is crucial for the whole processing pipeline. Below metrics could be used for QIC (macro + per class): 

1. Precision
2. Recall
3. F1

   
***Data Extraction Metrics:***

Pre-requirements: Dataset of scanned documents and appropriate them texts. (As a work around: readable documents could be scanned manually, which gives both - scanned image and ground truth text values)

It’s reasonable to measure OCR quality separately, as in the case of poor OCR quality, an accurate result can’t be reached. On the first stage of project let’s skip this metrics step, calculate high-level metrics on markup docs vs scanned images, only in case of significant difference in numbers data extraction metrics are calculated. 

**a. Word Error Rate**

It operates on the word level and shows the percentage of words that are misspelled in the OCR output compared to the ground truth. LLMs usually cope well with misprints; however, there still could be errors in important data, so WER could be used as a quick check of OCR quality. The lower the better. It could be replaced with the Character Error Rate.

$`word\_error\_rate = \frac{amount\_of\_misspelled\_words}{total\_amount\_of\_words}`$

**b. Formula Error Rate**

Formulas could be presented in the document as well. They are not a typical OCR problem, so if they are not recognized well, it degrades the system performance. The formula error rate could be measured as the percentage of incorrect OCRed formulas to the total amount of formulas.

$`formula\_error\_rate = \frac{amount\_of\_misspelled\_formulas}{total\_amount\_of\_formulas}`$

**c. Cell Error Rate**

As it is important to extract table-structured data as well, the percentage of incorrectly detected table cells compared to the ground truth could be used as one of the metrics.

$`cell\_error\_rate = \frac{amount\_of\_incorrectly\_detected\_cells}{total\_amount\_of\_cells}`$


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
- automatically with framework ragas [https://docs.ragas.io/en/stable/] (detailed description provided in section IV. Validation Schema)
- with other llms (paper to consider "Automatic Evaluation of Attribution by Large Language Models" [https://arxiv.org/pdf/2305.06311])
- manually based on experts output (approach is provided in section IX. Measuring and reporting)

  
**g. Hallucination Rate**

As one of the requirements is to avoid hallucinating, it is possible to calculate the percentage of incorrect or fabricated information in the generated answers. 

How to calculate: 

- Manually: prepare dataset of queries (including queries without answer in dataset)  + expected responses; calculate by comparing expected response to provided
- Finetune smaller llms to detect hallucination
- Add guardrails [https://github.com/NVIDIA/NeMo-Guardrails] - it will not only improve reponse, but also helps to calculate amount of times, model attempts to hallucinate

$`hallucination\_rate = \frac{amount\_of\_hallucinated\_responses}{total\_amount\_of\_responses}`$


**h. Clarification Capability**

Pre-requirements: Dataset of queries (ideally with unambiguous answer) + expected response, domain experts to evaluate the metric manually.

As one of the requirements is the ability to automatically request more details if an insufficient answer is generated, the average number of interactions or follow-up questions needed to clarify or correct an answer could be calculated to measure clarification capability and average relevance of follow-up questions. This metric helps to check the system’s ability to provide comprehensive answers initially or minimise the number of interactions needed for detalization.

$`clarification\_capability = \frac{number\_of\_clarification\_questions}{total\_amount\_of\_queries}`$


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
    - Structure: Documents typically include a table of contents and dedicated sections, such as introduction or glossary.
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
- Table of Contents (ToC): Generate or validate the presence of a ToC for documents.

**2. Scanned/Image Documents:**
- Enhance the quality of scans (e.g., adjusting brightness/contrast, removing noise).
- Perform Optical Character Recognition (OCR) for scans. Store both initial scan and its recognized content.

We don't perform duplicate removal neither for markdown nor for images.scans, considering that if the client uploaded several duplicating documents, he has the reason to do this, and this is as it should be.

#### ii. Data chunking strategy

Since our documents may be of a very different size, we need to split them into chunks for later [embedding](#Embedder) creation. Embeddings will cover a document, an article, a paragraph and a sentence.
A paragraph and a sentence are usually good to go without chunking. For articles - we'll chunk them by sections or other semantical parts, if possible. If that's not applicable (there's no sections in the document), then we apply chunking by 1k tokens (rougly 1-2 pages of text) with 20% overlap. Whole documents are to be chunked by articles and inside article - the same strategy as for article applies. This process will give us a hierarchical structure with chunked documents.

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
    starts_with: In this article we're about to introduce RAG implementation system for high-load cases.
    chapter_summary: Introduction to RAG implementation system for high-load cases with author's motivation and real-world examples
  - chapter: Chapter 1
    starts_with: Let's consider a situation where we have a platform designed for collaborative work and document sharing among clients.
    chapter_summary: Problem statement and available data are described.
  - chapter: Chapter 2
    starts_with: In order to perform quality RAG, we need the data to be prepared for this.
    chapter_summary: Data cleaning schema and other aspects.
  - chapter: Conclusion
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
    * Include relevant metadata for each question-context-answer triplet, such as document version or section headings.

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
The high-level flow is depicted in the image below.

![Retrieval Baseline](docs/retrieval_baseline.png)

Considering the solution that covers both Retrieval and Generation, we might divide the scope in terms of functionality into three parts:

- Basic solution: Raw generation from the model given a context.
- Reliable solution: Queries are checked to be correct, outputs are only generated when the context is considered relevant, the outputs are checked on correctness.
- Reliable and interactive solution: Reliable, with a context constructed by taking into account the dialogue interaction. The solution is expected to ask for clarification if the context is not clear or the output is ambiguous.
The idea here is that although the interactive mode brings value and covers the use cases described in Section 1, it comes with the cost of creating, managing, and monitoring a complex workflow.


Below you will find the schemas describing trhee options of increased complexity during implementation. The description is available further in the chapter.

**RAG: Basic solution**

![RAG Baseline](docs/rag_simple.png)

**RAG: Reliable solution**

![RAG Reliable](docs/rag_reliable.png)

**RAG: Reliable & Interactive**

![RAG Reliable & Interactive](docs/rag_reliable_interactive.png)

#### Retrieval Baseline: Sparse Encoded Solution

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

#### RAG

##### RAG: common functionality 

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


##### RAG: Bridging the Qualitative Gap

Currently, the common functionality lacks modules to ensure the solution meets quality criteria, specifically in areas such as hallucination mitigation and tolerance against misuse. To address these gaps, we propose using guardrails for quality assurance. This includes a retry strategy and a fallback mechanism designed to enhance reliability and robustness.

NeMo guardrails provide the following levels of checks that are relevant to our project:

- Input rails
- Dialogue rails
- Retrieval rails
- Output rails

The first two are simple use cases with fallback: just determine if a query needs to be executed.

The retrieval rails are designed to help reject non-relevant chunks or alter them. Although this part might be crucial, it's hard to design the specific implementation in advance.

The output rails are used to check the correctness of the answer. This is probably the right place where we might consider additional designs for tackling hallucination issues.

###### Output rails example

Here is an example of an alogirthm we might utilise. 
The fallback strategy could involve calling another or multiple LLMs. The Guardrails would evaluate these answers to select the best one that meets quality standards. This approach increases the likelihood of obtaining a satisfactory response.

The complexity might be increased or decreased depending on the metrics we obtain for the baseline, but this is something we need to keep in mind while choosing the framework in advance.

*** Algorithm ***

**Input:** Request from user
**Output:** Response to user

1. **Primary Answer Generation**
    1.1 `main_answer` ← obtain answer from main process

2. **Guardrails Evaluation**
    2.1 `guardrail_result` ← evaluate `main_answer` with Guardrails
    2.2 If `guardrail_result` is satisfactory:
        2.2.1 Return `main_answer` to user
    2.3 Else:
        2.3.1 `time_remaining` ← check remaining response time
        2.3.2 If `time_remaining` is sufficient to invoke fallback model:
            2.3.2.1 `fallback_answer` ← obtain answer from fallback pipeline
            2.3.2.2 `fallback_guardrail_result` ← evaluate `fallback_answer` with Guardrails
            2.3.2.3 If `fallback_guardrail_result` is satisfactory:
                2.3.2.3.1 Return `fallback_answer` to user
            2.3.2.4 Else:
                2.3.2.4.1 Return `override_response` to user
        2.3.3 Else:
            2.3.3.1 Return `override_response` to user

**End Algorithm**


##### Locating the components

###### Embedder

** Granularity of embeddings**

There are multiple options for embeddings granularity, i.e., a vector could represent:
- a document `[level 0]`
- an article `[level 1]`
- a paragraph `[level 2]`
- a sentence `[level 3]`

Surely enough, we could cover all the layers by having separate embeddings for each layer and deciding which one to use based on the context, for example:
- search without specifying a particular document -> `[level 0]`
- user selects a document and asks if there is a particular section in it -> `[level 1]`
... 

This approach might bring high accuracy, but it's complex and costly to implement. For the baseline solution, we would like to start with a single embedding representation. Based on the most common use case, this would be a paragraph encoding. Because according to our analysis, most of the problems' answers could be found given the context of a single paragraph.

**Embedder: design choice**

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
| **Integration**         | High: APIs, databases, guardrails,  etc.         | Average: APIs, data sources, guardrails         |
| **Tooling**             | Debugging, monitoring, optimization              | Debugging, monitoring                           |
| **LLM Flexibility**     | Supports various LLMs (local/APIs)               | Supports various LLMs (local/APIs)              |
| **Indexing**            | No primary focus on indexing                     | Core feature, creates indices for data          |
| **Query Interface**     | Complex workflows                                | Straightforward                                 |
| **Optimization**        | Optimization of LLM applications                 | Optimized for the retrieval of relevant data    |
| **Ease of Use**         | Challenging                                      | Easy                                            |

Given the pros and cons listed above, it appears that LlamaIndex provides all the features we are looking for, combined with an ease of use that could reduce development and maintenance costs. Additionally, LlamaIndex offers enterprise cloud versions of the platform. If our solution evolves towards a simpler design, we might want to move to the paid cloud version if it makes economical sense.


### **VI. Error analysis**

**TODO**: update this part after the project'll start and data will be shared.

Given the multi-step nature of the solution, consider potential isuues on each of the steps:

**0. Intent classification**
- “under” filtering: Irrelevant questions are treated as meaningful, pipeline’s running for  nothing
- “over” filtering: Filtering out relevant questions ⇒ the response won’t be provided

**1. Embeddings**
- Poor Quality Embeddings: If the embeddings do not accurately capture the semantic meaning of the input text, the entire pipeline is compromised.
- Embedding Drift: The embeddings may become less effective over time due to adding docs from specific domain / terminology.

**2. Retrieval**
- Ineffective Retrieval Algorithm: If the retrieval mechanism fails to fetch relevant documents, even the best embeddings won't help.
- Outdated Index: The new versions of documents may not be indexed, which could lead to providing irrelevant/out-of-date information.

**3. Generation:**
- Model Hallucination: The generative model might produce  incorrect fabricated information, which looks believable.
- Lack of Context Understanding: The model might fail to catch a relevant information from context and instead of providing clarification question generate incomplete responses.

**4. Guardrails**
- “under” filtering:  filtering out inappropriate or harmful content might fail, leading to problematic outputs.
- “over” filtering: guardrails might filter out correct, useful information, reducing the system's performance.


As errors are inharitated from one stage to another. Use following approaches to diagnose them:

1. *Isolate Components:*
    - Test each component individually. For embeddings, manually inspect a sample of embeddings to check their quality. For retrieval, evaluate the retrieved documents separately from the generation step. Use metrics from Section 2.
2. *Step-by-Step Analysis:*
    - Follow a query through the entire pipeline to catch where the first major difference from expected behaviour occurs.

Corner-cases to check are mentioned in section 4. Validation Schema.


### **VII. Training Pipeline**

### **i. Overview**

We are planning to use external/pretrained solution for Embedding generation, OCR and LLM components.
Because of this, our Training Pipeline should focused on:
- **Stable Data Preprocessing.** Should be executed regularly upon new document being submitted. 
- **Stable Context Selection.** Enabling robust Prompt Engineering.

### **ii. Toolset**

The suggested tools are:
- Python
- Cloud Vector DB service (Pinecone, Azure AI Search, etc.)
- On-premise out-of-the-box OCR / Cloud OCR service
- Docker
- Cloud LLM service (OpenAI / Azure OpenAI)

### **iii. Data Preprocessing**

The data preprocessing should include:

- **Text Recognition.** OCR module to convert image documents into text representation.
- **Text Metadata Extraction.** Store explicit information and statistics about documents.
- **Feature Engineering.** Extract required features on different levels.
- **Preprocessing Metadata Storage.** Store explicit information about tools and their versions used for preprocessing.
- **Feature Storage.** Make features accessible and searchable.

The main goal - document should be preprocessed withint 1-2 hours after submition/new version being assigned.

### **iv. Evaluations**
For Context, Prompt, and Chat E2E evaluations we would be using RAGAS tool as described in [**IV. Validation Schema**](#iv-validation-schema)

### **v. Continuous Integration and Deployment**

The pipeline should be integrated into the existing CI/CD infrastructure. This includes setting up automated evaluation on a regular basis, ensuring that the latest data is used and pulled, and releasing changes to production with minimal manual intervention.

### **vi. Monitoring and Maintenance**

We should monitor the model's performance in production and set up alerts for significant deviations from expected performance. This will enable us to catch issues early and trigger retraining or model updates when necessary. [**XI. Monitoring**](#xi-monitoring)

### **vii. Future Work and Experimentation**

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
        - System Prompt

On the high level, all features could be classified into:
- Document level
- Text level
- Token level
- Prompt templates

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

**ix. Prompt templates**

Set of Prompt templates which would be able to cover different question types and intents.
Each Prompt template would consist out of following component templates (not ordered):
- Agent Role & Knowledge
- Agent Task
- Output Formating
- Output Restrictions
- Input Metadata Context
- Input Document Context
- Input Documents Relations 
- (Optional) Task Knowledge Context
- (Optional) Task & Role Examples

Some component templates would be pre-created. 
Some - constructed on the go from selected relevent documents according to the component template.

In the beggining component templates should be able to cover following conditions (from [**Expectations**](#iii-expectations)):
- General domain questions
- Addressable questions
- Non-addressable questions
- Questions for 1 Document
- Questions for multiple different Documents
- Questions for 1 Document in multiple historical versions
- Questions for multiple different Documents in their multiple historical versions 


### **IX. Measuring and reporting**

#### i. Measuring Results

Understanding how to measure the system's performance precisely is essential for validating the effectiveness of the machine learning solution. Experiments must be well-designed to capture meaningful metrics that reflect real-world utility.

In the **V. Baseline Solution** section, we highlighted several reasonable approaches, each with unique advantages and drawbacks. The challenge lies in determining which approach is most suitable for specific scenarios, particularly when the trade-offs impact performance unpredictably.

**Previous work**

The **Sparse Encoded Retrieval Baseline** serves as a straightforward search engine. While functional, it presents several limitations that new methodologies aim to overcome:

- No semantic understanding: synonyms are not supported by default
- Bag-of-words approach: word order is not considered
- Requires updates to accommodate new vocabulary

**Evaluation approach**

Evaluating the relevance of responses to user queries can be challenging. For this purpose, we could use a crowdsourcing platform. Assessors will be provided with a series of prompts and answers not only to assess relevance but also to detect hallucinations. We consider the following metrics:

- **Average Relevance Score** of the direct questions.
- **Average Relevance Score** of following-up questions.
- **Hallucination Rate**: This new metric quantifies the percentage of responses that contain hallucinated content. Responses are considered hallucinated if they include information not supported by facts or the input prompt.

**Assessment Method**:
- Assessors will be provided with a series of prompts and answers to evaluate both their relevance and accuracy. Alongside the 5-point scale for relevance, assessors will use a binary scale (Yes/No) to indicate whether each response contains hallucinated information.
- For nuanced analysis, we can further categorize hallucinations by severity, with minor inaccuracies noted separately from outright fabrications.

**Platform and Settings**:

- **Platform Choice**: Yandex.Toloka or Amazon Mechanical Turk, etc.
- **Total Assessors:** 100
- **Query-Answer Pairs for Direct Questions:** 500
- **Query-Answer Pairs for Follow-up Questions:** 500

**Terminology**:
 - **Task**: Defined as one Query-Answer Pair, which is a single item for assessment.
 - **Pool**: Described as a page with multiple tasks for assessors to evaluate.
 - **Overlap**: Indicates how many different assessors evaluate the same task, ensuring accurate data by having multiple reviewers.

**Cost Calculation**:

- **Pool Price:** $0.05
- **Total Tasks**: 1000 (500 for direct questions and 500 for following-up questions)
- **Tasks Per Pool:** 5
- **Overlap:** 5

**Expense Formula**: `expense = pool_price * (total_tasks / tasks_per_pool) * overlap`

- Cost of direct questions: $25
- Cost of following-up questions: $25
- Total Cost with Hallucination Assessment: $50

**Budget Adjustments**:

The settings can be adapted based on the budget, with potential increases to accommodate the additional complexity of assessing hallucinations.

**Special Considerations for Niche Domains**: The evaluation approach works well for well-known domains. For specific domains, we can use local experts who are familiar with the context.

#### ii. A/B Tests

**Hypothesis**

 - **Primary Hypothesis**: Based on offline metrics and evaluation with a crowdsourcing platform, we expect to improve **Average Relevance Score**.
 - **Secondary Hypothesis**: The system will deliver responses within an average of 1 minute, supporting efficient user interaction without sacrificing quality or accuracy.

**Termination Criteria.** 

 - The system must deliver responses within an average of 1.5 minutes. 
 - The percentage of reports with offensive or improper responses must be below 1%.
 
 If the termination criteria are met, the experiment will be paused and resumed after corrections.

**Key Metrics**

- **Average Relevance Score**: Calculates the mean score of how relevant the answers provided by the system are to the queries (positive/negative feedback). 
- **Offensive or Improper Responses**: This metric tracks the rate at which the system produces inappropriate or offensive content (offesnive or improper responses reports). 

**Control metrics**

- **Time to Retrieve (TTR)**: Measures the average time taken by the system to fetch and display results after a query is submitted.
- **Average amount of clarification questions**: Tracks the average number of additional questions the system needs to ask users to clarify their initial queries.
- **Average time of dialogue**:  Measures the average duration of an interaction session between the user and the system. This includes the time from the initial query to the final response.

**Auxiliary metrics**

- Total Document Count
- Daily New Documents
- Total User Count
- New Users per Day
- Session Count per Day

**Splitting Strategy.** Users will be split into two groups by their IDs.

**Experiment Duration.** 

The experiment will last two weeks. After one week, groups will swap configurations to mitigate any biases introduced by variable user experiences and external factors.

**Statistical Criteria.** Statistical significance will be determined using Welch’s t-test, with a significance level set at 5% and the type II error at 10%.

**Future Steps for Experiment Improvement.** 

To further validate our experimental setup, we propose incorporating an A/A testing phase to ensure the reliability of our measurements, followed by A/B/C testing to compare multiple new solutions simultaneously.

#### iii. Reporting Results

At the end of the experiment, a comprehensive report will be generated. This will include:

- Key, control and auxiliary metric results with a 95% confidence interval.
- Distribution plots showing metric trends over time.
- Absolute numbers for all collected data.
- Detailed descriptions of each tested approach with links to full documentation.
- Conclusive summary and recommendations for further steps.

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

**iii.i. Frameworks and Technologies**
1. **Frontend.** React.js: A well-known framework for building user interfaces with a modular, component-based architecture.
2. **Backend.** Redux or Context API: for managing application state.
3. **Backend integration.** Axios: for handling API requests and responses.
4. **Real-Time Interaction.** Socket.io: For real-time, bi-directional communication between web clients and servers.
5. **Styling.** CSS-in-JS libraries like styled-components or Emotion for component-based styling.

**iii.ii. Features**
  1. Clients can upload new documents, which automatically triggers embedding generation and storage.
  2. Consists of a positive/negative feedback on answers.
  3. Clients can report offesnive or unproper responses, which triggers another LLM as a fallback scenario.
  4. Allows to save a chat history and responses for future reference.

### **iv. OCR**

MagicSharepoint utilizes Optical Character Recognition technology to convert text from image-based documents into machine-readable text. This offers users greater flexibility in input data formats. The algorithm takes an intermediate place between the user upload process and embeddings creation.

MagicSharepoint leverages an existing OCR solution - AWS Textract, considering the complexitity and high-accuracy it requires. This choice also facilitates scalability, reducing development time and maintenance overhead.

**Features.**
1. **Document upload.** The system automatically identifies the format of uploaded file and OCR gets triggered if image-based inputs.
2. **Engine.** A cloud solution is used to be scalable enough to handle large volumes of documents simultaneously.
3. **Storage.** Both the original image and the extracted text is stored in the Documents Storage, linked by the same Document ID.
4. **Multi-language Support.** The OCR engine supports multiple languages to serve wide range of clients.

### **v. Backend API Design**

Below are provided events, when a corresponding API action gets triggered, while interacting with a user.

**Documents Management.**
- Upload a new document
- Delete a document or version
- Retrieve document metadata
- Retrieve all versions of a document
- Retrieve a specific version of a document
- Apply OCR technology for image-basede documents, if required

**User Queries Management.**
- Retrieve a query result
- Rate a query response
- Report an inappropriate response

**Embeddings Management.**
- **Model level.** Generate embeddings for a new document - uses a pre-trained model and sends to the IO level component for storage.
- **IO level.** Update embeddings for a document version change, keeping previous embiddings, corresponding to the same session id. 

**Chat Session Management.**
- Start a new chat session
- End a session
- Retrieve chat history
- Save chat history

**Notifications and Alerts API:**
- Inform users of system updates, document changes they worked with
- Real-time notifications for document processing status, e.g. "Your document is being uploaded. Please wait..." or "OCR processing started for your document".
- Error Handling and Logging Events. Examples are "File upload failed due to network timeout", "Document size exceeds limit" or "Unsupported file format".

### **vi. Parallel processing**
To handle simultaneous queries and ensure document processing tasks do not slow down user interactions, we adopt a parallel processing strategy that separates asynchronous tasks (e.g., embedding generation) from synchronous tasks (e.g., real-time user interactions).

- **Embedding Generation for Index Updating.** Asynchronous tasks, which are placed in a asynchronous task queue  (e.g., Celery with RabbitMQ) and from there are taken for parallel processing. It is triggered upon Document upload, new version or OCR completion. Processed by general workers.
- **Real-time User Interaction.** Synchronous tasks, which are priotitised for low latency. Proccessed by preserve workers for entire chat sessions to maintain context and improve response relevance.

### **vii. SLAs**

In order to control system performance and meeting defined standards, the MagicSharepoint service is integrated with a monitoring tool.

Key validated compontents
- **Response Time.** Guarantee first token response within 1 minute.
- **Uptime.** Ensure a high availability rate, aiming to 99.9% uptime.

**Time Estimates per Stage.**

1. User Query Processing.

- Intent Classification: 200-300ms
- Context Retrieval from Vector Database: 300-500ms
- Response Generation by LLM:
- Network latency to the vendor LLM: 100-200ms (depending on vendor and location)
- Token Generation: 200-500ms (varies based on response length)
- Total Estimated Time for User Query Processing: 800-1500ms

2. Document Processing.
   
- Document Upload and OCR (if required): 1-2 minutes
- Embedding Generation:
- Text Processing and Embedding Creation: 1-3 minutes (depending on document size)
- Database Update: 200-300ms
- Total Estimated Time for Document Processing: 2-5 minutes

**Infrastructure Requirements for meeting SLAs.**
- CPU Nodes: For general API handling, metadata storage and other small task.
- GPU Nodes: For intensive tasks such as embedding generation.
- Fast SSDs: For quick read/write operations during document processing and storing original files.
- High-speed Network: To ensure low latency between API services.

### **viii. Fallback Strategies**

Fallbacks are crucial for maintaining operational efficiency in the face of unforeseen circumstances. MagicSharepoint uses a multi-tiered fallback system to ensure seamless service:

- **Primary fallback.** The primary model is served by the chosen vendor. It is used unless negative user feedback on the model outcome and if the latency out of the accepted range.
- **Secondary fallback.** Our next layer of fallback involves using a pretrained LLM from Hugging Face, installed locally. This approach addressed to adress both potential issues.
  
The system has latency and feedback based switchings, which reroutes requests to the secondary model. Once conditions are improved, it switches to the primary model. To simplify management, each service within MagicSharepoint handles its fallback mechanisms independently.

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
