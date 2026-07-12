# Techie Bot

---

## **1\. General information**

This project involves the development and implementation of a chatbot assistant for handling technical queries related to the operation and maintenance of solar power plant installations. The chatbot will be integrated into Telegram and designed to provide quick and accurate access to organizational and technical documentation for company employees. The primary objective is to streamline the retrieval of relevant information from a database of normative and technical documents, making it easier for employees to find answers to their queries efficiently. 

## **2\. Relevance\&Reasons**

2.1. Problem Definition

The current method of accessing technical documentation for the operation and maintenance of solar power plant installations is cumbersome and inefficient. Employees face significant challenges in quickly retrieving relevant information, leading to delays in maintenance procedures and increased downtime. The lack of an intuitive and rapid access system means that employees often have to manually search through extensive documentation, which is time-consuming and prone to errors.

The absence of a centralized, intelligent system to handle queries and provide precise information from the technical documents exacerbates the problem. This inefficiency not only affects the productivity of the staff but also has the potential to impact the overall performance and reliability of the solar power installations.

2.2.1 Goals

To address these challenges, the development of an intelligent chatbot system aims to streamline the process of accessing technical documentation. The primary goals of this project are:

1. Provide Relevant Answers: The chatbot should deliver accurate and relevant responses based on the information from the technical documentation. This includes understanding and processing user queries in natural language and retrieving pertinent data quickly.

2. Link to Source Material: Each response from the chatbot should include links to the specific sections of the documentation (or directly provide the page ) from which the information was retrieved. This ensures that users can verify the information and consult the original documents for further details.

3. Generate Responses in Russian: The chatbot must be capable of generating responses in Russian, catering to the primary language of the company's employees and ensuring clear and effective communication.

2.2.1 Anti-goals  
To avoid unnecessary work and clearly define what needs to be done, 

1. Processing Large Amounts of Data: There is no requirement to process large volumes of data.  
     
2. Non-Technical Queries Handling: The chatbot will not address non-technical queries or engage in general conversations. Its primary focus is strictly on technical documentation and related queries.

3. User Authentication and Authorization Management: The chatbot will not include user authentication and authorization mechanisms. These aspects should either be managed by existing systems or considered unnecessary due to the small number of users from a single group, and integrated separately as needed.  
     
4. Scalability Requirements: The chatbot is not intended to be scalable beyond its initial user group. Its design focuses on serving a small, specific user base without plans for significant scaling.

2.3. Risks

Despite the clear benefits, there are several risks associated with the implementation of this system:

1. Accuracy of Responses: If the chatbot provides incorrect or incomplete information, it could lead to improper maintenance actions, potentially damaging equipment or causing safety hazards.  
2. System Reliability: Any downtime or unavailability of the chatbot system could revert the employees back to the inefficient manual processes, negating the benefits of the system.Nevertheless, a fallback system is planned with a rollback to the previous version \- baseline.  
3. Model Limitations: The models used (Mistral-nemo) the model has 12b parameters, perhaps its reasoning and choice may be incorrect.

2.4. Cost of Error

The cost of errors in this system can be significant:

1. Operational Downtime: Incorrect information leading to improper maintenance can cause extended downtime, resulting in lost energy production and revenue.  
2. Equipment Damage: Errors in the chatbot’s responses could lead to improper handling of equipment, causing damage that might require expensive repairs or replacements.  
3. Safety Hazards: Inaccurate information could result in safety incidents, endangering the lives of employees and leading to potential legal liabilities and reputational damage.

2.5. Other Considerations 

In the context of this project, we also consider the future scalability and adaptability of the system. While the initial deployment will focus on cloud-based infrastructure, there are plans to transition to a local setup within the company's premises, ensuring greater control and security over the system.

Additionally, the design and development of this chatbot system open up possibilities for its application in other areas or industries where quick access to technical documentation is crucial. This potential for broader use makes the project even more valuable and justified.

## **3\. Preliminary research**

3.1. Problem Decomposition

The problem of creating an intelligent chatbot for accessing technical documentation on solar power plant installations involves several layers of complexity. To manage this, the problem was decomposed into the following key components:

1. Natural Language Processing (NLP) Capabilities: Understanding and processing user queries in natural language is fundamental. This involves:  
* Query Understanding: The chatbot must accurately interpret the technical queries posed by the users.  
* Keyword and Phrase Recognition: Identifying key terms within queries to search the relevant documentation sections.  
2. Data Extraction and Preprocessing: Efficiently extracting and preparing data from technical documents for use by the chatbot, which includes:  
* Optical Character Recognition (OCR): Extracting text and images from scanned documents.  
* Data Structuring: Organizing extracted data into a structured format suitable for further processing.  
3. Vector Representation of Data: Converting textual and visual data into vector representations for efficient searching and retrieval:  
* Embedding Models: Using models like CLIP (as baseline model) to create unified vector spaces for text and images.  
* Vector Storage: Utilizing a vector database to store and manage these representations.  
* ColPali: converting each page to patches with multivector representations, which are then submitted to ColBERT for scoring (request, patches)  
4. Information Retrieval: Implementing a system to search and retrieve relevant information from the vector database:  
* Similarity Metrics: Defining metrics such as cosine similarity to compare query vectors with stored vectors.  
* Efficient Search Algorithms: Ensuring quick and accurate retrieval of the top relevant results.  
5. Response Generation: Formulating accurate and contextually appropriate responses based on retrieved information:  
* Text Generation Models: Employing models like Mistral-nemo-12B to generate responses.  
* Post-Processing: Refining the responses to include relevant links and additional information.  
6. Integration and Deployment: Integrating the chatbot with Telegram and deploying the system in a scalable and maintainable manner:  
* Telegram Bot Development: Using libraries like Telebot to create the chatbot interface.  
* Cloud Deployment: Initially deploying the system on a cloud platform with plans for future local deployment.

3.2. Research Findings

1. NLP Models: Model such as Mistral-nemo-12B was identified as suitable for understanding and generating technical responses in Russian, providing a balance between performance and computational requirements.  
2. OCR Technologies: Libraries like Tesseract and EasyOCR didn’t do so well for extracting text and images from various types of technical documents.Thus, hybrid ways of extracting information from PDF documents were created: Processing each page through ChatGPT-4 to obtain a brief content and description of page elements; an analog is processing pages through VLLM paligemma to obtain a multi-vector representation of each page.  
3. Vector Databases:For the Baseline FAISS was chosen for its efficiency in handling high-dimensional vector searches, supporting rapid and accurate information retrieval necessary for the chatbot’s operation.This is a very simple way to store and search by vectors  
4. Data Structuring and Preprocessing: The need for robust data preprocessing pipelines was evident to handle the diverse formats and types of technical documentation, ensuring that the data fed into the models is clean and well-structured.It is also necessary to make a mechanism like Knowledge-map or self-reasoning, for a more effective response of the model. The reason for this is the strong noise in the data  
5. Response Generation and Post-Processing: The importance of including metadata and links in responses was highlighted to enhance the usability and reliability of the chatbot, ensuring users can trace back the information to the original documents.  
6. VLLM:It is likely that visual models cope better with complex documents for analysis (paligemma from google)

3.3. Key Challenges

1. Accurate Query Interpretation: Ensuring the chatbot accurately understands and processes complex technical queries remains a primary challenge.  
2. Efficient Data Extraction: Dealing with diverse document formats and ensuring high accuracy in OCR and data structuring is critical.  
3. Effective Vector Search: Maintaining efficiency and accuracy in vector searches, especially as the volume of data grows.  
4. Contextual Response Generation: Generating responses that are not only accurate but also contextually appropriate and useful, including relevant links and metadata.  
5. Scalability and Deployment: Ensuring the system is scalable and can be seamlessly deployed initially on the cloud and later locally within the company.

## **3\. Metrics and losses**

In the context of this project, the evaluation of the system's performance is crucial to ensure that the chatbot delivers accurate, relevant, and useful responses to user queries. While traditional loss functions are not directly applicable due to the in-context learning approach of the generative model, the evaluation of the system's effectiveness relies heavily on well-defined metrics. These metrics provide a structured way to measure the quality and reliability of the responses generated by the model, ensuring that the system meets the necessary standards.

3.1. In-Context Learning

The generative model in this project employs in-context learning, where the model is "trained" during the generation process by being provided with the most relevant chunks of information related to the user's query. This approach negates the need for a traditional loss function, as the model's performance is not fine-tuned through backpropagation but instead depends on the quality of the provided context.

3.2. Evaluation Metrics

To systematically evaluate the performance of the system, we employ the RAGAS framework, which is specifically designed for automated scoring of Retrieval-Augmented Generation (RAG) models. RAGAS enables a comprehensive evaluation by analyzing both the quality of information extraction and the quality of text generation. The key metrics used in this framework are as follows:

1. Faithfulness  
- Description: Faithfulness measures how accurately the responses generated by the model reflect the information contained in the retrieved data sources. This metric is essential to ensure that the model does not distort or alter the information when presenting it to the user. High faithfulness is critical for maintaining the integrity of the information, particularly in a technical domain where accuracy is paramount.

2. Answer Relevancy  
- Description: This metric evaluates how well the generated answers correspond to the questions asked by the user. It assesses whether the response is not only correct but also directly addresses the user's query.

3. Context Recall and Context Precision  
- Context Recall:  
- Description: Context recall measures how well each sentence in the response can be attributed to the provided context. It evaluates how much of the relevant information from the context is utilized in the model’s response. The final score for context recall is calculated as the ratio of true positives (TP) to the sum of true positives and false negatives (TP / (TP \+ FN)). This metric ensures that the response is comprehensive and leverages the full breadth of the available information.  
- Importance: High context recall indicates that the model effectively uses the available information to form a comprehensive answer.  
- Context Precision:  
- Description: Assesses how accurately and appropriately the contextual information is applied in the response.  
- Importance: High context precision ensures that the model is selective and relevant in the information it uses, avoiding unnecessary or irrelevant details.  

4. Answer Semantic Similarity  
- Description: This metric measures the semantic closeness of the model's response to a reference answer. It helps evaluate how naturally and accurately the model can reproduce human-like language in its responses. Semantic similarity is important for ensuring that the chatbot's responses are both natural and easy to understand, closely mirroring the way a human expert might respond.

5. Answer Correctness  
- Description: This is a direct assessment of the correctness of the information provided in the response. It checks whether the model delivers accurate and reliable information.Ensuring answer correctness is the final and most critical check, as it determines whether the system is delivering valid and trustworthy information to the user.

	  
Link to the official documentation on metrics: [https://docs.ragas.io/en/stable/concepts/metrics/index.html\#ragas-metrics](https://docs.ragas.io/en/stable/concepts/metrics/index.html\#ragas-metrics)

3.3 Online and Offline Metrics

Online Metrics:

- User Feedback: Employee feedback will be collected continuously to gauge satisfaction with the chatbot’s performance in real-world scenarios. This feedback loop will help identify any issues with relevance, clarity, or accuracy in the responses provided by the chatbot.

Offline Metrics:

- RAGAS Framework: The primary offline metric will be the RAGAS framework, which provides automated and detailed scoring across the various dimensions of the chatbot’s performance. This includes faithfulness, answer relevancy, context recall and precision, semantic similarity, and answer correctness.

## **4\. Dataset😎**

This block will be a kind of history of how the pipeline was developed to process the source data. There will be two parallel approaches that have been consistently implemented in the project.

4.1. Source Files

The initial dataset comprises four extensive operation manuals (only for MVP). Each manual contains detailed instructions, technical specifications, safety precautions, and maintenance guidelines. The documents are rich in visual content, including numerous tables and diagrams, which are essential for understanding the technical information. These features presented unique challenges in the data processing pipeline, particularly in terms of extracting accurate textual and tabular data for use in the RAG model.

4.2. Data Processing: OCR Challenges and Solutions

Given the complexity of the source documents, traditional OCR (Optical Character Recognition) methods proved inappropriate (as it was said earlier). Both Tesseract and EasyOCR, popular open-source OCR tools, were tested extensively but failed to deliver the necessary accuracy, especially in parsing tables and complex diagrams.

To address these limitations, a custom OCR solution was developed.

In each section below, we will present our two different approaches to processing pdf files.This way it will be possible to understand the specifics of each stage in comparison of the two approaches. The first one reflects the most basic and naive RAG. The second reflects an approach using the ColPali architecture.  
Link to the repository with the source code of this architecture:[https://github.com/illuin-tech/colpali](https://github.com/illuin-tech/colpali)

1. This approach involved page-by-page parsing of the documents, followed by the use of a multimodal large language model (LLM), specifically ChatGPT-4, to process and understand the extracted content. Each piece of information extracted by this method was compressed into a chunk (size about 300 tokens) using the RecursiveTextSplitter from LangChain, leveraging the tokenizer from ChatGPT-3.5 Turbo. Each chunk was then annotated with metadata, including the source document, chunk number, and the page from which the chunk was extracted.  
2. Another approach also involves page-by-page parsing of the documents and saving this result in files for future usage in prompt. In addition, it is also necessary to process each page, but presented as an image divided into patches (32x32 per page)

4.3. Embedding and Vectorization

After the text was extracted and split into chunks, each chunk underwent further processing:

1. Using the CLIP (Contrastive Language–Image Pre-training) model. This model was employed to generate embeddings with a dimensionality of 512\. The entire dataset consisted of 1,963 chunks, each embedding representing a unique portion of the extracted information. Additionally, images were parsed separately from each document. These images were also wrapped with metadata, ensuring that they could be attached to responses when relevant.  
2. Each patch from the page goes through VLLM \- paligemma with a retrained adapter, which outputs a multi-vector representation for each patch.  
     
   

4.4. Loading into the Vector Database

1. A crucial step in the pipeline was managing the embeddings generated by CLIP. Given the manageable size of the dataset, the decision was made to use FAISS (Facebook AI Similarity Search) for fast and efficient retrieval in the vector space. FAISS was chosen for its ability to create a simple index that can be further trained and clustered on the dataset, optimizing retrieval for this specific application.  
2. Since the multi-vector representation already contains information both textual and various other elements of the patch, only grams are enough. We load these vectors into the pickle format and save them.  
     
   

4.5. Retrieval and Prompt Engineering

1. When a user submits a query, the system performs a search for the most relevant top-k chunks (in our case, k=3) using cosine similarity within the FAISS index. The retrieved chunk IDs are then used to extract the corresponding chunks and metadata from the text database. These chunks, along with the user’s query, are fed into a prompt template designed to generate accurate and contextually relevant answers.  
2. All we need to do is score correctly. To do this, the user's request is scored with each page’s patch embedding using the ColBERT architecture. At the output, we get a vector, the maximum value of which is the most relevant page. We take the Top-10 pages and find the corresponding chunk or txt file by indexes, which was created in paragraph 4.2. 

4.6. Post-processing

This section applies only to the second approach using the ColPali architecture as a retriever

After receiving the Top-10 pages, we submit these chunks to a special prompt, in which LLM (in our case, Mistral-nemo via its 128k contextual window ) will have to select the top 2 chunks that best fit the answer. And only then these 2 chunks are fed into the final prompt to generate a response. This will help reduce the noise that may be in the found chunks pages.

**5\. Baseline Solution**

Typically, in RAG systems, a Constant Baseline refers to the implementation of the most basic, naive version of the system. However, given the complexity and novelty of our approach, the baseline in our case is defined differently.

5.1. Constant Baseline

For our project, the Constant Baseline represents the simplest structural implementation of the RAG system. This version, which has already been implemented, involves the straightforward retrieval of the top-k relevant chunks based on cosine similarity without any additional preprocessing or post-processing steps. The selected chunks are directly fed into the generative model to produce an answer. This basic RAG model serves as our structural baseline, but it demonstrates only average metric performance.

This naive RAG implementation has several limitations:  
\- Limited Preprocessing: It does not include any advanced preprocessing, such as refining the retrieved chunks or filtering irrelevant information.  
\- No Self-Reflective Mechanism: The model does not incorporate any reasoning or self-reflective mechanisms to refine its responses, which can result in less accurate and less contextually relevant answers.

5.2. Advanced Constant Baseline

As the next step, we are working on an Advanced Constant Baseline that introduces a fundamentally different approach to the retrieval process. This approach leverages a more sophisticated architecture called ColPali, which combines a visual large language model (VLLM) named Paligemma-3b-mix with the ColBERT architecture.

In this advanced setup:  
\- Page-Level Processing: Each PDF file is first split into individual pages, which are then converted into images using the Pillow library.  
\- Patch-Based Embedding: Each page image is divided into patches (32x32 per page), and these patches are transformed into a multi-vector space using the Paligemma model. The output is a tensor of shape (1024,128), where 128 represents the embedding dimensionality.  
\- Advanced Scoring: ColPali uses the ColBERT architecture to perform a fine-grained scoring across all patches, identifying the top-k most relevant page indices based on their content.

This advanced retrieval process significantly improves the relevance and accuracy of the retrieved chunks by focusing on the most relevant visual and textual information at a granular level. The introduction of a multi-vector space and a sophisticated scoring mechanism allows for more precise retrieval of relevant information, improving the overall quality of the generated responses.

5.3 Specific Baselines

The Specific Baselines in our project are focused on incorporating more advanced techniques, such as self-reflective mechanisms and reasoning, into the RAG system. While this is a future goal beyond the current MVP stage, it is worth noting that adding these capabilities could significantly enhance the system's performance.

For example, integrating a self-reflective mechanism would enable the model to critically evaluate its own responses and refine them based on feedback loops, ensuring higher accuracy and contextual relevance. This could involve implementing a secondary processing stage where the model assesses the faithfulness and correctness of its initial output before presenting it to the user.

5.4. Current Status and Future Directions

At the current MVP stage, our system demonstrates its potential but is constrained by the budgetary limitations of cloud resources, where we utilize rented CPU and RAM. We are currently using the Mistral-Nemo generative model through the Mistral API, which processes pre-parsed text files derived from GPT-4 Vision. While this approach works for now, it is not ideal for scaling, as it does not fully leverage multimodal capabilities.

The next significant step after the MVP will be to implement a local version of the project that supports inference with a multimodal LLM, such as LLaVa. This would allow the model to handle pairs of prompts and images directly, enabling more accurate and contextually enriched responses. Moving to a local setup will also provide greater control over resources and the ability to scale the project more effectively.
